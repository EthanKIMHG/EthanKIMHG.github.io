# Charon Core Workflow 정리 (Duty → Scheduler → Fetcher → UnsignedData)

```json
Core Workflow

      Phases │ Components                              │ External
─────────────┴─────────────────────────────────────────┴──────────────────────

             │                ┌─────────┐
  *Schedule* │          ┌─────◆Scheduler│
        duty │          |     └─&┌──────┘
                        |        │
             │          |     ┌──▼────┐
             │  ┌──----------─┤Fetcher│
             │  │       |     └─&┌────┘
    *Decide* │  |       |        │
        what │  |       |     ┌──▼──────┐
        data │  |       |     │Consensus│
          to │  |       |     └─*┌──────┘
        sign │  |       |        ├────────────┐
             │  |       |     ┌──▼───┐     ┌──▼───┐    │
             │  |       |     │DutyDB│     │Signer├────│─► RS
             │  |       |     └──◆───┘     └──┬───┘    │ Remote signer
                |       |        │            │        │
      *Sign* │  |       |     ┌──┴─┐          │        │
        duty │  |       └----─┤VAPI◄───────────────────│── VC
        data │  |             └──┬─┘          │        │ Query, sign, submit
                |                ├────────────┘        │
     *Share* │  | ┌────────┐  ┌──▼─────┐
     partial │  | │ParSigEx◄──►ParSigDB│
        sigs │  | └─────*──┘  └──┬─────┘
                |                │
 *Aggregate* │  │ ┌────────┐  ┌──▼───┐
     partial │  └─◆AggSigDB◄──┤SigAgg│
        sigs │    └────────┘  └──┬───┘
                                 │
 *Broadcast* │                ┌──▼──────┐
  aggregated │                │Broadcast│
         sig │                └─&───────┘

       &: Beacon-node client calls
       *: P2P protocol
       ▼: Pushed data (in direction of the arrow)
       ◆: Pulled data (returned from the diamond)
```

## 개요

이 문서는 Charon의 Core Workflow 중에서도 다음 흐름을 중심으로 설명한다.

- Duty
- Scheduler
- Fetcher
- UnsignedData

또한, 이해에 핵심이 되는 **DV(Distributed Validator) 관점과 클러스터 관점의 차이**를 함께 포함한다.

---

## 전체 흐름 요약

Charon의 Core Workflow는 다음 목적을 가진다.

> 클러스터 전체가 동일한 duty를 동일한 데이터로 수행하도록 보장
> 

이를 위해 workflow는 다음 단계로 구성된다.

1. 수행할 작업 정의 (Duty)
2. 작업 시점 및 대상 결정 (Scheduler)
3. 서명 대상 데이터 획득 (Fetcher)
4. 클러스터 간 데이터 합의 (Consensus)
5. 부분 서명 및 집계

본 문서는 1~3단계까지를 다룬다.

---

# 먼저 정리: DV 관점과 클러스터 관점의 차이

이 부분이 가장 중요하다.

## DV 관점

DV(Distributed Validator)는 **논리적인 validator 1개**를 뜻한다.

예를 들어 32 ETH 하나가 하나의 validator라면, 그것이 DV 1개다.

즉:

- DV = 논리적 validator
- 하나의 루트 validator public key를 가짐
- 실제로는 여러 노드가 그 validator를 공동 운영함

문서 표현으로는 `PubKey`가 DV를 식별하는 키다.

---

## 클러스터 관점

클러스터는 **여러 개의 charon 노드와 여러 개의 DV를 포함한 전체 운영 단위**다.

즉:

- 클러스터 안에는 여러 개의 DV가 있을 수 있음
- 클러스터 안에는 여러 개의 Charon node가 있음
- 각 Charon node는 여러 DV에 대한 co-validator 역할을 수행함

문서에서 중요한 표현이 있다:

> Duty는 DV 레벨이 아니라 클러스터 레벨의 unit of work다.
> 

이 말의 의미는:

- 어떤 슬롯에서 attestation duty가 생겼다고 해도
- 그걸 DV마다 완전히 따로따로 workflow로 돌리는 게 아니라
- **같은 슬롯, 같은 duty type에 해당하는 여러 DV를 묶어서**
- 클러스터 차원에서 한 번의 workflow 단위로 처리한다는 뜻이다

이게 중요한 이유는 성능 때문이다.

DV가 많아질수록 validator별로 완전히 분리해서 처리하면 비효율적이기 때문에,

특히 **consensus 단계에서 batch 처리**를 할 수 있도록 설계한 것이다.

| 구분 | DV 관점 | 클러스터 관점 |
| --- | --- | --- |
| 단위 | 개별 validator | 전체 시스템 |
| 처리 방식 | validator별 독립 처리 | batch 처리 |
| workflow 단위 | 없음 | Duty |

---

# 1단계: Duty가 무엇인가

## 정의

문서에서 `Duty`는 core workflow의 **unit of work**, 즉 **작업 단위**다.

Duty는 두 가지로 정의된다:

- `Slot`
- `Type`

즉 개념적으로는:

- **언제 수행하는가** → slot
- **무슨 작업인가** → duty type

예를 들어:

- slot 100의 attestation
- slot 200의 proposer
- slot 300의 randao

이런 식이다.

---

## DutyType 예시

문서에는 여러 duty type이 나온다:

- `DutyProposer`
- `DutyAttester`
- `DutySignature`
- `DutyExit`
- `DutyBuilderRegistration`
- `DutyRandao`
- `DutyPrepareAggregator`
- `DutyAggregator`
- `DutySyncMessage`
- `DutySyncContribution`
- 등등

하지만 핵심적으로 생각하면:

- 블록 제안
- attestation 생성
- randao 관련 서명
- aggregator 관련 작업

처럼 **이더리움 validator가 슬롯/에폭 단위로 해야 하는 책임들**을 Charon 내부의 추상 작업 단위로 표현한 것이다.

---

## Duty가 왜 추상화되어 있나

실제 validator duty는 종류가 여러 가지다.

하지만 문서는 이 모든 duty가 결국 비슷한 흐름을 따른다고 본다:

- duty를 스케줄링하고
- 필요한 데이터를 가져오고
- 합의하고
- 서명하고
- partial sig를 모으고
- aggregate하고
- broadcast한다

즉 duty 종류는 달라도, **workflow 골격은 동일**하므로

`Duty`라는 추상 개념으로 통합한 것이다.

---

# 2단계: Scheduler가 무엇을 하는가

## 한 줄 요약

Scheduler는 “어떤 DV가 어떤 slot에서 어떤 duty를 수행해야 하는지 판단하고, 적절한 시점에 workflow를 시작하는 컴포넌트”다.

즉 workflow의 시작점이다.

---

## Scheduler의 핵심 역할 1: 활성 DV 식별

클러스터에는 여러 DV가 들어있을 수 있지만,

그 모든 DV가 항상 실제 beacon chain에서 활성 상태인 것은 아니다.

그래서 Scheduler는 먼저 cluster-lock에 있는 validator 목록을 보고,

각 DV의 루트 public key(`PubKey`)를 기준으로 beacon node에 질의해서:

- validator가 존재하는지
- 현재 active 상태인지

를 확인한다.

활성 아니면 스킵한다.

즉 Scheduler는 단순 타이머가 아니라,

**현재 클러스터에서 실제로 duty를 수행해야 할 DV 집합을 먼저 정리**한다.

---

## Scheduler의 핵심 역할 2: 다음 epoch duty 정의를 미리 가져옴

Scheduler는 beacon API를 이용해서 다음 epoch에 필요한 duty들을 미리 조회한다.

문서 기준으로 예시:

- attester duties 조회
- proposer duties 조회

이 결과는 아직 “실제 서명할 데이터”는 아니다.

그 대신:

**“어떤 validator가, 어떤 slot에, 어떤 duty를 수행하게 될지”에 대한 정의 정보**

다.

문서에서는 이것을 `DutyDefinition`이라고 부른다.

즉 Scheduler가 가져오는 것은:

- attestation 데이터 자체가 아니라
- **attestation을 해야 한다는 스케줄 정보 / 정의 정보**
- block 자체가 아니라
- **누가 proposer인지에 대한 정의 정보**

이다.

---

## DutyDefinition이 왜 필요한가

문서에서는 ETH2 spec 쪽에서 이런 것도 “duty”라고 부르지만,

Charon 내부에서는 혼동을 피하려고 이것을 `DutyDefinition`이라고 구분한다.

즉:

- `Duty` = workflow의 작업 단위
- `DutyDefinition` = 그 작업을 수행하기 위해 scheduler가 미리 확보한 정의/파라미터 정보

예를 들어 attester duty라면:

- 어떤 validator가
- 어떤 slot에
- 어떤 committee 관련 duty를 갖는지

같은 정보가 `AttesterDefinition` 형태로 들어간다.

proposer라면 `ProposerDefinition`이 된다.

---

## DutyDefinitionSet: 왜 Set인가

클러스터에는 여러 DV가 있으므로,

같은 slot에 같은 종류의 duty를 수행해야 하는 DV가 여러 개일 수 있다.

예를 들어:

- slot 123에서 attester duty를 수행해야 하는 DV가 50개일 수 있다

그러면 Scheduler는 이를 개별적으로 하나씩 넘기지 않고,

`PubKey -> DutyDefinition` 형태의 맵으로 묶어서 전달한다.

이게 `DutyDefinitionSet`이다.

즉:

- key: DV의 루트 public key
- value: 해당 DV의 duty definition

이 구조를 쓰는 이유는,

**클러스터 레벨의 duty 안에서 여러 DV를 함께 처리할 수 있게 하기 위해서**다.

---

## Scheduler의 핵심 역할 3: slot 시작 시 fetcher 호출

Scheduler는 duty definition을 미리 캐시해두고,

실제 해당 slot이 시작되면 그때 workflow를 트리거한다.

즉 흐름은:

1. 다음 epoch의 duty definition들을 미리 받아둠
2. 캐시에 저장
3. 해당 slot 도달
4. 해당 duty 시작
5. Fetcher 호출

즉 Scheduler는 단순 조회 컴포넌트가 아니라

**“미리 계획하고, 정확한 시점에 시작시키는 orchestration 역할”**을 한다.

---

# 3단계: Fetcher가 무엇을 하는가

## 한 줄 요약

Fetcher는 **해당 duty를 실제로 수행하기 위해 서명 대상이 되는 원본 데이터(UnsignedData)를 beacon node 등에서 가져오는 역할**을 한다.

Scheduler가 “무슨 일을 해야 하는지”를 정했다면,

Fetcher는 “그 일을 위해 정확히 무엇에 서명해야 하는지”에 가까운 데이터를 가져온다.

---

## 입력: Duty + DutyDefinitionSet

Fetcher는 Scheduler로부터 다음을 받는다:

- `Duty`
- `DutyDefinitionSet`

즉:

- 지금 수행할 작업 종류와 slot
- 그리고 그 작업을 해야 하는 DV 목록 및 각 DV의 duty definition

이 정보를 바탕으로 각 DV별 필요한 unsigned data를 가져온다.

---

## 예시 1: DutyAttester일 때

`DutyAttester`라면 Fetcher는 beacon node에서

**AttestationData**를 가져온다.

여기서 중요한 건, 아직 서명되지 않은 상태라는 점이다.

즉 validator가 나중에 서명하게 될 **원본 attestation payload**라고 보면 된다.

---

## 예시 2: DutyProposer일 때

`DutyProposer`는 조금 더 복잡하다.

문서 기준으로는:

1. 먼저 AggSigDB에서 이전에 집계된 `randao_reveal` 서명을 가져오고
2. 그것을 입력으로 사용해서 beacon node에서 block proposal 데이터를 가져온다

즉 proposer duty는 block을 바로 가져오는 게 아니라,

그 전에 필요한 randao 관련 집계 서명이 있어야 한다.

그래서 Fetcher는 단순 REST 호출만 하는 게 아니라,

필요한 선행 signed data도 참조할 수 있게 되어 있다.

---

## Fetcher가 만드는 결과: UnsignedData

Fetcher가 가져오는 결과를 문서는 `UnsignedData`라고 부른다.

이건 duty type에 따라 달라진다.

예를 들면:

- attester duty면 `AttestationData`
- proposer duty면 `BeaconBlock`

즉 `UnsignedData`는

“아직 서명되지 않았지만, 나중에 validator가 서명하게 될 duty data”를 추상화한 타입이다.

---

# 4단계: UnsignedData와 UnsignedDataSet이 무엇인가

## UnsignedData

UnsignedData는 서명 전의 duty data다.

중요한 포인트는:

- duty마다 구체 타입은 다를 수 있음
- 하지만 workflow 입장에서는 “서명 전 데이터”라는 공통 성격을 가짐

그래서 공통 추상 타입으로 다룬다.

이렇게 하면 attestation, block proposal 등 서로 다른 duty도

동일한 파이프라인에서 처리할 수 있다.

---

## UnsignedDataSet

클러스터에는 여러 DV가 있으므로,

한 duty 실행 시 여러 DV에 대한 unsigned data가 동시에 생길 수 있다.

그래서 Fetcher는 결과를 `UnsignedDataSet`으로 만든다.

형태는 개념적으로:

- key: `PubKey`
- value: `UnsignedData`

즉:

- DV A의 unsigned attestation data
- DV B의 unsigned attestation data
- DV C의 unsigned attestation data

이런 식으로 묶여 있다.

---

## 왜 proposer는 보통 하나뿐인가

문서에 따르면 `DutyProposer`는 slot당 유일하다.

즉 같은 slot에서 proposer duty는 보통 한 DV만 담당한다.

그래서 proposer의 `UnsignedDataSet`은 보통 entry가 하나뿐이다.

반면 attester duty는 같은 slot에 여러 DV가 동시에 attester일 수 있으므로

`UnsignedDataSet` 안에 여러 entry가 들어갈 수 있다.

---

# 5단계: 왜 Fetcher 결과를 바로 서명하지 않고 Consensus로 넘기나

이게 Charon 구조의 핵심이다.

문서가 매우 중요하게 말하는 부분은:

**beacon node가 같은 slot에 대해 반환하는 unsigned data는 결정적이지 않을 수 있다.**

즉:

- 시간에 따라 달라질 수 있고
- 어떤 beacon node에 물어보느냐에 따라 달라질 수 있다

그래서 서로 다른 charon node가 각자 fetch하면,

같은 duty인데도 서로 다른 `UnsignedDataSet`을 가져올 수 있다.

이 상태에서 각자 자기 VC에 서명시키면:

- 서로 다른 메시지에 서명하는 꼴이 되고
- 슬래싱 위험이 생긴다

그래서 Fetcher가 가져온 값은 **최종 정답이 아니라 제안값(proposal)** 이다.

즉 흐름은:

1. Scheduler가 duty 시작
2. Fetcher가 unsigned data를 가져옴
3. 이 값은 아직 로컬 노드의 “제안안”
4. 이를 Consensus에 넘김
5. 클러스터 전체가 동일한 unsigned data set에 합의
6. 그 뒤에만 서명 진행

즉 `UnsignedData`는 Fetcher 산출물인 동시에

**Consensus 입력값**이기도 하다.

# 단계별 전체 흐름 요약

이제 질문한 범위만 일렬로 다시 정리해보면 이렇다.

## 1. 클러스터에 여러 DV가 존재한다

각 DV는 루트 public key(`PubKey`)로 식별된다.

중요한 건 Charon은 이것을 개별 validator 수준만이 아니라

**클러스터 전체 운영 단위**로 본다는 점이다.

---

## 2. Scheduler가 활성 DV를 확인한다

cluster-lock의 DV 목록을 기반으로 beacon node에 질의해서:

- validator가 실제 존재하는지
- active 상태인지

를 확인한다.

활성이 아닌 DV는 이후 workflow 대상에서 제외된다.

---

## 3. Scheduler가 다음 epoch의 duty definition을 미리 조회한다

예를 들어:

- attester duties
- proposer duties

등을 미리 가져와서 캐시한다.

이 시점의 데이터는 아직 서명 대상 본문이 아니라

**어떤 duty를 해야 하는지에 대한 정의 정보**다.

이것이 `DutyDefinition`이며, 여러 DV 분은 `DutyDefinitionSet`으로 묶인다.

---

## 4. 특정 slot이 시작되면 Scheduler가 duty를 트리거한다

즉:

- “지금 slot 123 attester duty 시작”
- “이 duty에 해당하는 DV 목록은 이것들”

이라는 형태로 workflow를 시작한다.

이때 Fetcher가 호출된다.

---

## 5. Fetcher가 DutyDefinitionSet을 바탕으로 실제 서명 대상 원본 데이터를 가져온다

예를 들어:

- attester면 AttestationData
- proposer면 randao 관련 집계 서명을 참고한 뒤 BeaconBlock

을 가져온다.

이 데이터는 아직 서명되지 않았으므로 `UnsignedData`다.

---

## 6. 여러 DV의 결과를 UnsignedDataSet으로 묶는다

즉:

- DV1용 unsigned data
- DV2용 unsigned data
- DV3용 unsigned data

를 `PubKey` 기준으로 묶은 것이 `UnsignedDataSet`이다.

---

## 7. 하지만 이 값은 아직 최종 확정값이 아니다

왜냐하면 beacon node 응답은 비결정적일 수 있어서,

각 charon 노드가 서로 다른 unsigned data를 가져올 수 있기 때문이다.

그래서 Fetcher 결과는 바로 서명으로 가지 않고

Consensus에 proposal로 전달된다.

---

## 핵심 요약

- Duty는 클러스터 단위 작업이다
- Scheduler는 실행 시점과 대상 DV를 결정한다
- DutyDefinition은 사전 정의 정보다
- Fetcher는 실제 서명 대상 데이터를 가져온다
- UnsignedData는 서명 전 데이터다
- UnsignedDataSet은 여러 DV를 묶은 구조다
- Fetcher 결과는 반드시 consensus를 거쳐야 한다

---

## 한 줄 정리

> Charon은 "클러스터 전체가 동일한 데이터를 기반으로 동일한 duty를 수행하도록" 설계된 workflow를 가진다
>