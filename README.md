# Accelerate Agentic AI

**Agentic AI Acceleration Program**을 위한 Kiro Power입니다.
AWS Specialist SA와 고객 개발자가 함께 프로덕션 수준의 AI 에이전트를 구축하는 co-building 과정을 체계적으로 지원합니다.

> **"Show Together, Don't Tell"** — 강의가 아닌, 함께 만드는 co-building.

## 개요

이 Power는 **AI-DLC(AI-Driven Development Life Cycle)** 방법론을 기반으로 에이전트 개발을 3단계 적응형 워크플로우로 구조화합니다:

| 단계 | 핵심 질문 | 주요 활동 |
|------|----------|----------|
| **Inception** | 무엇을, 왜 만드는가? | 요구사항 분석, 멀티에이전트 패턴 선택, 아키텍처 설계 |
| **Construction** | 어떻게 만드는가? | 코드 구현, 도구 개발, 테스트, 품질 검증 |
| **Operations** | 어떻게 배포/운영하는가? | AgentCore 배포, Cognito 인증, 모니터링 설정 |

## 기술 스택

| 구성 요소 | 기술 | 용도 |
|-----------|------|------|
| Agent SDK | [Strands Agents SDK](https://strandsagents.com/latest/documentation/) | 에이전트 개발 프레임워크 |
| Runtime | [Amazon Bedrock AgentCore](https://github.com/awslabs/amazon-bedrock-agentcore-samples) | 에이전트 배포 및 운영 |
| Model | Amazon Bedrock (Claude Sonnet 4, Nova 등) | LLM 추론 |
| Memory | AgentCore Memory | 에이전트 상태 및 지식 관리 |
| Gateway | AgentCore Gateway | API를 에이전트 도구로 변환 |
| IDE | Kiro | Agentic coding 환경 |
| Frontend | React + Vite + TypeScript + Tailwind | UI (필요 시) |
| DB | SQLite (로컬) → DynamoDB (프로덕션) | 데이터 저장 |

## 프로젝트 구조

```
accelerate-agentic-ai/
├── POWER.md                      # Power 메타데이터 및 전체 가이드
├── mcp.json                      # MCP 서버 설정
├── steering/
│   ├── setup.md                  # 프로젝트 초기화 가이드
│   ├── program-guide.md          # Co-Building 진행 가이드
│   ├── inception.md              # AI-DLC Inception 단계 가이드
│   ├── construction.md           # AI-DLC Construction 단계 가이드
│   ├── operations.md             # AI-DLC Operations 단계 가이드
│   └── best-practices.md         # Agentic coding 모범 사례
└── README.md
```

## 적응형 워크플로우

AI-DLC의 핵심 원칙: **워크플로우가 프로젝트 복잡도에 맞춰 적응합니다.**

| 깊이 | 적용 조건 | Inception | Construction |
|------|----------|-----------|-------------|
| **Minimal** | 단순하고 명확한 요청 | 의도 분석만 | 바로 구현 |
| **Standard** | 일반적 복잡도 | 요구사항 + 아키텍처 | 설계 + 구현 + 테스트 |
| **Comprehensive** | 복잡하고 고위험 | 전체 분석 + 리스크 평가 | 전체 설계 + 구현 + 테스트 + 검증 |

각 단계 완료 시 **승인 게이트**를 통해 SA와 고객이 함께 리뷰하고 승인합니다.

## 빠른 시작

### 0단계: 프로젝트 초기화 (최초 1회)

Power 설치 후 프로젝트에서 처음 개발을 시작할 때:

```
readSteering("accelerate-agentic-ai", "setup.md")
```

서브 에이전트(Planner, Developer, Inspector), steering 파일, 상태 추적 파일이 자동 생성됩니다.

### 1단계: 프로그램 시작

```
readSteering("accelerate-agentic-ai", "program-guide.md")
```

### 2단계: AI-DLC 단계별 진행

```
# Inception (기획/설계)
readSteering("accelerate-agentic-ai", "inception.md")

# Construction (구현/테스트)
readSteering("accelerate-agentic-ai", "construction.md")

# Operations (배포/운영)
readSteering("accelerate-agentic-ai", "operations.md")
```

### 3단계: 모범 사례 참고

```
readSteering("accelerate-agentic-ai", "best-practices.md")
```

## MCP 서버

이 Power는 두 개의 MCP 서버를 통해 실시간 문서 검색과 개발 가이드를 제공합니다.

### strands-agents

Strands Agents SDK 문서 검색 및 조회.

| 도구 | 용도 |
|------|------|
| `search_docs` | Strands SDK 문서 검색 (에이전트 개념, 도구, 멀티에이전트 패턴 등) |
| `fetch_doc` | 특정 문서 페이지 전체 내용 조회 |

### agentcore-mcp-server

Amazon Bedrock AgentCore 문서 검색, 런타임/메모리/게이트웨이 관리.

| 도구 | 용도 |
|------|------|
| `search_agentcore_docs` | AgentCore 문서 검색 |
| `fetch_agentcore_doc` | 특정 AgentCore 문서 조회 |
| `manage_agentcore_runtime` | 에이전트 배포 및 런타임 관리 가이드 |
| `manage_agentcore_memory` | AgentCore Memory 리소스 관리 |
| `manage_agentcore_gateway` | MCP Gateway 배포 및 관리 |

## 서브 에이전트

초기화(`setup.md`) 시 3개의 서브 에이전트가 생성됩니다:

| 에이전트 | 역할 | 담당 단계 |
|----------|------|----------|
| **Planner** | 요구사항 수집, 멀티에이전트 패턴 추천, 아키텍처 설계 | Inception |
| **Developer** | Strands SDK 기반 에이전트/도구 구현, SSE 스트리밍 적용 | Construction |
| **Inspector** | 배포된 에이전트 진단, 로그 분석, 성능 최적화 | Operations |

## 멀티에이전트 패턴

**단일 에이전트 우선 원칙**: 대부분의 경우 단일 에이전트 + 여러 도구로 충분합니다.

멀티에이전트가 필요한 경우 의사결정 트리를 따릅니다:

```
독립적 하위 작업으로 분해 가능?     → Agents-as-Tools
반복적 피드백으로 결과 개선?        → Swarm
조건부 분기 + 에러 제어 필요?       → Graph
고정 파이프라인 + 병렬 실행?        → Workflow
```

## Co-Building 진행 흐름

```
Day 1-2: Inception  (기획/설계)
Day 2-4: Construction (구현/테스트)
Day 4-5: Operations (배포/검증)
```

### 일일 루틴

1. **Morning Stand-up** (15분): 전일 진행 상황, 오늘 목표, 블로커 공유
2. **Co-Building Session** (오전/오후): AI-DLC 단계에 따른 개발
3. **Daily Wrap-up** (15분): 진행 상황 정리, 내일 계획

## 상태 추적

프로젝트 루트에 `aidlc-docs/aidlc-state.md`를 생성하여 진행 상황을 추적합니다. 세션이 끊겨도 이어서 작업할 수 있습니다.

## 트러블슈팅

### Strands SDK 관련

| 에러 | 해결 방법 |
|------|----------|
| `Module 'strands' not found` | `pip install strands-agents strands-agents-tools` 실행, 가상환경 활성화 확인 |
| `Access denied to model` | Bedrock 콘솔에서 모델 접근 권한 활성화 |

### AgentCore 관련

| 에러 | 해결 방법 |
|------|----------|
| `AWS authentication failed` | `aws login` 또는 AWS credentials 설정 확인 |
| `Could not find entrypoint module` | `agentcore configure` CLI로 설정 재생성, entrypoint 경로 확인 |

### MCP 서버 연결

1. Kiro Powers 패널에서 서버 상태 확인
2. 서버 재시작 시도
3. `uvx` 명령어 설치 확인 (`pip install uv`)

## 참고 자료

| 자료 | 링크 |
|------|------|
| Agentic AI Foundations | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-foundations/ |
| Agentic AI Frameworks | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/ |
| Operationalizing Agentic AI | https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-operationalizing-agentic-ai/ |
| Serverless Architectures | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-serverless/ |
| FAST Template | https://github.com/awslabs/fullstack-solution-template-for-agentcore |
| AgentCore Samples | https://github.com/awslabs/amazon-bedrock-agentcore-samples |
| Strands SDK Docs | https://strandsagents.com/latest/documentation/ |
| Strands Samples | https://github.com/strands-agents/samples |
| AI-DLC Workflows | https://github.com/awslabs/aidlc-workflows |

---

**Program:** Agentic AI Acceleration Program
**Team:** AWS Korea AI/ML Specialist SA Team
