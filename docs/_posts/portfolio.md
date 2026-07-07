# Ethan Kim

## Frontend / Web3 Engineer 

보안, 인증, 배포, 운영까지 고려해 제품을 설계하는 **Frontend / Web3 Engineer**입니다.

React와 TypeScript 기반의 프론트엔드 개발을 중심으로, 인증 흐름, 서버 상태 관리, 성능 최적화, 폐쇄망/온프레미스 배포, 감사 대응이 필요한 운영 환경까지 함께 다뤄왔습니다.

DID 신원 인증 플랫폼에서는 Holder, Issuer, Admin, Audit Admin 서비스를 구축하며 인증/인가, 감사 로그, 데이터 시각화, 폐쇄망 배포, GS 인증 대응을 경험했습니다. 현재는 4개국에 분산된 베어메탈 서버 4대 위에 Obol DVT 기반 Ethereum 분산 밸리데이터 클러스터를 직접 구축해 운영하며, key custody, Safe multisig, 모니터링·알림, 형상관리·회계 증빙까지 Web3 인프라 운영 전반으로 영역을 넓혔습니다. 최근에는 국방 기술 해커톤 플랫폼 D4D에 합류해 Next.js 프론트엔드와 Go API를 오가는 풀스택 개발을 하고 있습니다.

---

## Core Positioning

### 보안과 운영을 제품 구조로 풀어내는 프론트엔드 개발자

저는 UI 구현에서 끝나지 않고, 사용자가 실제 운영 환경에서 안정적으로 서비스를 사용할 수 있도록 **인증, 데이터 흐름, 배포 구조, 장애 대응, 감사 로그**까지 함께 고려합니다.

제가 강점을 가지는 영역은 다음과 같습니다.

| Area | Keywords |
| --- | --- |
| 인증/보안 UX | JWT, Refresh Token, HttpOnly Cookie, Silent Refresh, Session Security |
| 프론트엔드 아키텍처 | Multi-service Routing, Server State, Client State, ErrorBoundary |
| 배포/인프라 | Docker, Nginx, HTTPS, AWS, OVH Bare Metal, Ansible, On-premise, Closed Network |
| 성능 최적화 | Code Splitting, Gzip, Image Optimization, Bundle Analysis, Caching |
| Web3 운영 | Ethereum Staking, Obol DVT, DKG, Charon, Safe, Web3Signer, AWS Secrets Manager |
| 모니터링/관측 | Prometheus, Grafana, Loki, Telegram Alerting, Daily Report Automation |
| 감사/통제 | Audit Log, Configuration Management, Approval Workflow, Key Custody |

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

### 2. ETH Staking / Obol DVT 분산 밸리데이터 구축·운영

**Role**  
`Technical Owner / Infrastructure / Operations`

**Overview**  
런던, 프랑크푸르트, 바르샤바, 싱가포르 4개 리전의 OVH 베어메탈 서버 4대 위에 Obol DVT 기반 Ethereum 분산 밸리데이터 클러스터를 직접 구축해 운영하고 있습니다. 서버 하드닝과 DKG부터 key custody, deposit, 모니터링·알림, 형상관리·회계 증빙까지 밸리데이터 라이프사이클 전체를 감사 가능한 운영 체계로 만들었고, 현재 Hoodi 테스트넷에서 멀티 밸리데이터를 운영하며 메인넷 이행을 준비하고 있습니다.

**Key Contributions**

- 4개 리전 베어메탈 서버에 EL / CL / Charon / Web3Signer 스택을 구축하고, 1개 리전 장애를 허용하는 3-of-4 임계 분산 클러스터 구성
- RAID-1 스토리지, LUKS 암호화 볼륨, vRack 사설망, UFW/fail2ban 등 서버 보안 기준선을 구축하고 Ansible role 13종으로 코드화(IaC)
- DKG ceremony를 수행하고 validator key share를 AWS Secrets Manager에 노드별로 분산 적재 — 노드별 IAM 유저와 SourceIp 조건으로 접근을 격리해, 한 노드가 침해되어도 서명 임계값에 도달할 수 없는 custody 구조 설계
- Web3Signer 원격 서명과 slashing protection DB를 구성하고, RPC / Engine / Beacon / 서명 API는 전부 로컬 바인드로 비공개 처리
- Ledger 하드웨어 지갑 기반 3-of-5 Safe multisig를 withdrawal owner로 하는 0x02(compounding) credentials 구성, 테스트넷 밸리데이터 4기 deposit 수행
- 구 클러스터의 voluntary exit → 서버 decommission → 신규 클러스터 재구축까지 라이프사이클 전 구간을 직접 수행하고 단계별 런북으로 문서화
- Prometheus / VictoriaMetrics 메트릭과 Loki 로그 파이프라인, Grafana 대시보드 구축 — 밸리데이터 생애주기·slashing 등 Telegram 알림 룰 16종과 beacon 워처 일일 리포트, 보상 결산 봇 운영
- Git 기반 형상관리대장(변경요청 40여 건 누적)과 회계 정책 문서, 보상 자동 추출 도구로 감사 대응 가능한 증빙 체계 구축

**Impact**

- 밸리데이터 운영을 "노드 띄우기"가 아니라 변경 승인, 검증, 기록, 감사가 남는 운영 체계로 만들었습니다. 모든 변경은 형상관리대장과 Git 커밋으로 추적됩니다.
- 키 유출, slashing, 리전 장애 같은 위험을 키 분산, IAM 격리, 임계 분산이라는 구조로 낮추고, 남은 위험은 런북과 알림 체계로 대응합니다.
- 복잡한 DVT 구조를 기술팀뿐 아니라 보안, 회계/감사 관점에서도 설명 가능한 문서로 정리했습니다.

**Tech / Domain**  
Ethereum Validator, Obol DVT, Charon, DKG, Threshold BLS, Web3Signer, AWS Secrets Manager / IAM, Safe Multisig (Ledger), OVH Bare Metal, LUKS, RAID, vRack, Ansible, Docker, Prometheus, VictoriaMetrics, Loki, Grafana, Telegram Bot

---

### 3. D4D — 국방 기술 해커톤 플랫폼

**Role**  
`Frontend / Full-stack Development`

**Overview**  
국방 기술 해커톤 플랫폼 D4D(Deploy for Defense)의 웹 서비스를 개발하고 있습니다. 참가 신청, 팀 구성, 프로젝트 제출, 심사, 쇼케이스로 이어지는 해커톤 운영 전 과정을 하나의 서비스로 담았고, Turborepo 모노레포에서 Next.js 프론트엔드와 Go(Fiber) API를 함께 다룹니다.

**Key Contributions**

- Next.js 15 App Router + React 19 기반 프론트엔드 개발, Feature-Sliced Design 구조 도입
- 이벤트 phase(upcoming → apply → live → judging → closed)에 따라 신청, 제출, 쇼케이스 노출이 달라지는 상태 기반 UX 구현
- 팀 초대/수락/탈퇴/해체 흐름 구현 — 초대 링크의 returnTo 검증으로 open redirect를 차단하고, 정원 제한과 동시 수락 경합을 안전하게 처리
- 프로젝트 제출 흐름 안정화: 팀 멤버 검증, 재제출 시 upsert 멱등성 보장, 제출 전 검토 모달
- Go API 이슈 직접 수정(pgx UUID 인코딩, 팀 초대 트랜잭션 폴백 등) — 프론트엔드에 갇히지 않고 풀스택으로 문제 해결
- Playwright E2E 테스트 체계 구축(공개/인증 시나리오 분리)과 GitHub Actions CI 연동
- KO/EN 다국어 지원, 참가 가이드 등 운영 콘텐츠 반영

**Impact**

- 대규모 동시 제출을 가정한 rate limit과 멱등성 설계로, 마감 직전 트래픽에서도 안전한 제출 흐름을 만들었습니다.
- 신청부터 심사, 수상까지 해커톤 운영 전 과정을 온라인에서 처리할 수 있는 플랫폼으로 완성했습니다.

**Tech Stack**  
Next.js 15, React 19, TypeScript, Zustand, TanStack Query, Turborepo, Go, Fiber, PostgreSQL(Supabase), Redis, Playwright, GitHub Actions, Fly.io, Vercel

**Link**  
[d4d.tech](https://d4d.tech)

---

### 4. The Helia — 프리미엄 산후조리원 웹사이트

**Role**  
`Frontend Development / UI System / SEO / Operations`

**Overview**  
프리미엄 산후조리원 브랜드 The Helia의 공식 웹사이트를 구현하고 운영하고 있습니다. 객실, 신생아 케어, 스파/웰니스, 예약 안내 등 상담 전에 확인해야 하는 정보를 명확한 섹션 흐름으로 구성하고, 차분하고 고급스러운 브랜드 톤을 유지하는 디자인 시스템을 만들었습니다. 론칭 이후에도 고객 후기, 예약 전환, SEO를 계속 개선하며 실서비스로 운영 중입니다.

**Key Contributions**

- Next.js / TypeScript 기반 프로덕션 웹사이트 구현 및 운영
- locale 라우팅 기반 한/영 다국어 지원 — 페이지별 메타데이터, hreflang, Open Graph까지 언어별로 구성
- JSON-LD 구조화 데이터, 동적 사이트맵, canonical URL 등 검색 노출을 위한 SEO 최적화
- 객실(VIP/VVIP/Prestige), 스파, 신생아실, 예약·가격, 고객 후기, FAQ 등 예약 전 탐색에 필요한 정보 구조 설계
- 홈 퀵핏 가이드, 지도 앱 선택, 예약 CTA 개선 등 상담 전환을 높이는 기능을 반복적으로 추가
- Framer Motion, GSAP, Lenis를 활용한 부드러운 인터랙션과 모바일 가독성 중심 레이아웃
- Vercel Analytics / Speed Insights로 배포 후 사용자 행동과 성능 확인

**Impact**

- 단발성 제작이 아니라 론칭 이후 데이터와 현장 요청을 반영해 계속 개선하는, 운영 중인 실서비스입니다.
- 사용자가 브랜드 정보를 빠르게 이해하고 상담/예약 행동으로 이어지도록 정보 구조와 전환 흐름을 설계했습니다.

**Tech Stack**  
Next.js, React, TypeScript, Tailwind CSS, Framer Motion, GSAP, Lenis, Vercel

**Link**  
[thehelia.co.kr](https://www.thehelia.co.kr)

---

### 5. Tixxet — Web3 QR/NFC 티켓팅 MVP

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
- Stackflow를 활용해 모바일 UX에 자연스러운 화면 전환을 구현했습니다.

**Tech Stack**  
Stackflow, Next.js, TypeScript, Turborepo, Privy, Account Abstraction, Wagmi, Viem, Ethers.js, Supabase, AES256, Web NFC API

---

## Other Projects

- **ChiroMed — 의료기기 기업 웹사이트 리뉴얼**: 수부 임플란트, 의료용품 등을 다루는 의료기기 기업의 소개·제품 카탈로그 웹사이트를 리뉴얼했습니다. 프레임워크 없이 단일 HTML 경량 SPA로 구현해 한/영/중 3개국어, 제품 상세와 브로셔 다운로드, 해시 라우팅을 지원하며, Alibaba Cloud 인프라에서 서비스되고 있습니다.

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

폐쇄망, 온프레미스, AWS EC2, 베어메탈 환경에서 Docker Multi-stage Build부터 서버 하드닝·IaC까지 인프라 계층을 직접 구성하며 여러 서비스를 안정적으로 배포했습니다.

**What I did**

- Holder, Issuer, Issuer Admin, Admin 등 4개 프론트엔드 프로젝트를 Docker Multi-stage Build로 빌드했습니다.
- 최종 산출물은 Node.js 런타임이 아니라 정적 파일만 포함된 경량 Nginx 이미지로 구성했습니다.
- 여러 서비스를 하나의 Nginx 컨테이너에서 `/holder`, `/issuer`, `/issuer-admin`, `/admin` path 기준으로 서빙했습니다.
- SPA 새로고침 시 404가 발생하지 않도록 각 path별 `try_files` 설정을 구성했습니다.
- 운영 서버에서 `docker load → docker compose up`으로 재현 가능한 배포 흐름을 만들었습니다.
- 4대 베어메탈 서버는 Ansible role 13종으로 SSH 하드닝, UFW, RAID, LUKS, vRack 사설망까지 코드화해 동일한 기준으로 멱등하게 수렴시켰습니다.

**Keywords**  
Docker, Multi-stage Build, Nginx, Closed Network, Path-based Routing, SPA Routing, Ansible, Infrastructure as Code, Bare Metal

---

### 3. 성능 최적화를 측정과 실험으로 접근합니다

빌드 최적화에서는 단순히 플러그인을 추가하는 데서 끝내지 않고, 번들 결과와 네트워크 요청 수, gzip 용량, 로드 시간, 빌드 시간을 비교했습니다.

**What I did**

- CRACO/Webpack 환경에서 `splitChunks`를 활용해 `react-vendor`, `ui-vendor`, `vendor` 청크를 분리했습니다.
- Vite/Rollup 환경에서는 `manualChunks`의 특성을 분석하고, Webpack과 다른 priority 처리 방식을 검증했습니다.
- Terser, CompressionPlugin, ImageMinimizerPlugin, vite-plugin-image-optimizer 등을 적용해 console/debugger 제거, gzip 압축, 이미지 최적화를 수행했습니다.
- 이미지 최적화에서는 `1.7MB → 240KB`, 3G 환경 로드 시간 `53.32s → 23.67s` 개선을 확인했습니다.
- "Chunk를 잘게 나누면 무조건 빠르다"가 아니라, 요청 수 증가로 로드 시간이 늘어나는 경우까지 실험했습니다.

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

### 6. 위험 자산을 다루는 시스템은 승인·격리 구조로 설계합니다

Ethereum 밸리데이터 운영처럼 자산과 slash risk가 걸린 시스템에서는, 얼마나 많이 자동화했는지보다 무엇을 자동화하지 않았는지가 더 중요하다고 생각합니다.

**What I did**

- validator key share를 노드별로 분산 적재하고, AWS IAM 유저와 SourceIp 조건으로 접근을 격리해 한 노드가 침해되어도 서명 임계값에 도달하지 못하도록 설계했습니다.
- withdrawal 권한을 EOA가 아니라 Ledger 하드웨어 지갑 기반 Safe multisig로 구성해, 단일 키 유출이 곧 자금 유출로 이어지지 않도록 했습니다.
- 상태 수집·검증·배포 수렴은 Ansible로 자동화하되, deposit·withdrawal·signer 교체 같은 고위험 작업은 승인과 기록을 거치도록 자동화와 승인의 경계를 분리했습니다.
- 모든 변경을 Git 커밋과 형상관리대장에 연결해, 누가 언제 무엇을 왜 바꿨는지 사후에 추적 가능하게 만들었습니다.

**Keywords**  
Key Custody, Threshold Signing, IAM Isolation, Safe Multisig, Approval Workflow, Configuration Management

---

## Tech Stack

| Category | Stack |
| --- | --- |
| Frontend | React, Next.js (App Router), TypeScript, React Router, TanStack Table, Framer Motion, GSAP |
| State / Data | Redux Toolkit, RTK Query, TanStack Query, Zustand |
| Form / Validation | React Hook Form, Zod |
| Backend | Go (Fiber), PostgreSQL, Supabase, Redis, JWT |
| Build / Performance | Webpack, CRACO, Vite, Rollup, Terser, CompressionPlugin, ImageMinimizerPlugin, Bundle Analyzer / Visualizer |
| Testing / CI | Playwright E2E, GitHub Actions |
| Infra / Deployment | Docker, Docker Compose, Nginx, AWS EC2, OVH Bare Metal, Ansible, LUKS, RAID, vRack, UFW, HTTPS/TLS, Closed Network Deployment, Vercel, Fly.io |
| Web3 / Staking | Ethereum Validator, Obol DVT, Charon, DKG, Threshold BLS, QBFT, Safe Multisig, Web3Signer, AWS Secrets Manager / IAM |
| Monitoring / Observability | Prometheus, VictoriaMetrics, Grafana, Loki, Grafana Alloy, Telegram Alerting |
| Operations / Governance | Configuration Management(형상관리대장), Audit Log, Approval Workflow, Runbook, Key Custody Control, 회계 증빙 자동화 |

---

## Work Style

### 1. 먼저 구조를 이해합니다

새로운 기술을 사용할 때 단순히 예제를 따라 하지 않고, 내부 구조와 실패 케이스를 먼저 정리합니다. Obol DVT에서는 Charon의 consensus, partial signature, quorum, relay/direct P2P, DKG 산출물까지 구조적으로 이해한 뒤 클러스터를 구축했습니다.

### 2. 운영 가능한 형태로 문서화합니다

제가 작성하는 문서는 단순 학습 노트가 아니라, 다른 사람이 따라 할 수 있는 runbook과 guide를 목표로 합니다. ETH Staking에서는 형상관리대장, 회계 정책, exit/decommission 런북까지 회계감사와 내부통제를 고려한 문서 체계를 운영하고 있습니다.

### 3. 위험한 것은 자동화하지 않습니다

자동화는 좋아하지만, staking처럼 자산과 slash risk가 걸린 영역에서는 자동화와 승인 경계를 분리합니다. 상태 수집, 검증, 배포 수렴은 자동화하되 deposit, withdrawal, signer 변경 같은 고위험 작업은 승인과 기록 안에서만 진행합니다.

### 4. 프론트엔드를 제품의 가장자리까지 확장합니다

제 관심사는 UI 컴포넌트에만 있지 않습니다. 사용자가 보는 화면 뒤에 있는 인증, 네트워크, 배포, 데이터, 감사 로그, 운영 절차까지 연결되어야 제품이 안정적으로 동작한다고 생각합니다.

---
