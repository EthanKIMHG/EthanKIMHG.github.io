# Charon Core Workflow 정리2 (Consensus → DutyDB → ValidatorAPI → ParSig → SigAgg → Broadcast)

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

## 문서 목적

이 문서는 Charon Core Workflow의 후반부를 설명한다.

다음 컴포넌트를 중심으로 다룬다.

- Consensus
- DutyDB
- ValidatorAPI
- ParSigDB
- ParSigEx
- SigAgg
- AggSigDB
- Broadcast

이 문서의 목적은 단순 기능 나열이 아니라, 각 컴포넌트가 왜 필요한지, 어떤 입력과 출력을 가지는지, 슬래싱 방지와 클러스터 일관성을 위해 어떤 역할을 수행하는지까지 포함해 전체 흐름을 이해하는 것이다.

---

## 전체 흐름 개요

앞 단계에서 Scheduler와 Fetcher는 다음 결과를 만든다.

- 어떤 slot에 어떤 duty를 수행할지 결정
- 각 DV별 unsigned duty data를 조회
- 이를 UnsignedDataSet 형태로 구성

하지만 이 시점의 값은 아직 최종 확정값이 아니다.

Beacon node 응답은 시간과 노드에 따라 달라질 수 있으므로, 각 Charon 노드가 서로 다른 unsigned data를 가져올 수 있다.

따라서 후반부 workflow는 다음 문제를 해결하기 위해 존재한다.

- 클러스터 전체가 동일한 unsigned data에 합의
- 동일한 메시지에 대해서만 부분 서명 수행
- threshold 이상 partial signature 수집
- 최종 aggregate signature 생성
- beacon node로 안전하게 제출

이를 순서로 적으면 다음과 같다.

Consensus → DutyDB → ValidatorAPI → ParSigDB / ParSigEx → SigAgg → AggSigDB → Broadcast

---

## 1. Consensus

### 역할

Consensus는 클러스터 내 모든 Charon 노드가 동일한 duty input data에 합의하도록 만드는 컴포넌트다.

여기서 합의 대상은 블록체인의 블록이 아니라, 특정 duty에 대해 서명해야 할 UnsignedDataSet이다.

즉, Charon의 consensus는 다음 질문에 답한다.

- 이번 slot의 attestation data는 정확히 무엇인가
- 이번 proposer duty에서 서명해야 할 block body는 무엇인가
- 클러스터 전체가 어느 unsigned data를 정답으로 채택할 것인가

---

### 왜 필요한가

Consensus가 필요한 핵심 이유는 두 가지다.

첫째, BLS threshold signature aggregation은 같은 메시지에 대해 서명된 partial signature만 합칠 수 있다.

노드마다 서로 다른 메시지를 서명하면 aggregation이 성립하지 않는다.

둘째, 서로 다른 attestation이나 block을 같은 duty에 대해 제출하면 슬래싱 위험이 발생한다.

즉, consensus는 단순한 성능 최적화가 아니라 안전 장치다.

---

### 블록체인 consensus와의 차이

Charon의 consensus는 일반 블록체인 consensus와 닮았지만 목적이 다르다.

블록체인 consensus는 연속적인 블록 체인을 만들기 위해 이전 상태를 이어받으며 반복적으로 합의를 수행한다.

반면 DVT의 consensus는 다음 특징을 가진다.

- 각 duty마다 독립적인 단발성 consensus game
- 합의 대상은 transaction block이 아니라 UnsignedDataSet
- 각 slot / duty마다 별도의 isolated consensus 수행

즉, Charon은 “체인을 만드는 합의”가 아니라 “서명 입력 데이터를 통일하는 합의”를 한다고 보면 된다.

---

### 입력과 출력

입력

- Duty
- UnsignedDataSet proposal

출력

- 클러스터 합의가 끝난 최종 UnsignedDataSet

이 결과는 DutyDB에 저장된다.

---

### 동작 관점 정리

각 노드는 로컬 Fetcher가 생성한 UnsignedDataSet을 제안할 수 있다.

또한 다른 노드의 proposal도 수신한다.

이후 consensus protocol이 실행되며, 최종적으로 클러스터가 동일한 duty data를 선택한다.

핵심은 다음이다.

- 로컬 노드가 가져온 데이터가 곧 정답은 아님
- consensus가 끝난 결과만 정답
- 이후의 모든 signing path는 이 결과만 사용

---

## 2. DutyDB

### 역할

DutyDB는 consensus가 끝난 unsigned duty data를 저장하고, 이후 ValidatorAPI가 조회할 수 있게 제공하는 저장소다.

동시에 슬래싱 방지 보조 역할도 수행한다.

---

### 왜 필요한가

Validator client는 duty가 발생할 때 Charon의 Validator API에 질의한다.

이때 Charon은 아직 consensus가 끝나지 않았을 수도 있고, 이미 끝난 결과가 있을 수도 있다.

DutyDB는 이 중간 상태를 흡수한다.

- 합의가 끝난 결과를 저장
- VC가 요청했을 때 blocking query 제공
- 결과가 아직 없으면 기다렸다가 반환

즉 DutyDB는 consensus 결과와 validator client 요청 사이를 연결하는 안정화 계층이다.

---

### 저장 단위

UnsignedDataSet은 내부적으로 PubKey 기준으로 분리 저장된다.

이렇게 하는 이유는 각 DV별로 유일한 unsigned data를 보장하기 위해서다.

즉 개념적으로는 다음과 같다.

- 키: Slot + DutyType + PubKey
- 값: 해당 DV의 unsigned duty data

이 구조를 통해 특정 duty와 특정 DV 조합에 대해 하나의 정답만 허용할 수 있다.

---

### 슬래싱 방지 측면

DutyDB는 slot, duty type, DV 조합에 unique 성격을 부여해 동일 duty에 대해 서로 다른 unsigned data가 뒤늦게 저장되는 것을 방지하는 데 도움을 준다.

다만 이것만으로 슬래싱 방지가 완결되는 것은 아니다.

문서 관점에서도 consensus와 slashing DB는 함께 필요하다.

정리하면:

- Consensus: 클러스터가 같은 데이터를 선택하게 함
- DutyDB: 선택된 결과를 안정적으로 유지하고 중복/충돌을 줄임

---

### 조회 API의 의미

DutyDB는 blocking query를 제공한다.

이는 매우 중요하다.

왜냐하면 VC는 자기 타이밍에 attestation data나 block proposal을 요청하는데, 그 순간 Charon 내부 consensus가 아직 끝나지 않았을 수 있기 때문이다.

그래서 DutyDB는:

- 데이터가 있으면 즉시 반환
- 아직 없으면 대기
- timeout은 VC 쪽 정책에 따름

이 구조 덕분에 VC는 일반 beacon node API와 유사한 방식으로 Charon을 사용할 수 있다.

---

## 3. ValidatorAPI

### 역할

ValidatorAPI는 downstream validator clients에게 beacon-node API처럼 보이는 인터페이스를 제공하는 계층이다.

즉, VC 입장에서는 beacon node와 통신한다고 생각하지만, 실제로는 Charon ValidatorAPI를 통해 duty data를 받고 partial signature를 제출한다.

---

### 핵심 목적

ValidatorAPI의 목적은 크게 두 가지다.

- DutyDB에서 합의 완료된 unsigned data를 VC에 제공
- VC가 서명한 partial signed data를 ParSigDB로 전달

즉 후반부 workflow에서 ValidatorAPI는 “VC와 Charon core workflow의 접점”이다.

---

### 왜 별도 계층이 필요한가

VC는 일반적으로 beacon-node API 형태를 기대한다.

하지만 Charon 내부에는 다음과 같은 특수성이 있다.

- beacon node에서 바로 받은 데이터가 아니라 consensus 완료된 데이터만 제공해야 함
- VC가 가진 키는 threshold share 기반 키이며, beacon chain의 root validator key와 직접 대응되지 않을 수 있음
- duty에 따라 내부 mapping, verification, 비동기 aggregation 연계가 필요함

따라서 단순 프록시가 아니라 “프로토콜 적응 계층”이 필요하다. 그 역할이 ValidatorAPI다.

---

### 주요 동작 예시

### Attestation data 요청

VC가 attestation data를 요청하면 ValidatorAPI는 DutyDB에서 해당 slot, committee index에 맞는 합의 완료 attestation data를 조회해 반환한다.

즉 VC는 beacon node로부터 raw data를 받는 것이 아니라, 클러스터 consensus를 거친 데이터를 받는다.

---

### Block proposal 요청

VC가 block proposal을 요청하면 ValidatorAPI는 먼저 proposer duty에 대응하는 DV를 식별하고, randao reveal 관련 partial signature 흐름을 ParSigDB로 넘긴 뒤, 최종적으로 DutyDB에서 합의 완료 beacon block을 조회해 반환한다.

즉 proposer는 attester보다 더 복합적인 흐름을 가진다.

---

### Signed attestation / signed block 제출

VC는 unsigned data를 받아 서명한 뒤 ValidatorAPI에 제출한다.

ValidatorAPI는 이를 ParSignedData 형태로 변환해 ParSigDB에 저장한다.

여기서 중요한 건 VC가 반환한 값이 최종 aggregate signature가 아니라 “이 노드의 partial signature”라는 점이다.

---

### 키 매핑 문제

VC는 DKG 결과로 private key share를 사용한다.

따라서 VC가 내부적으로 인식하는 public key와 beacon chain 상의 DV root public key는 다를 수 있다.

ValidatorAPI는 이 차이를 흡수한다.

즉:

- 외부 beacon chain 기준 식별자
- 내부 VC share 기준 식별자

사이를 연결하는 mapping 계층 역할을 수행한다.

이 부분은 운영/디버깅에서 매우 중요하다.

왜냐하면 로그를 볼 때 “VC가 알고 있는 키”와 “클러스터가 대표하는 validator 키”를 혼동하면 문제 원인을 잘못 해석할 수 있기 때문이다.

---

## 4. ParSigDB

### 역할

ParSigDB는 partial signature를 저장하고 상태를 관리하는 데이터베이스다.

입력 source는 두 가지다.

- 로컬 VC에서 들어온 partial signature
- 다른 Charon peer가 전파한 partial signature

---

### 왜 필요한가

Threshold BLS 서명은 하나의 partial signature만으로는 완성되지 않는다.

여러 노드에서 동일 메시지에 대해 생성한 partial signature를 모아서 threshold를 만족해야 한다.

ParSigDB는 이 수집과 상태 전이를 관리한다.

---

### 관리하는 상태

문서 기준으로 partial signature는 다음 상태를 가질 수 있다.

- Internal: 로컬 VC로부터 막 수신됨
- Broadcasted: peer에 전파되었거나 peer로부터 수신됨
- Aggregated: threshold 충족 후 aggregation에 전달됨
- Expired: 너무 오래되어 더 이상 aggregation 대상이 아님

이 상태 관리가 필요한 이유는 단순 저장만으로는 다음을 보장할 수 없기 때문이다.

- 로컬 시그니처를 모든 peer에 전파했는가
- 이미 threshold를 만족했는가
- 늦게 도착한 시그니처를 어떻게 처리할 것인가
- 오래된 데이터를 언제 만료할 것인가

---

### 주요 역할 분리

ParSigDB는 크게 두 흐름을 담당한다.

### 내부 수신 처리

로컬 VC가 서명한 partial signature를 저장하고, 이를 peer에 전파할 대상으로 표시한다.

### 외부 수신 처리

다른 peer가 보낸 partial signature를 저장하고, threshold 충족 여부를 확인한다.

즉 ParSigDB는 로컬-외부 양쪽에서 들어오는 partial signature를 하나의 상태 머신 안에서 통합 관리한다.

---

### threshold 충족 시점

특정 Duty와 PubKey 조합에 대해 충분한 수의 partial signature가 모이면, ParSigDB는 SigAgg를 호출한다.

즉 ParSigDB는 “aggregation을 시작해도 되는가”를 판단하는 관문이다.

---

## 5. ParSigEx

### 역할

ParSigEx는 partial signature를 peer 간 직접 교환하는 컴포넌트다.

즉 로컬 노드가 받은 partial signature를 다른 모든 Charon peer에게 전파하고, 반대로 다른 peer의 partial signature를 수신해 ParSigDB에 저장하게 만든다.

---

### 왜 필요한가

각 노드는 자기 VC로부터 자기 share에 대한 partial signature만 얻을 수 있다.

최종 aggregate signature를 만들기 위해서는 다른 노드의 partial signature도 필요하다.

따라서 peer 간 partial signature 교환이 필수다.

---

### 통신 방식의 특징

문서 구조상 ParSigEx는 gossip-style pubsub이 아니라 direct p2p fan-out 방식을 사용한다.

즉:

- 각 노드가 다른 노드들과 직접 연결
- partial signature를 배치로 전송
- n² 형태의 네트워크 오버헤드
- 대신 지연시간 감소

운영 관점에서는 이것이 매우 중요하다.

클러스터 노드 수가 늘어날수록 direct exchange의 연결 수와 네트워크 제약을 반드시 고려해야 한다.

---

## 6. SigAgg

### 역할

SigAgg는 threshold 이상 수집된 partial signatures를 aggregate해서 최종 BLS signature를 생성하는 서비스다.

---

### 의미

여기서 비로소 workflow는 “부분 서명 수집”에서 “최종 서명 생성” 단계로 넘어간다.

즉:

- 입력: 동일 duty / 동일 DV / 동일 메시지에 대한 partial signatures
- 출력: beacon node 제출 가능 상태의 최종 SignedData

---

### 중요한 제약

SigAgg가 정상 동작하려면 partial signatures가 모두 동일 메시지에 대해 만들어졌어야 한다.

이 때문에 앞단의 Consensus가 반드시 필요했다.

Consensus 없이 서로 다른 unsigned data를 각 VC가 서명했다면, ParSigDB에 partial signature가 많이 모여도 aggregation은 성립하지 않는다.

즉 전체 구조는 다음 의존성을 가진다.

- Consensus가 맞아야
- DutyDB 데이터가 맞고
- VC가 같은 데이터에 서명하고
- ParSigDB가 올바른 partial signature를 모으고
- SigAgg가 성공한다

---

## 7. AggSigDB

### 역할

AggSigDB는 aggregate된 최종 signed duty data를 저장하는 데이터베이스다.

즉 core workflow의 최종 산출물 저장소다.

---

### 왜 필요한가

일부 duty는 이후 다른 duty의 입력으로 다시 사용될 수 있다.

대표적인 예가 randao reveal이다.

예를 들어 proposer duty에서 block proposal을 생성하려면 먼저 aggregated randao reveal이 필요하다.

이 값은 AggSigDB에서 조회될 수 있다.

따라서 AggSigDB는 단순 보관소가 아니라 후속 duty의 입력 저장소 역할도 한다.

---

## 8. Broadcast

### 역할

Broadcast는 aggregate된 signed duty data를 beacon node에 제출하는 마지막 단계다.

즉 Charon 내부 합의, 부분 서명 수집, threshold aggregation이 모두 끝난 뒤 최종 산출물을 외부 consensus layer로 내보내는 출구다.

---

### 의미

여기까지 오면 Charon core workflow는 완료된다.

정리하면 Broadcast는 다음 의미를 가진다.

- 클러스터 내부 합의 결과를 외부 beacon chain 행위로 변환
- aggregate signature 기반의 최종 validator 행위 실행
- attestation, block proposal 등 실제 validator duty 제출 완료

---

## 전체 데이터 흐름

### 1단계: Consensus

Fetcher가 생성한 UnsignedDataSet proposal들을 가지고 클러스터 전체가 동일한 duty data에 합의한다.

### 2단계: DutyDB 저장

합의 완료된 UnsignedDataSet을 DV별로 분리 저장한다.

### 3단계: ValidatorAPI 응답

VC 요청이 들어오면 DutyDB에서 합의 완료 데이터를 꺼내 unsigned duty data를 제공한다.

### 4단계: Partial Signature 수신

VC는 해당 데이터에 서명하고, ValidatorAPI는 이를 ParSignedData로 변환해 ParSigDB에 저장한다.

### 5단계: Partial Signature 교환

ParSigDB는 로컬 partial signature를 ParSigEx를 통해 peer들에 전파하고, peer들의 partial signature를 다시 저장한다.

### 6단계: Threshold 충족

ParSigDB는 동일 duty / DV에 대해 충분한 partial signatures가 모였는지 판단한다.

### 7단계: Aggregate Signature 생성

SigAgg가 partial signatures를 aggregate하여 최종 SignedData를 생성한다.

### 8단계: 결과 저장 및 제출

AggSigDB에 저장하고, Broadcast가 beacon node로 제출한다.

---

## 컴포넌트별 책임 분리 요약

### Consensus

클러스터 전체가 동일한 unsigned duty data를 선택하게 만든다.

### DutyDB

합의 완료된 unsigned data를 저장하고 VC에 안정적으로 제공한다.

### ValidatorAPI

VC와 Charon workflow 사이의 adapter 역할을 수행한다.

### ParSigDB

partial signature를 상태 기반으로 저장하고 threshold 충족 여부를 관리한다.

### ParSigEx

partial signature를 모든 peer에 교환한다.

### SigAgg

partial signatures를 aggregate하여 최종 BLS signature를 생성한다.

### AggSigDB

최종 aggregate signed data를 저장한다.

### Broadcast

최종 서명을 beacon node에 제출한다.

---

## 슬래싱 방지 관점 핵심 정리

슬래싱 방지는 단일 컴포넌트가 해결하지 않는다.

여러 계층이 함께 작동해야 한다.

- Fetcher 이후 바로 서명하지 않고 Consensus를 거친다
- DutyDB가 duty별 유일한 unsigned data를 유지한다
- VC는 DutyDB 기반 데이터에만 서명한다
- 동일 메시지 기반 partial signatures만 aggregation된다
- 최종적으로 하나의 aggregate result만 외부에 제출된다

즉 Charon의 후반부 workflow는 “서명을 잘 만드는 파이프라인”이 아니라, “같은 것을 서명하게 강제하는 안전 파이프라인”이라고 보는 것이 정확하다.

---

## 운영/디버깅 포인트

문제 분석 시 다음 순서로 보는 것이 좋다.

### Consensus 문제

노드 간 동일 unsigned data 합의 실패, timeout, peer quorum 문제

### DutyDB 문제

VC가 필요한 시점에 unsigned data를 받지 못함, blocking query timeout

### ValidatorAPI 문제

VC 요청과 내부 duty mapping 불일치, pubkey 매핑 혼선

### ParSigDB 문제

partial signature 저장은 되지만 threshold 미충족

### ParSigEx 문제

peer 간 partial signature 교환 실패, 네트워크 단절

### SigAgg 문제

partial signatures는 모였지만 aggregate 실패

### Broadcast 문제

aggregate signature는 생성됐으나 beacon node 제출 실패

이 순서로 보면 원인 추적이 훨씬 쉬워진다.

---

## 한 줄 결론

Charon 후반부 workflow는 다음을 보장하기 위한 구조다.

> 클러스터 전체가 동일한 unsigned data에 합의하고, 각 노드가 동일 메시지에 대해 partial signature를 생성하며, threshold aggregation을 거쳐 최종 signed duty data를 안전하게 beacon chain에 제출한다.
>