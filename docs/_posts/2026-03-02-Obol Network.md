# Obol Network

# 기술 아키텍처

Obol 노드는 컴퓨터에 4개의 탑을 쌓는거랑 똑같음.

## Obol DV 노드 4층 구조

컴퓨터 한대에 이 4층 구조를 지키면서 돌아가야함.

| **레이어** | **이름** | **역할** |
| --- | --- | --- |
| **1층 (EL)** | **Execution Layer** | 이더리움 트랜잭션 처리 (Geth, Nethermind 등) |
| **2층 (CL)** | **Consensus Layer** | 이더리움 비콘 체인 합의 및 블록 관리 (Lighthouse, Prysm 등) |
| **3층 (Middleware)** | **Charon (Obol)** | **핵심 브레인.** 클러스터 노드 간 합의 및 데이터 중재 |
| **4층 (VC)** | **Validator Client** | 실제 키 조각으로 서명을 수행 (Lodestar, Teku 등) |
- 일반 스테이킹은 CL ↔ VC 가 직접 대화하지만 Obol 은 Charon (3층)이 있음
- VC 가 서명하기 전에 다른 노드들하고 합의하는데 필요.

# Obol 내부 알고리즘

Obol의 내부 알고리즘은 크게 "어떻게 합의하는가(Consensus)"와 "어떻게 서명하는가(Cryptography)"라는 두 가지 기둥으로 지탱됩니다.

직접 구축을 위해 소스 코드를 분석하시려는 분들이 반드시 이해해야 할 **QBFT 합의 알고리즘**과 **Threshold BLS 서명 알고리즘**의 핵심 원리를 깊이 있게 설명해 드릴게요.

---

## 1. 합의 알고리즘: QBFT (Quorum Byzantine Fault Tolerance)

Charon은 클러스터 노드들이 "이번 슬롯에 어떤 데이터를 서명할지" 결정하기 위해 **QBFT** 알고리즘을 사용합니다. 이는 이스탄불 BFT(IBFT)를 개선한 것으로, 노드 중 일부가 악의적으로 행동하거나 오프라인이어도 안전하게 합의를 이끌어냅니다.

### 3단계 합의 프로세스

1. **Pre-prepare (제안):** 라운드 리더(클러스터 내에서 순번제로 선정)가 서명할 블록/투표 데이터를 제안합니다.
2. **Prepare (준비):** 다른 노드들이 제안된 데이터를 검증하고, 문제가 없으면 "나도 준비됐다"는 메시지를 보냅니다.
3. **Commit (확정):** 전체 노드의 **2/3 초과(Quorum)**로부터 준비 메시지를 받으면, "이 데이터로 서명하자!"고 최종 확정합니다.

**수학적 안전성 (Threshold)**
Obol 클러스터의 전체 노드 수를 N, 허용 가능한 장애 노드 수를 F 라고 할 때, 다음과 같은 공식이 성립합니다.

$$
n \ge 3f + 1
$$

$$
Quorum(M) = \lceil \frac{2n}{3} \rceil
$$

4인 클러스터 N=4 라면

- $f = \lceil (4-1)/3 = 1 \rceil$ (최대 1명의 고장 /공격 허용)
- $M = \lceil (2 \times 4)/3 \rceil = 3$ (최소 3명이 합의해야 서명 가능)

<aside>
❓

각각의 노드들은 어떤 데이터를 보고 부분 서명하는걸까?

**증명(Attestation) 데이터**

가장 빈번하게 서명하는 데이터로, 현재 네트워크 상태에 대한 '투표 용지'입니다.

- **Slot:** 투표하는 현재 시간(슬롯 번호).
- **Index:** 내가 속한 위원회 번호.
- **BeaconBlockRoot:** 내가 "정상"이라고 생각하는 최신 블록의 해시값.
- **Source & Target:** 체크포인트 정보 (네트워크의 최종 확정을 위한 이정표).

**블록 제안(Block Proposal) 데이터**

직접 블록을 만들 때 서명하는 데이터로, 훨씬 무겁습니다.

- **Transactions:** 블록에 담긴 모든 거래 목록.
- **State Root:** 거래 처리 후 변경된 이더리움 전체 상태의 결과값.
- **Parent Root:** 바로 이전 블록의 해시값.
- **RANDAO Reveal:** 다음 순번을 정하기 위한 무작위 값.
</aside>

컴퓨터는 위와 같은 텍스트 데이터를 그대로 서명하지 않습니다. SSZ(Simple Serialize)라는 특수한 방식으로 데이터를 직렬화한 후, 최종적으로 **32바이트의 해시값**으로 변환합니다. 이를 `SigningRoot`라고 부릅니다.

$$
SigningRoot = HashTreeRoot(Object) + Domain
$$

- **Object Root:** 위에 설명한 데이터(증명/블록)를 트리 구조로 쪼개서 만든 하나의 대표 해시값입니다.
- **Domain:** "이 서명이 어떤 네트워크(메인넷/테스트넷)의 어떤 용도(Attestation/Proposal)인가?"를 나타내는 고유 식별자입니다. (이게 있어야 다른 체인에서 내 서명을 재사용하는 '리플레이 공격'을 막을 수 있습니다.)

Obol의 **Charon** 노드들이 QBFT 합의를 할 때 서로 주고받는 데이터가 바로 이 `unsigned_data` (서명 전 SSZ 객체)입니다.

1. **동일성 검사:** 4명의 운영자가 각자 자신의 비콘 노드에서 데이터를 가져옵니다.
2. **비교:** Charon끼리 데이터를 교환하며 "너가 가져온 `BeaconBlockRoot`랑 내가 가져온 게 같아?"라고 묻습니다.
3. **합의:** 만약 한 명이라도 인터넷이 느려서 다른 블록 해시를 가져왔다면, QBFT 알고리즘은 임계값(3/4)이 일치할 때까지 기다리거나 다수결에 따라 올바른 데이터를 선택합니다.
4. **확정:** "좋아, 이 32바이트 해시값(`SigningRoot`)이 정답이다!"라고 합의가 끝나면, 그제야 각자의 키 조각으로 서명을 시작합니다.

## **2. 서명 알고리즘: Threshold BLS**

합의가 끝났다고 해서 바로 서명이 완료되는 것은 아닙니다. 각 노드는 자신의 키 조각 $sk_i$ 으로만 서명할 수 있기 때문입니다. 여기서 **임계값(Threshold) 암호학**이 작동합니다.

### **작동 메커니즘**

1. **부분 서명 생성 (Partial Signing):** 각 노드의 VC(Validator Client)가 자신의 키 조각을 사용해 부분 서명($\sigma$)을 만듭니다.
2. **서명 합산 (Aggregation):** Charon이 이 부분 서명들을 수집합니다.
3. **라그랑주 보간법 (Lagrange Interpolation):** 수학적으로 임계값(***M***) 이상의 부분 서명이 모이면, 이를 합산하여 전체 원본 키(***SK***)로 서명한 것과 동일한 최종 서명($\sigma$)을 복구해냅니다.

$$
\sigma = \sum_{i \in S} \lambda_i \sigma_i
$$

*(단, S는 부분 서명을 제출한 노드들의 집합,* $\lambda_i$*는 라그랑주 계수)*

**💡 핵심 가치:** 이 과정에서 **전체 개인키(*SK*)는 단 한 번도 어느 노드에서도 생성되거나 조립되지 않습니다.** 오직 결과물인 '서명 값'만 완성될 뿐입니다.

## 3. 이중 방어 알고리즘: Anti-Slashing Logic

Obol은 슬래싱(Slashing)을 막기 위해 **미들웨어 수준**과 **VC 수준**에서 이중으로 알고리즘을 가동합니다.

1. **Charon Middleware Layer:** * 내부 DB(DutyDB)에 이미 서명한 슬롯 번호를 기록합니다.
    - 동일한 슬롯에 대해 다른 데이터로 합의를 시도하는 로직을 알고리즘적으로 차단합니다.
2. **Validator Client Layer:** * Charon이 실수로 잘못된 데이터를 서명하라고 던져도, VC(Lighthouse, Prysm 등) 자체의 **Anti-slashing DB**가 "이 슬롯은 이미 투표했어!"라며 거부합니다.

# Charon 내부의 데이터 처리 흐름

Charon 내부에서는 매 초마다 다음과 같은 복잡한 과정이 일어납니다.

1. **Fetcher (가져오기):** 내 노드의 비콘 노드(CL)로부터 "이번에 서명해야 할 데이터(블록이나 투표)"를 가져옵니다.
2. **Consensus (QBFT 합의):** 내 Fetcher가 가져온 데이터와 다른 운영자들의 Charon이 가져온 데이터를 비교합니다. 이때 **QBFT(Quorum Byzantine Fault Tolerance)** 알고리즘을 사용하여 클러스터 전체가 "동일한 데이터"에 서명하기로 합의합니다.
3. **DutyDB 저장:** 합의된 데이터를 로컬 DB에 저장합니다.
4. **Sign (서명 요청):** Charon이 본인의 검증인 클라이언트(VC)에게 "자, 이제 이 데이터에 너의 키 조각으로 서명해"라고 요청합니다.
5. **Aggregation (합산):** 각 노드에서 생성된 **부분 서명(Partial Signatures)**들을 Charon이 다시 수집합니다. 임계값(예: 4명 중 3명)이 모이면 이를 **하나의 완전한 서명**으로 합칩니다.
6. **Broadcast (전파):** 완성된 서명을 이더리움 네트워크로 전송합니다.

## Charon 핵심 데이터 구조

**`duty`**

```go
type Duty struct {
		ID          string       // 임무 고유 ID
    Slot        uint64       // 이더리움 슬롯 번호
    Type        DutyType     // Attestation, Proposal, Aggregation 등
    Validator   PubKey       // 해당 임무를 수행할 검증인 공개키
    Status      DutyStatus   // 현재 단계 (Fetching, Consensing, Signing...)
}
```

## 내부 데이터 처리 파이프라인 (Go 로직 중심)

Charon의 `Runtime` 엔진은 다음과 같은 순서로 코드를 실행하며 클러스터 합의를 이끌어냅니다.

### **Step 1: Scheduler & Fetcher (가져오기)**

네트워크 시간에 맞춰 임무가 활성화되면 `Fetcher`가 비콘 노드(BN)에서 데이터를 가져옵니다.

```go
// Fetcher 단계 로직
func (f *Fetcher) FetchUnsignedData(duty Duty) (Data, error) {
    // 1. 비콘 노드로부터 아직 서명되지 않은 원본 데이터 요청
    data, err := f.beaconNode.GetUnsignedData(duty)
    
    // 2. 가져온 데이터를 로컬 DutyDB에 임시 저장
    f.dutyDB.SaveUnsignedData(duty.ID, data)
    return data, nil
}
```

### **Step 2: Consensus - QBFT (합의)**

**Obol의 핵심 기술입니다.** 내가 가져온 데이터와 다른 노드가 가져온 데이터가 일치하는지 확인합니다.

```go
// Consensus 단계 로직 (QBFT 알고리즘)
func (c *Consensus) ProposeAndAgree(duty Duty, myData Data) (AgreedData, error) {
    // 1. QBFT 알고리즘을 통해 클러스터 노드들과 통신 (P2P)
    // 2. 각자가 가져온 데이터의 Hash를 비교
    // 3. 임계값(M/N) 이상의 노드가 동일한 Hash에 동의하면 '합의 성공'
    agreedData, err := c.qbftEngine.RunRound(duty.ID, myData)
    return agreedData, err
}
```

### **Step 3: Validator API & Signing (서명)**

합의가 완료되면, 이제 각 운영자의 검증인 클라이언트(VC)가 서명을 할 차례입니다.

```go
// ValidatorAPI (VC와의 인터페이스)
func (api *ValidatorAPI) GetDutyToSign(validatorPubKey PubKey) Data {
    // VC가 "서명할 거 줘"라고 요청하면 (HTTP GET)
    // 합의(Consensus)가 완료된 데이터만 반환합니다.
    return api.dutyDB.GetAgreedData(validatorPubKey)
}

// VC가 서명을 해서 다시 Charon에게 제출하면 (HTTP POST)
func (api *ValidatorAPI) SubmitPartialSignature(sig Signature) {
    // 제출된 '부분 서명'을 PartialSignatureDB에 저장
    api.sigDB.SavePartialSignature(sig)
}
```

### **Step 4: Aggregator & Broadcast (합산 및 전파)**

마지막으로 흩어진 서명 조각들을 하나로 합쳐 네트워크에 던집니다.

```go
// Aggregator 단계 로직
func (a *Aggregator) AggregateAndBroadcast(duty Duty) error {
    // 1. DB에서 부분 서명들을 긁어모음
    partials := a.sigDB.GetPartials(duty.ID)
    
    // 2. 임계값이 모였다면 BLS Threshold Aggregation 수행
    // (여러 개의 조각 서명을 하나의 유효한 전체 서명으로 결합)
    finalSig := bls.Aggregate(partials)
    
    // 3. 완성된 서명 데이터를 비콘 노드로 최종 전송
    return a.beaconNode.SubmitSignedDuty(duty, finalSig)
}
```

| **단계** | **컴포넌트** | **하는 일** | **실패 시 결과** |
| --- | --- | --- | --- |
| **Input** | `Fetcher` | 비콘 노드에서 투표 용지(데이터) 가져오기 | `msgFetcherNoData` 에러 |
| **Process** | `QBFT Engine` | "이 용지가 맞지?"라고 파트너들과 합의 | `Consensus Timeout` (가동 중단) |
| **Action** | `Validator API` | 내 컴퓨터 안의 VC에게 서명 시키기 | `Unauthorized` 또는 `Missing Sig` |
| **Output** | `Aggregator` | 서명 조각들을 합쳐서 제출 | `Insufficient Signatures` |

코드를 뜯어보시다 보면 가장 많이 마주칠 문제는 **`Consensus` 단계의 지연**입니다.

- **원인:** 노드들 사이의 네트워크 핑(Ping)이 너무 멀거나, 특정 노드의 CPU가 100%를 쳐서 QBFT 합의에 늦게 반응하는 경우입니다.
- **해결:** `CHARON_LOG_LEVEL=debug` 설정을 켜고 로그를 보시면, 어느 단계(Step 1~4)에서 시간이 오래 걸리는지 밀리초(ms) 단위로 확인할 수 있습니다.

## 성공적인 합의 서명 및 서명로그 흐름 (Attesation)

검증 업무는 매 슬롯(12초)마다 발생하며, 로그는 크게 4단계로 흐릅니다.

```bash
# 1단계: 임무 감지 및 데이터 가져오기 (Fetcher)
INFO  fetcher  fetching unsigned data for duty  {"slot": 1240501, "type": "attestation", "validator": "0x8a2f..."}

# 2단계: 클러스터 간 QBFT 합의 시작 (Consensus)
DEBUG consensus  proposing data for consensus   {"slot": 1240501, "hash": "0xabc123..."}
DEBUG consensus  received quorum for proposal    {"slot": 1240501, "votes": 3, "threshold": 3}
INFO  consensus  agreed on data for duty        {"slot": 1240501, "type": "attestation", "hash": "0xabc123..."}

# 3단계: 내 로컬 VC가 서명 조각 제출 (Signing)
INFO  validator  received partial signature     {"slot": 1240501, "validator": "0x8a2f...", "origin": "local"}

# 4단계: 동료들의 서명 취합 및 최종 전파 (Aggregation & Broadcast)
DEBUG partials   waiting for more signatures    {"slot": 1240501, "received": 2, "required": 3}
INFO  partials   sufficient partial signatures  {"slot": 1240501, "count": 3}
INFO  broadcast  aggregated and broadcasted      {"slot": 1240501, "type": "attestation", "validator": "0x8a2f..."}
```

### 로그 핵심 포인트 분석

- **`slot`**: 현재 작업 중인 이더리움 시간 단위입니다. 모든 노드가 같은 슬롯 번호를 보고 있어야 합니다.
- **`hash`**: 앞서 설명해 드린 **SigningRoot**입니다. 클러스터 노드들이 이 해시값이 일치하는지 QBFT로 확인하는 것입니다.
- **`votes` / `threshold`**: 4명 중 3명이 동의했다는 뜻입니다. 이 숫자가 부족하면 합의가 멈춥니다.
- **`origin: local`**: 내 컴퓨터에 설치된 Validator Client(Lighthouse 등)가 성공적으로 서명 조각을 Charon에게 전달했다는 증거입니다.

## 블록 제안(Block Proposal) 시의 특별한 로그

블록 제안은 훨씬 중요하므로 로그에 **MEV-Boost** 관련 내용이 포함될 수 있습니다.

```bash
INFO  scheduler  upcoming proposer duty         {"slot": 1240510, "validator": "0x8a2f..."}
INFO  builder    fetching blinded block from relay {"relay": "ultrasound.money", "slot": 1240510}
INFO  consensus  agreed on block hash           {"slot": 1240510, "hash": "0xblock789..."}
INFO  broadcast  distributed block broadcasted   {"slot": 1240510, "block_hash": "0xblock789..."}
```

## 시나리오: 운영자 D의 서버 다운 (4인 클러스터)

운영자 D가 정전으로 인해 오프라인이 되더라도, 남은 A, B, C는 이더리움 네트워크에 정상적으로 투표를 제출해야 합니다. 이때 남은 노드들의 로그는 다음과 같이 변합니다.

### 1. 합의 단계 (Consensus): "어? 한 명이 대답 안 하네?"

평소에는 4명이 금방 합의하지만, 이제는 D의 응답을 기다리다 타임아웃이 발생한 후 남은 인원끼리 진행합니다.

```bash
# 운영자 A의 로그
DEBUG consensus  proposing data for consensus   {"slot": 1240600, "hash": "0xdef456..."}
WARN  consensus  peer is unreachable            {"peer": "Operator_D_ENR...", "error": "connection timeout"}
DEBUG consensus  received quorum for proposal    {"slot": 1240600, "votes": 3, "threshold": 3} 
INFO  consensus  agreed on data for duty        {"slot": 1240600, "type": "attestation", "hash": "0xdef456..."}
```

### 2. 서명 취합 단계 (Aggregation): "딱 맞게 모였다!"

이제 여유분(Extra) 서명이 없습니다. 정확히 3명이 모여야만 최종 서명이 완성됩니다.

```bash
# 운영자 A의 로그
INFO  validator  received partial signature     {"slot": 1240600, "origin": "local"}
DEBUG partials   waiting for more signatures    {"slot": 1240600, "received": 2, "required": 3}
# (잠시 후 운영자 B, C의 서명이 도착)
INFO  partials   sufficient partial signatures  {"slot": 1240600, "count": 3}
INFO  broadcast  aggregated and broadcasted      {"slot": 1240600, "type": "attestation"}
```

## 노드 D의 자동 재합류 3단계 과정

100% 자동으로 완벽하게 재합류함.

### 1단계: 피어 탐색 (Peer Discovery)

노드 D의 Charon이 켜지면 가장 먼저 하는 일은 **"내 동료들 어디 있지?"**라고 찾는 것입니다.

- 이미 구축 시 생성된 `cluster-lock.json` 파일 안에 동료 노드(A, B, C)의 **ENR(명함)** 정보가 들어있습니다.
- Charon은 이 정보를 바탕으로 P2P 네트워크를 통해 A, B, C에게 연결 요청을 보냅니다.

### 2단계: 상태 동기화 (State Syncing)

연결이 복구되면 노드 D는 그동안 놓쳤던 정보를 업데이트합니다.

- **"지금 몇 번 슬롯이야?"**, **"내가 없는 동안 어떤 합의가 있었어?"**를 동료들에게 묻습니다.
- Charon은 내부적으로 매우 가벼운 상태 동기화 알고리즘을 가지고 있어, 몇 초 내에 현재 네트워크 시간에 맞게 본인의 상태를 맞춥니다.

### 3단계: 합의 라운드 즉시 참여 (Active Participation)

상태가 맞춰지면, 다음으로 돌아오는 **증명(Attestation)**이나 **블록 제안(Proposal)** 임무부터 즉시 참여합니다.

- 이제 다시 4명 전원이 합의에 참여하게 되므로, 클러스터의 **여유분(Fault Tolerance)**이 다시 1로 복구됩니다.

# **분산 키 생성 (DKG)**

키를 어떻게 나누는가? 에 대한 답임.

- **No Central Dealer:** Obol은 누군가 키를 만들어 나눠주는 방식이 아닙니다. **DKG(Distributed Key Generation)**를 통해 노드들이 서로 암호화된 메시지를 주고받으며 **처음부터 조각난 상태로 키를 생성**합니다.
- **Threshold BLS:** 이더리움은 BLS 서명을 사용하는데, 이는 여러 개의 부분 서명을 산술적으로 합치면 전체 키로 서명한 것과 동일한 결과가 나오는 특성이 있습니다.

$$
N = 4 (전체 노드), M = 3 (임계값)
$$

## No Trusted Dealer

전통적인 방식은 한 명이 키를 생성한 뒤 칼로 자르듯 나누어 주는 방식(Trusted Dealer)입니다. 하지만 이 방식은 키를 처음 만든 사람이 키를 훔칠 수 있다는 치명적인 약점이 있습니다.

Obol의 DKG는 **Pedersen VSS(Verifiable Secret Sharing)** 알고리즘을 사용하여 이 문제를 해결합니다.

- **비밀의 탄생:** 4명의 운영자가 각자 자신만의 무작위 숫자(비밀 조각)를 생성합니다.
- **검증 가능성:** 내가 받은 조각이 상대방이 속임수 없이 보낸 것인지 수학적으로 검증(VSS)할 수 있습니다.
- **결과:** 모든 과정이 끝나면, 누구도 전체 개인키(SK)를 알지 못하지만, 조각들을 합치면 유효한 서명을 만들 수 있는 상태가 됩니다.

## 수학적 원리

각 노드는 $t -1$ 차 다항식 생성 (t 는 입계값, 4인 클러스터인 경우 3.)

$$
f_i(x) = a_{i,0} + a_{i,1}x + a_{i,2}x^2 \pmod p
$$

1. 공유 : 각 노드 $i$는 다른 노드 $j$에게 $f_{i}(j)$ 값을 암호화 하여 보냄
2. 합산 : 각 노드는 받은 값들을 모두 합쳐 자신의 최종 키 조각 $(s_{i})$ 를 가짐

$$
s_i = \sum_{j=1}^{n} f_j(i)
$$

1. 공개키 결정 : 전체 검증인 공개키 (PK)는 각 노드가 공개한 값들의 합으로 결정됨.

## DKG 진행 절차 (4인 베어메탈 기준)

베어메탈 서버 4대에 Charon이 설치되었다고 가정하고 다음 단계를 밟습니다.

### 1단계: ENR(Node Record) 수집

모든 운영자가 자신의 ENR 문자열을 생성하여 한 곳(메모장이나 슬랙 등)에 모읍니다.

Bash

`charon create enr # 각자 실행 후 생성된 enr:... 값을 공유`

### 2단계: DKG 정의 파일 생성 (Cluster Definition)

한 명의 리더가 `cluster-definition.json` 파일을 만듭니다. 여기에는 **4명의 ENR과 임계값(3), 입금 주소 등**이 포함됩니다. (보통 Obol Launchpad 웹사이트에서 시각적으로 진행합니다.)

### 3단계: DKG Ceremony 실행 (가장 중요)

이제 **모든 서버가 동시에** 이 명령어를 실행해야 합니다. 서로 P2P로 연결되어 암호화된 조각을 주고받아야 하기 때문입니다.

```bash
docker run --rm -v "$(pwd):/opt/charon" \
  obolnetwork/charon:latest create dkg \
  --definition-file=/opt/charon/cluster-definition.json
```

**이때 내부적으로 일어나는 일:**

- **P2P 연결:** 각 서버의 Charon이 설정된 IP/포트로 서로 연결을 시도합니다. (베어메탈 방화벽이 열려있어야 함)
- **Round 1:** 각자 다항식을 만들고 커밋먼트(Commitment)를 공유합니다.
- **Round 2:** 암호화된 키 조각(Shares)을 서로 교환합니다.
- **Round 3:** 받은 조각이 유효한지 검증하고 최종 **`cluster-lock.json`*과 **`validator_keys/`*를 생성합니다.

## DKG 성공 후 생성되는 결과물

DKG가 끝나면 각 서버의 `.charon` 폴더에는 다음이 생성됩니다.

1. **`cluster-lock.json`**: 클러스터의 '헌법'입니다. 누가 멤버인지, 임계값이 얼마인지, DKG가 성공했는지에 대한 증명이 담겨 있습니다. 모든 노드가 **동일한 내용**을 가집니다.
2. **`validator_keys/keystore-*.json`**: 이 파일이 진짜 내 **키 조각**입니다. 4명의 파일이 모두 다릅니다. (본인의 조각만 가짐)
3. **`deposit-data.json`**: 32 ETH를 입금할 때 사용하는 파일입니다.

# **장애 허용 능력 (Fault Tolerance)**

구축 시 노드 수를 결정하는 기준이 됩니다. Obol(DVT)은 일반적으로 다음과 같은 **BFT 보안 모델**을 따릅니다

$$
F = \lfloor \frac{N-1}{3} \rfloor
$$

*(N: 전체 노드 수, F: 허용 가능한 장애 노드 수)*

| **클러스터 크기 (N)** | **임계값 (M)** | **허용 장애 노드 (F)** | **비고** |
| --- | --- | --- | --- |
| **4** | **3** | **1** | 최소 권장 구성 (가장 대중적) |
| **7** | **5** | **2** | 높은 보안 수준 |
| **10** | **7** | **3** | 기관급 고가용성 구성 |

# 백업

## 백업해야 할 핵심 파일 리스트 (The Essential Assets)

서버가 통째로 타버려도 아래 파일들만 있으면 5분 만에 새 서버에서 복구가 가능합니다. 모든 파일은 `.charon` 폴더 내에 위치합니다.

| **파일명** | **중요도** | **역할** | **비고** |
| --- | --- | --- | --- |
| `charon-enr-private-key` | **최상** | 내 노드의 고유 신분증 (ENR 키) | 유출 시 내 노드 권한 탈취됨 |
| `cluster-lock.json` | **최상** | 클러스터 구성원 및 합의 규칙 지도 | 이 파일이 없으면 재합류 불가 |
| `validator_keys/` 폴더 | **최상** | 분산 생성된 **키 조각(Key Shares)** | **가장 중요한 암호학적 자산** |
| `deposit-data.json` | 상 | 최초 예치 증빙 데이터 | 복구 자체에는 필수는 아니나 보관 권장 |