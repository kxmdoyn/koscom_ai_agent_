# 🏦 KOSCOM AI Agent Platform

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)
![FastAPI](https://img.shields.io/badge/FastAPI-Framework-009688?logo=fastapi)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker)
![MCP](https://img.shields.io/badge/Architecture-MCP%20Multi--Agent-orange)
![Status](https://img.shields.io/badge/Project-Academic%20Research-success)
![Domain](https://img.shields.io/badge/Domain-Financial%20AI-critical)

## 🏆 **Koscom AI Agent Challenge 2025 대상 수상작**  
원화 스테이블코인 발행사의 리스크 관리 자동화를 목적으로 개발된 AI Agent 시스템입니다.

링크 : https://www.koscom.co.kr/portal/bbs/B0000064/view.do?nttId=30321&searchCnd=&searchWrd=&gubun=&delcode=0&searchBgnDe=&searchEndDe=&useAt=&replyAt=&menuNo=200629&sdate=&edate=&deptId=&isk=&ise=&viewType=&type=&year=&pageIndex=2

# 🪙 K-WON — 원화 스테이블코인 리스크 관리 AI Agent



> Team DANCOM | 권민석 · 김도연 · 양서윤 · 이도원

원화 스테이블코인(K-WON) 발행사를 위한 **실시간 리스크 관리 AI Agent** 시스템입니다.  
Claude AI와 4개의 MCP Server를 연동하여 담보 검증, 예치은행 리스크 분석, 규제 보고서 자동화, 감사 추적을 통합적으로 수행합니다.

---

## 🎯 Why KRW Stablecoin Needs This System

### 문제 및 시나리오: 원화 스테이블코인, 지금 당장 해결해야 할 4가지

2025년, 한국은 가상자산 규제를 완료하고 K-WON 발행이 현실화되고 있습니다.  
글로벌 스테이블코인 시장은 **2,080억 달러** 규모로 성장했지만, 그 99.83%가 미국 달러 기반입니다.  
한국이 대응하지 않으면, 원화의 디지털 결제 영향력은 달러 패권에 잠식됩니다.

그러나 원화 스테이블코인 발행사는 아직 이런 문제를 수작업으로 처리하고 있습니다:

```
① 준비금 실시간 검증 불가   → "1:1 담보가 지금 맞는지 아무도 모른다"
② 예치은행 신용 & 집중위험  → "은행 하나가 무너지면 시스템 전체가 흔들린다"
③ 규제 보고/감사 대응 지연  → "보고서 하나 만드는 데 사람이 일주일 걸린다"
④ 대규모 유동성 리스크      → "대량 소각 요청이 오면 실시간 대응이 불가능하다"
```

### 해결: AI Agent가 24시간 자동으로 지킨다

```
Before (수작업)                  After (K-WON AI Agent)
────────────────────────         ────────────────────────
수작업 데이터 수집 (2일)    →    실시간 자동 수집
엑셀 기반 리스크 계산 (1일) →    MCP Tool 자동 계산
수동 보고서 작성 (7일)      →    AI 자동 생성
수작업 감사 대응 (~20%)     →    블록체인 증빙 자동화 (< 2%)
담당자 4~5명 투입            →    담당자 1명 의사결정만
```

반복적인 수작업은 AI Agent가 맡고, 사람은 의사결정에만 집중할 수 있는 구조입니다.

---

## ⚡ End-to-End Flow

K-WON 발행 한 건이 처리되는 전체 흐름입니다.

```
1. 수탁은행       발행사가 은행 계좌로 원화 입금
       │
       ▼
2. 운영 센터      내부 발행 관리 시스템에 발행 요청 기록
       │
       ▼
3. 온체인 Mint    스마트 컨트랙트가 K-WON 토큰 발행, 블록체인에 기록
       │
       ▼
4. Cloud DB       Bastion Server를 통해 PostgreSQL에 발행·입금 이력 저장
       │
       ▼
5. API / metrics  운영 서버가 최신 발행량·준비금 정보를 /metrics에 반영
       │
       ▼
6. MCP 평가       MCP Agent가 온·오프체인 수치를 조회해 담보율 자동 계산
       │
       ▼
7. Dashboard      총 발행량·담보율·기관별 잔액 상태가 실시간으로 갱신
```

이 7단계가 발행 즉시 자동으로 실행되며, 이상 감지 시 Slack 경보와 함께 MCP Agent가 즉각 대응합니다.

---

## 🗄️ Cloud Data Pipeline

로컬 시뮬레이션 환경에서 시작해 실제 클라우드 인프라로 이어지는 데이터 파이프라인 설계입니다.

```
┌─────────────────────────────────────────────────────────┐
│  Local Simulation                                        │
│  Hardhat (Ethereum) → EVM Bytecode → 로컬 블록체인 환경  │
└────────────────────────┬────────────────────────────────┘
                         │ 온체인 이벤트 발생
                         ▼
┌─────────────────────────────────────────────────────────┐
│  Naver Cloud Platform                                    │
│  dancom-bastion ──→ dancom-ubuntu                        │
│                         │                               │
│              Cloud DB for PostgreSQL                     │
│              (발행·입금 이력 영구 저장)                  │
│                         │                               │
│              InfluxDB                                    │
│              (시계열 메트릭 실시간 저장)                 │
└────────────────────────┬────────────────────────────────┘
                         │ REST API (/metrics, /banks)
                         ▼
┌─────────────────────────────────────────────────────────┐
│  MCP Agent Queryable Architecture                        │
│                                                          │
│  MCP Server ① krw-full-reserve   → 담보율 계산          │
│  MCP Server ② bank_monitering    → 예치은행 리스크      │
│  MCP Server ③ report-master      → 규제 보고서          │
│  MCP Server ④ tx_audit           → 감사 추적            │
│                    │                                     │
│              Claude AI (MCP Client)                      │
│              자연어 질문 → Tool 선택 → 즉시 답변          │
└─────────────────────────────────────────────────────────┘
```

로컬 블록체인 시뮬레이션 → 클라우드 DB 마이그레이션 → API 실시간 피드 → MCP Agent 쿼리 가능 구조까지, 프로덕션 수준의 데이터 파이프라인을 직접 설계하고 구현했습니다.

---

## 🏗️ 전체 아키텍처

```
[ K-WON Dashboard (frontend/web_chat_app.py) ]
              │  HTTP
              ▼
     [ MCP Client (Claude AI) ]
              │  MCP Protocol
    ┌─────────┼──────────────────────┐
    ▼         ▼            ▼         ▼
krw-full-  bank_       report-    tx_audit
reserve    monitering  master
(담보검증)  (예치은행)  (규제보고)  (감사추적)
              │
   [ PostgreSQL / InfluxDB — Naver Cloud ]
   [ Ethereum Mock L2 ]
```

---

## 📁 프로젝트 구조

```
final_koscom_ai_agent/
├── Dockerfile
├── compose.yml
├── requirements.txt
│
├── frontend/                        # K-WON 운영 대시보드
│   ├── web_chat_app.py              # Flask 기반 MCP Client (Claude AI 연동)
│   ├── static/
│   │   ├── css/
│   │   └── js/
│   └── templates/
│       └── index.html
│
├── infra/docker/                    # 인프라 구성
│   ├── Dockerfile.api
│   ├── Dockerfile.worker
│   ├── docker-compose.yml
│   ├── influxdb-compose.yml
│   └── nginx.conf
│
└── mcp_servers/
    ├── krw-full-reserve/            # MCP Server ① 1:1 완전 담보 실시간 검증
    │   ├── mcp_server.py
    │   ├── mcp_http_gateway.py
    │   ├── app_mcp/
    │   ├── config/
    │   ├── core/
    │   ├── data/
    │   └── tests/
    │
    ├── bank_monitering/             # MCP Server ② 예치은행 신용위험 & 분산도
    │   ├── mcp_server.py
    │   ├── mcp_http_gateway.py
    │   ├── app_mcp/
    │   ├── core/
    │   └── scripts/
    │
    ├── report-master/               # MCP Server ③ 규제 준수 보고서 자동 생성
    │   ├── mcp_server.py
    │   ├── gateway.py
    │   ├── claude_mcp_kwon_reports.py
    │   ├── claude_mcp_provider.py
    │   ├── report_generator_routes.py
    │   ├── report_routes.py
    │   ├── app_mcp/
    │   └── artifacts/
    │
    └── tx_audit/                    # MCP Server ④ 실시간 감사추적 & 불변 기록
        ├── main.py
        ├── audit_gateway.py
        ├── verify_etherscan.py
        ├── apps/
        ├── core/
        ├── servers/
        ├── scripts/
        ├── data/
        └── docs/
```

---

## 🔧 MCP Server 상세

### MCP Server ① `krw-full-reserve` — 1:1 완전 담보 실시간 검증

온체인 발행량과 오프체인 준비금을 실시간으로 비교하여 담보 100% 유지 여부를 자동 검증합니다.

**제공 Tool**

| Tool | API | 설명 |
|---|---|---|
| `get_onchain_state` | `/metrics` | 온체인 K-WON 발행량 실시간 조회 |
| `get_offchain_reserves` | `/banks` | 금융기관별 오프체인 준비금 실시간 조회 |
| `check_coverage` | Logic: Internal | 담보율 자동 계산 및 상태 판정 |
| `get_risk_report` | GenAI: Report | 종합 리스크 리포트 자동 생성 |
| `history` | DB | 기간별 담보·준비금 이력 조회 |

**담보율 상태 기준**

| 상태 | 기준 |
|---|---|
| 🟢 SAFE | 105% 이상 |
| 🟡 WARNING | 100 ~ 105% |
| 🔴 CRITICAL | 100% 미만 — 즉시 입금 필요 |

---

### MCP Server ② `bank_monitering` — 예치은행 신용위험 & 분산도 관리

Flask HTTP Gateway 기반으로 예치은행의 신용 위험을 평가하고 역할 기반 분산 정책에 따라 리밸런싱 권고를 생성합니다.

**제공 Tool**

| Tool | 설명 |
|---|---|
| `normalize_name` | 은행명 표준화 |
| `calc_risk_score` | FSS 재무 점수 기반 신용등급 산출 |
| `check_policy` | 7가지 규제 위반 자동 탐지 및 조치 권고 |
| `suggest_rebalance` | 역할 기반 목표 배분 대비 리밸런싱 계획 생성 |

**역할별 분산 정책**

| 역할 | 목표 비중 | 신용등급 기준 |
|---|---|---|
| Commercial Bank (주수탁) | 15% | FSS 80점 이상 |
| Secondary Custodian | 10~12% | FSS 75점 이상 |
| Broker (중개) | 5~8% | FSS 60점 이상 |
| Custody Agent (보관) | 0% | FSS 85점 이상 |

**AI 종합 리스크 점수 (5개 항목 가중 평가)**  
신용등급(35%) · LCR 유동성 비율(20%) · 예금보험 커버율(15%) · 시장 신용위험(20%) · 뉴스 감성 분석(10%)

---

### MCP Server ③ `report-master` — 규제 준수 보고서 자동 생성

FastAPI + LangGraph + MCP Tool + Slack 연동으로 규제 컴플라이언스 전 과정을 자동화하는 3단계 Agent 구조입니다.

```
1. 실시간 리스크 감지 Agent
   APScheduler 15분 주기 자동 실행 → 위험 감지 시 Slack 즉시 경보 → Snapshot DB 기록

2. 월간 보고서 Agent (LangGraph, AI Reasoning 12단계)
   Load Data → Validate Quality → Assess Collateral → Check Liquidity
   → Verify PoR → Cross-Validate → Summarize → Review Disclosure
   → Human Review (Slack 승인/반려) → Generate DOCX → Notify Slack

3. 대화형 MCP Tool Agent
   Claude가 자연어 질문 해석 → Tool 선택 → 결과 설명
```

**제공 Tool**

| Tool | 설명 |
|---|---|
| `run_monthly_report(period)` | 지정 월 규제 준수 보고서 DOCX 자동 생성 |
| `get_latest_report()` | 가장 최근 보고서 요약 및 다운로드 경로 반환 |
| `get_report(period)` | 특정 기간 보고서 메타 정보 조회 |
| `get_collateral_status()` | 현재 담보율·안전 등급 즉시 확인 |
| `get_risk_summary()` | 담보·페그·유동성·PoR 4대 지표 종합 요약 |
| `get_compliance_alerts()` | 최근 컴플라이언스 경고 이력 조회 |

> **Human-in-the-loop**: LangGraph Interrupt 기능으로 최종 보고서 확정 전 담당자 Slack 승인 필수

---

### MCP Server ④ `tx_audit` — 실시간 감사추적 및 불변 기록 관리

모든 온체인 거래 이벤트를 수집하고 Merkle Tree + 블록체인 앵커링으로 위변조를 원천 차단합니다.

**데이터 파이프라인 (5단계)**

```
수집(Collection) → 저장(Storage) → 배치(Batch) → 앵커링(Anchoring) → 증명(Proof)
```

**핵심 기술**

- **Merkle Tree**: 루트 해시 하나로 전체 데이터 위변조 여부 확인, Merkle proof로 특정 항목만 선택적 증명
- **블록체인 앵커링 (Mock L2)**: DB Root 변조 시 외부 블록체인 기록과 비교로 즉시 탐지 (`verify_etherscan.py`)
- **증빙팩 (ProofPack)**: `proof_pack.json` + `event_raw.json` + `merkle_proof.json` + `anchor_metadata.json` + `verification_guide.md`로 구성된 ZIP 자동 생성

**Trust Design 6원칙**: 완전성 · 연속성 · 변경불가성 · 추적가능성 · 재현가능성 · 검증가능성

---

## 💻 대시보드 (`frontend/`)

`web_chat_app.py` (Flask)가 Claude AI MCP Client로 동작하는 K-WON 운영 인터페이스입니다.

- **ON-CHAIN SUPPLY**: 총 발행량, 순유통량 실시간 표시
- **COLLATERAL RATIO**: 담보율 · 목표 최소 비율 · 초과 담보 금액
- **OFF-CHAIN RESERVES**: 기관별 예치 잔액 개별 카드 (신한/국민/하나/NH/KSD 등)
- **REPORT / EVIDENCE**: 버튼 한 번으로 리스크 보고서 및 감사 증빙팩 즉시 생성
- **Claude AI Chat**: 자연어로 담보 분석, 보고서 생성, 온체인 증빙 조회

---

## 🚀 시작하기

### 사전 요구사항

- Python 3.10+
- Docker & Docker Compose
- PostgreSQL / InfluxDB (또는 `compose.yml` 사용)
- Claude API Key

### 설치 및 실행 (Docker Compose)

```bash
git clone https://github.com/dancom-MCP-AI-Agent/final_koscom_ai_agent_public.git
cd final_koscom_ai_agent_public

cp .env.example .env
# .env 파일에 API Key 등 환경 변수 입력

docker compose up --build
```

### 환경 변수

```env
ANTHROPIC_API_KEY=your_api_key_here
DATABASE_URL=postgresql://user:password@host:5432/kwon_db
OPERATION_SERVER_URL=http://your-operation-server:port
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/...
DART_API_KEY=your_dart_api_key
```

### 개별 MCP Server 실행 (로컬 개발)

```bash
# MCP Server ① 담보 검증
cd mcp_servers/krw-full-reserve
python mcp_server.py

# MCP Server ② 예치은행 리스크
cd mcp_servers/bank_monitering
python mcp_http_gateway.py

# MCP Server ③ 규제 보고서
cd mcp_servers/report-master
python gateway.py

# MCP Server ④ 감사 추적
cd mcp_servers/tx_audit
python main.py
```

### 대시보드 실행

```bash
cd frontend
python web_chat_app.py
# http://localhost:5100
```

---

## 👥 팀원

| 이름 | 역할 |
|---|---|
| 권민석 | 시스템 아키텍처, MCP Server ① (`krw-full-reserve`) |
| 양서윤 | MCP Server ② (`bank_monitering`), DART/FSS 연동 |
| 김도연 | MCP Server ③ (`report-master`), LangGraph 보고서 자동화 |
| 이도원 | MCP Server ④ (`tx_audit`), 블록체인 앵커링 & 감사추적 |

---

> 🏆 **Koscom AI Agent Challenge 2025 대상 수상작**  
> 원화 스테이블코인 발행사의 리스크 관리 자동화를 목적으로 개발된 AI Agent 시스템입니다.
