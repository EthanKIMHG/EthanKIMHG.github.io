# Ethan Kim

## Frontend / Web3 Engineer

보안, 인증, 배포, 운영까지 고려해 제품을 설계하는 **Frontend / Web3 Engineer**입니다.

React와 TypeScript 기반의 프론트엔드 개발을 중심으로, 인증 흐름, 서버 상태 관리, 성능 최적화, 폐쇄망/온프레미스 배포, 감사 대응이 필요한 운영 환경까지 함께 다뤄왔습니다.

DID 신원 인증 플랫폼에서는 Holder, Issuer, Admin, Audit Admin 서비스를 구축하며 인증/인가, 감사 로그, 데이터 시각화, 폐쇄망 배포, GS 인증 대응을 경험했습니다. 현재는 Obol DVT 기반 Ethereum Staking 운영 구조를 설계하며 validator 운영, key custody, Safe multisig, Charon runtime, approval/audit workflow까지 Web3 인프라 영역으로 확장하고 있습니다.

---

## Core Positioning

### 보안과 운영을 제품 구조로 풀어내는 프론트엔드 개발자

저는 UI 구현에서 끝나지 않고, 사용자가 실제 운영 환경에서 안정적으로 서비스를 사용할 수 있도록 **인증, 데이터 흐름, 배포 구조, 장애 대응, 감사 로그**까지 함께 고려합니다.

제가 강점을 가지는 영역은 다음과 같습니다.

| Area | Keywords |
| --- | --- |
| 인증/보안 UX | JWT, Refresh Token, HttpOnly Cookie, Silent Refresh, Session Security |
| 프론트엔드 아키텍처 | Multi-service Routing, Server State, Client State, ErrorBoundary |
| 배포/인프라 | Docker, Nginx, HTTPS, AWS EC2, On-premise, Closed Network |
| 성능 최적화 | Code Splitting, Gzip, Image Optimization, Bundle Analysis, Caching |
| Web3 운영 | Ethereum Staking, Obol DVT, DKG, Charon, Safe, OVM, Web3Signer/KMS |
| 감사/통제 | Audit Log, Approval Workflow, Evidence Package, Key Custody |

---

## Selected Projects

### 1. 블록체인 기반 DID 신원 인증 플랫폼

**Role**  
`Frontend Architecture / Security Flow / Deployment`

**Overview**  
Holder, Issuer, Admin, Audit Admin으로 구성된 DID 기반 신원 인증 플랫폼의 프론트엔드 구조를 설계하고 구현했습니다. 단순 화면 개발을 넘어 인증/인가 흐름, 감사 로그, 데이터 시각화, 폐쇄망 배포, GS 인증 대응까지 포함해 실제 운영 가능한 구조를 만드는 데 집중했습니다.

**Key Contributions**

- Holder / Issuer / Admin / Audit Admin 4개 서비스의 프론트엔드 아키텍처 설계 및 구축
- Access Token / Refresh Token 기반 인증 구조 설계
- Refresh Token을 HttpOnly Cookie로 관리하고, Access Token 만료 시 Silent Refresh 후 원 요청을 재시도하는 흐름 구현
- Audit Admin 단독 개발: 보안 로그 감사, 데이터 시각화, CSV 다운로드 기능 구현
- 총 89개 기능, 109개 input validation, 131개 error handling 구현
- RPS 1000 기준 에러율 0% 테스트 환경 구성
- Docker / Nginx 기반 온프레미스 및 폐쇄망 배포 구조 설계
- 현장 실사 및 협력사 인터페이스 테스트 대응

**Impact**

- 인증, 감사, 배포, 테스트가 필요한 실증/인증 환경에서 안정적으로 동작하는 프론트엔드 구조를 구축했습니다.
- 세션 만료, 인증 실패, 서버 오류, 잘못된 경로 접근을 일관된 UX로 처리했습니다.
- 외부 의존성이 제한된 폐쇄망 환경에서도 재현 가능한 배포 흐름을 만들었습니다.

**Tech Stack**  
React, TypeScript, Redux Toolkit, RTK Query, Axios Interceptor, React Router, Docker, Nginx, AWS EC2, HTTPS

---

### 2. ETH Staking / Obol DVT 운영 시스템

**Role**  
`Technical Owner / Architecture / Operations Design`

**Overview**  
Obol DVT 기반 Ethereum Staking 운영 구조를 설계하고 문서화했습니다. Validator 운영을 단순 노드 실행이 아니라 **DKG, key custody, Safe multisig, OVM 권한 구조, Charon runtime, Bare Metal 배포, approval/audit workflow**가 연결된 운영 시스템으로 바라보고 설계했습니다.

**Key Contributions**

- Obol DVT 기반 validator 운영 구조 분석 및 문서화
- DKG ceremony 절차와 산출물 민감도 분류
- `cluster-lock`, `deposit-data`, ENR, validator key share 등 runtime artifact와 secret artifact 구분
- Safe 3-of-4 multisig 기반 withdrawal ownership 구조 설계
- OVM contract role 분석 및 deposit/withdraw 운영 흐름 정리
- Bare Metal 4대 기준 runtime deployment automation 방향 설계
- allowlist 기반 artifact staging, preflight 검증, rollout 흐름 설계
- Charon unhealthy 원인 분석과 운영 runbook 작성

**Impact**

- Web3 validator 운영을 수동 서버 작업이 아니라 승인, 검증, 배포, 감사가 결합된 운영 체계로 정리했습니다.
- slash risk, custody risk, audit risk를 분리해 운영자가 따라갈 수 있는 문서와 절차로 전환했습니다.
- 복잡한 DVT 구조를 기술팀, 보안팀, 회계/감사 관점에서 설명 가능한 형태로 문서화했습니다.

**Tech / Domain**  
Ethereum Validator, Obol DVT, Charon, DKG, Threshold BLS, Safe Multisig, OVM, Web3Signer, KMS, Bare Metal, Docker

---

### 3. The Helia Duty — 산후조리원 운영 대시보드

**Role**  
`Frontend / Full-stack MVP Development`

**Overview**  
산후조리원 운영 스태프를 위한 근무, 객실, 입실 관리 대시보드 MVP를 구현했습니다. 달력형 보기, 엑셀형 보기, 객실 평면도 기반 보기, 스태프 등록 및 입실 이력 관리를 통해 운영자가 필요한 정보를 빠르게 확인하고 관리할 수 있도록 구성했습니다.

**Key Contributions**

- Next.js App Router 기반 운영 대시보드 구조 설계
- 로그인, 대시보드, 공유 가능한 스케줄 페이지, 스태프 등록 화면 구성
- Calendar View, Excel View, Room Floorplan View 등 운영 목적별 화면 전환 구현
- Supabase 기반 스키마 관리 및 운영 데이터 흐름 설계
- React Hook Form / Zod 기반 입력 검증 구조 적용
- TanStack Query 기반 서버 데이터 조회 및 캐싱 구조 적용
- Radix UI 기반 Dialog, Select, Popover, Tabs 등 운영툴 UI 구성

**Impact**

- 실제 운영자가 사용하는 스케줄/객실/스태프 데이터를 하나의 대시보드에서 확인할 수 있도록 구조화했습니다.
- 데이터 입력, 검증, 조회, 공유 페이지까지 연결된 운영 흐름을 설계했습니다.

**Tech Stack**  
Next.js, React, TypeScript, Supabase, TanStack Query, React Hook Form, Zod, Radix UI, Tailwind CSS

---

### 4. Tixxet — Web3 QR/NFC 티켓팅 MVP

**Role**  
`Product Architecture / Frontend / Web3 Integration`

**Overview**  
Web2 사용자도 쉽게 사용할 수 있는 Web3 이벤트 티켓팅 MVP를 설계했습니다. 사용자는 소셜 로그인과 내장 지갑을 통해 티켓을 발급받고, 운영자는 QR 스캔과 NFC 태그를 활용해 입장 처리 흐름을 관리할 수 있도록 구성했습니다.

**Key Contributions**

- 사용자 앱과 관리자 앱을 분리한 Turborepo monorepo 구조 설계
- Privy 기반 소셜 로그인 및 Embedded Wallet 흐름을 고려한 UX 설계
- QR 기반 티켓 발급, NFC 태그 쓰기/읽기, 게이트 입장 검증 흐름 정의
- Supabase tickets 테이블과 on-chain token id를 연결하는 데이터 모델 설계
- NFC 태그에는 raw data를 저장하지 않고 서버 API에서 AES256 암호화/복호화를 수행하는 보안 흐름 설계
- Host / Staff / Attendee 역할에 따라 다른 액션을 제공하는 운영자 UX 설계

**Impact**

- Web3 티켓팅의 복잡한 지갑, 민팅, 검증 흐름을 사용자가 이해하기 쉬운 QR/NFC 경험으로 전환했습니다.
- 사용자 앱, 관리자 앱, 스마트 컨트랙트, 데이터베이스, 공통 UI 패키지를 분리해 확장 가능한 구조를 설계했습니다.

**Tech Stack**  
Stackflow, Next.js, TypeScript, Turborepo, Privy, Account Abstraction, Wagmi, Viem, Ethers.js, Supabase, AES256, Web NFC API

---

### 5. The Helia — 프리미엄 산후조리원 웹사이트

**Role**  
`Frontend Development / UI System / Interaction`

**Overview**  
프리미엄 산후조리원 브랜드를 위한 정보 중심 웹사이트를 구현했습니다. 시설, 신생아 케어, 스파/웰니스, 예약 안내 등 사용자가 상담 전 확인해야 하는 정보를 명확한 섹션 흐름으로 구성하고, 브랜드의 차분하고 고급스러운 톤을 유지하는 디자인 시스템을 정리했습니다.

**Key Contributions**

- Next.js / TypeScript 기반 프로덕션 웹사이트 구현
- 브랜드 톤을 premium, calm, warm, conversion-oriented 방향으로 정의
- 객실, 케어, 스파, 예약 안내 등 예약 전 탐색에 필요한 정보 구조 설계
- 모바일 가독성을 고려한 카드형 섹션과 CTA 배치
- Framer Motion, GSAP, Lenis를 활용한 부드러운 인터랙션 구성
- Vercel Analytics / Speed Insights 기반 배포 후 사용자 경험 확인 구조 적용

**Impact**

- 사용자가 브랜드 정보를 빠르게 이해하고 상담/예약 행동으로 이어질 수 있도록 정보 구조와 전환 흐름을 설계했습니다.
- 단순 랜딩 페이지가 아니라 브랜드 톤, 콘텐츠 구조, 모바일 UX, 인터랙션을 함께 고려한 웹사이트로 구현했습니다.

**Tech Stack**  
Next.js, React, TypeScript, Tailwind CSS, Framer Motion, GSAP, Lenis, Vercel

---

## Technical Strengths

### 1. 인증/보안 UX를 운영 흐름까지 고려해 설계합니다

DID 기반 인증 플랫폼에서 단순 로그인 구현을 넘어 Access Token / Refresh Token의 저장 위치와 생명주기, 브라우저 쿠키 정책, HTTPS 환경, API Gateway 경로 불일치까지 함께 고려했습니다.

**What I did**

- Refresh Token은 HttpOnly Cookie로 관리하여 XSS 노출 위험을 낮추고, Access Token은 Redux/Zustand In-Memory로 관리하는 구조를 설계했습니다.
- Access Token 만료 시 사용자를 바로 로그아웃시키지 않고 Silent Refresh 후 원 요청을 자동 재시도하는 흐름을 구현했습니다.
- 다수의 API가 동시에 실패할 때 Refresh 요청이 폭주하지 않도록 Promise Singleton / Queueing / Mutex 패턴을 적용했습니다.
- 401, 404, 500 에러를 React Router ErrorBoundary와 연결해 인증 실패, 서버 오류, 잘못된 경로 접근을 선언적으로 처리했습니다.

**Keywords**  
JWT, HttpOnly Cookie, Silent Refresh, RTK Query, Axios Interceptor, ErrorBoundary, Session Security

---

### 2. 제약 있는 환경에서도 배포 가능한 구조를 만듭니다

폐쇄망, 온프레미스, AWS EC2 환경에서 Docker Multi-stage Build, Nginx path-based routing, HTTPS 구성을 통해 여러 프론트엔드 서비스를 안정적으로 배포했습니다.

**What I did**

- Holder, Issuer, Issuer Admin, Admin 등 4개 프론트엔드 프로젝트를 Docker Multi-stage Build로 빌드했습니다.
- 최종 산출물은 Node.js 런타임이 아니라 정적 파일만 포함된 경량 Nginx 이미지로 구성했습니다.
- 여러 서비스를 하나의 Nginx 컨테이너에서 `/holder`, `/issuer`, `/issuer-admin`, `/admin` path 기준으로 서빙했습니다.
- SPA 새로고침 시 404가 발생하지 않도록 각 path별 `try_files` 설정을 구성했습니다.
- 운영 서버에서 `docker load → docker compose up`으로 재현 가능한 배포 흐름을 만들었습니다.

**Keywords**  
Docker, Multi-stage Build, Nginx, Closed Network, Path-based Routing, SPA Routing, Immutable Infrastructure

---

### 3. 성능 최적화를 측정과 실험으로 접근합니다

빌드 최적화에서는 단순히 플러그인을 추가하는 데서 끝내지 않고, 번들 결과와 네트워크 요청 수, gzip 용량, 로드 시간, 빌드 시간을 비교했습니다.

**What I did**

- CRACO/Webpack 환경에서 `splitChunks`를 활용해 `react-vendor`, `ui-vendor`, `vendor` 청크를 분리했습니다.
- Vite/Rollup 환경에서는 `manualChunks`의 특성을 분석하고, Webpack과 다른 priority 처리 방식을 검증했습니다.
- Terser, CompressionPlugin, ImageMinimizerPlugin, vite-plugin-image-optimizer 등을 적용해 console/debugger 제거, gzip 압축, 이미지 최적화를 수행했습니다.
- 이미지 최적화에서는 `1.7MB → 240KB`, 3G 환경 로드 시간 `53.32s → 23.67s` 개선을 확인했습니다.
- “Chunk를 잘게 나누면 무조건 빠르다”가 아니라, 요청 수 증가로 로드 시간이 늘어나는 경우까지 실험했습니다.

**Keywords**  
Webpack, Vite, Rollup, Code Splitting, manualChunks, splitChunks, Gzip, Image Optimization, Bundle Analysis

---

### 4. 데이터 계층과 상태 관리를 보안·정합성 중심으로 설계합니다

DID 플랫폼은 인증서, 사용자, 시스템, 감사 로그 등 민감한 데이터가 많기 때문에 단순 상태 저장보다 데이터 정합성, 캐시 무효화, 세션 종료 시 데이터 파기가 중요했습니다.

**What I did**

- RTK Query의 `providesTags` / `invalidatesTags`를 활용해 데이터 변경 후 관련 UI가 자동으로 최신 상태를 반영하도록 설계했습니다.
- 로그아웃 또는 세션 만료 시 `resetApiState()`와 store purge를 통해 캐싱된 개인정보/VC 데이터를 제거했습니다.
- Session Storage whitelist 정책을 적용해 민감 데이터는 저장하지 않고, 필요한 UI/비식별 상태만 유지했습니다.
- TanStack Query와 Axios Interceptor를 결합해 데이터 fetching과 인증 갱신 책임을 분리했습니다.
- `URLSearchParams` 기반 동적 쿼리 빌더로 검색 조건이 많은 대시보드 API를 안정적으로 처리했습니다.

**Keywords**  
RTK Query, TanStack Query, Redux Toolkit, Zustand, Cache Invalidation, Session Storage, Data Consistency

---

### 5. 반복되는 개발 구조를 자동화합니다

여러 프로젝트가 동시에 운영되는 상황에서 반복 작업과 컨벤션 차이는 유지보수 비용으로 이어집니다. 저는 이 부분을 자동화와 구조화로 줄이는 데 집중했습니다.

**What I did**

- ESLint Flat Config, simple-import-sort, unused-imports 등을 적용해 import 정렬과 dead code 제거를 자동화했습니다.
- Vite의 `import.meta.glob`을 활용해 파일 기반 자동 라우팅을 구현했습니다.
- React Router 6.4의 `lazy`를 활용해 Component뿐 아니라 loader/action/ErrorBoundary까지 함께 비동기 로드하는 구조를 적용했습니다.
- React Hook Form + Zod 기반의 schema-driven form 구조를 만들어 런타임 검증과 TypeScript 타입 안정성을 함께 확보했습니다.
- sonner의 `toast.promise`를 활용해 비동기 요청의 pending/success/error 상태를 사용자에게 명확히 전달했습니다.

**Keywords**  
ESLint, Flat Config, import.meta.glob, React Router Data API, React Hook Form, Zod, sonner

---

## ETH Staking Deep Dive

> 아래 내용은 포트폴리오 본문에서는 접어두고, Notion toggle 또는 별도 기술 문서로 분리하는 것을 추천합니다.

### 1. Obol DVT 기반 Ethereum Staking 구조 설계

Obol Distributed Validator는 하나의 validator key를 여러 operator가 threshold 방식으로 나누어 운영하는 구조입니다. 저는 이 구조를 이해하기 위해 Charon의 내부 흐름, DKG, consensus, partial signature aggregation까지 정리했습니다.

**Key Points**

- Execution Layer / Consensus Layer / Charon / Validator Client 4계층 구조 정리
- Charon의 Fetcher → Consensus → DutyDB → ValidatorAPI → Partial Signature → Aggregation → Broadcast 흐름 분석
- QBFT 기반 duty consensus와 Threshold BLS 기반 부분 서명 구조 이해
- Anti-slashing을 위한 Charon / Validator Client 이중 방어 구조 정리

---

### 2. DKG Ceremony와 Key Custody 통제 설계

Obol DVT에서는 DKG를 통해 validator key share가 분산 생성됩니다. 중요한 것은 클러스터 생성 자체가 아니라 원본 key share가 단일 위치에 재조합되지 않도록 통제하는 것입니다.

**Key Points**

- Obol DV Launchpad 기반 DKG 절차 실습 및 문서화
- Creator / Operator / ENR / DKG command / 산출물 구조 정리
- `cluster-lock.json`, `deposit-data.json`, `charon-enr-private-key`, `validator_keys`의 의미와 민감도 분류
- `deposit-data.json`의 withdrawal credentials가 Safe address와 일치해야 한다는 검증 흐름 정리
- runtime에 필요한 artifact와 절대 stage하면 안 되는 secret artifact 구분
- 원본 key share가 중앙 repository, shared drive, control plane DB에 수집되지 않았음을 입증하는 운영지침 초안 작성

---

### 3. Safe Multisig / OVM 기반 소유권·권한 구조 설계

스테이킹에서 중요한 것은 validator 실행뿐 아니라 staking된 ETH의 출금 권한과 보상 수취 권한이 누구에게 있는지를 명확히 하는 것입니다.

**Key Points**

- Safe 3-of-4 multisig 구조를 기준으로 단독 소유권 및 내부 승인 구조 설계
- OVM role 구조 분석: `WITHDRAWAL_ROLE`, `CONSOLIDATION_ROLE`, `SET_BENEFICIARY_ROLE`, `RECOVER_FUNDS_ROLE`, `SET_REWARD_ROLE`, `DEPOSIT_ROLE`
- OVM의 `deposit`, `withdraw`, role grant 구조를 Solidity 인터페이스 수준에서 분석
- Owner Address를 EOA가 아니라 Safe로 설정해야 하는 이유 정리
- withdrawal credentials, Safe owner snapshot, threshold, signer 조직도, approval log를 연결한 소유권 증빙 체계 설계

---

### 4. Bare Metal 기반 DVT Runtime 배포 자동화 설계

ETH Staking 운영은 단순히 노드를 실행하는 일이 아니라, 여러 서버에 동일한 기준으로 runtime을 배포하고 위험한 artifact가 섞이지 않도록 검증하는 일이 중요합니다.

**Key Points**

- 4대 Bare Metal Host 기준으로 EL / CL / Charon / Validator Client / Web3Signer / KMS 구조 분리
- `cluster.yml`, `hosts.yml` 기반 host별 runtime render 구조 설계
- 승인된 `.charon` artifact만 stage하는 allowlist 기반 staging 설계
- `verify.sh`, `rollout.sh`, `host-preflight.sh`, `rollout-exec.sh` 흐름으로 배포 전 검증 강화
- `validator_keys`, keystore, mnemonic, seed, password file은 runtime staging 대상에서 제외
- Web Admin / API / Worker / Audit Log / Approval Workflow / Safe proposal export로 이어지는 control plane 구조 구상

---

### 5. Charon 운영·장애 분석 체계 수립

DVT 운영에서 Charon unhealthy는 단순 컨테이너 문제가 아니라 네트워크, beacon node, quorum, 리소스 문제가 결합되어 나타납니다.

**Key Points**

- Beacon node sync 상태 확인
- EL/CL 연동 상태 확인
- peer quorum 충족 여부 확인
- relay/direct P2P connectivity 확인
- disk I/O, memory, CPU saturation 확인
- Charon consensus timeout, insufficient peers, beacon API timeout 로그 패턴 분석
- direct P2P와 relay 기반 연결의 운영상 trade-off 정리
- Docker가 문제를 해결하는 것이 아니라 스토리지, 네트워크, 리소스 설계가 핵심이라는 점을 운영 문서로 정리

---

## Tech Stack

| Category | Stack |
| --- | --- |
| Frontend | React, TypeScript, React Router, TanStack Table, Dnd-Kit, sonner |
| State / Data | Redux Toolkit, RTK Query, TanStack Query, Zustand |
| Form / Validation | React Hook Form, Zod |
| Build / Performance | Webpack, CRACO, Vite, Rollup, Terser, CompressionPlugin, vite-plugin-compression, ImageMinimizerPlugin, vite-plugin-image-optimizer, Bundle Analyzer / Visualizer |
| Infra / Deployment | Docker, Docker Compose, Nginx, AWS EC2, Certbot, ngrok, HTTPS/TLS, Closed Network Deployment, Path-based Routing |
| Web3 / Staking | Ethereum Validator, Obol DVT, Charon, DKG, Threshold BLS, QBFT, Safe Multisig, OVM, Web3Signer, KMS |
| Operations / Governance | Approval Workflow, Audit Log, Evidence Package, Withdrawal Credentials 검증, Key Custody Control, Safe Owner Snapshot, Runtime Rollout, Charon Health Troubleshooting |

---

## Work Style

### 1. 먼저 구조를 이해합니다

새로운 기술을 사용할 때 단순히 예제를 따라 하지 않고, 내부 구조와 실패 케이스를 먼저 정리합니다. Obol DVT에서는 Charon의 consensus, partial signature, quorum, relay/direct P2P, DKG 산출물까지 구조적으로 이해하려고 했습니다.

### 2. 운영 가능한 형태로 문서화합니다

제가 작성하는 문서는 단순 학습 노트가 아니라, 다른 사람이 따라 할 수 있는 runbook과 guide를 목표로 합니다. 특히 ETH Staking에서는 회계감사, 내부통제, key custody까지 고려한 문서화를 진행했습니다.

### 3. 위험한 것은 자동화하지 않습니다

자동화는 좋아하지만, staking처럼 자산과 slash risk가 걸린 영역에서는 자동화와 승인 경계를 분리합니다. 상태 수집, 검증, render, preflight는 자동화하되 deposit, withdrawal, signer 변경 같은 고위험 작업은 approval과 audit 안에서만 진행되도록 설계합니다.

### 4. 프론트엔드를 제품의 가장자리까지 확장합니다

제 관심사는 UI 컴포넌트에만 있지 않습니다. 사용자가 보는 화면 뒤에 있는 인증, 네트워크, 배포, 데이터, 감사 로그, 운영 절차까지 연결되어야 제품이 안정적으로 동작한다고 생각합니다.

---

## Archive / Additional Notes

아래 항목은 메인 포트폴리오에서 길게 노출하기보다 별도 페이지나 toggle로 분리하는 것을 추천합니다.

- ETH Staking 상세 운영 노트
- GS 인증 대응 상세 기록
- 성능 최적화 실험 로그
- 모바일 / UI 관련 상세 페이지
- 초기 Web3 팀 프로젝트 및 학습 프로젝트
