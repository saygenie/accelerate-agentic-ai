---
name: "accelerate-agentic-ai"
displayName: "Accelerate Agentic AI"
description: "Agentic AI Acceleration Program을 위한 개발 가이드 파워. AI-DLC 기반의 적응형 에이전트 개발 라이프사이클(Inception → Construction → Operations)을 따라 프로덕션 수준의 AI 에이전트를 구축합니다."
keywords: ["accelerate", "agentic-ai", "acceleration-program", "agent-development", "co-building", "strands", "agentcore"]
author: "AWS Korea AI/ML Specialist SA Team"
---

# Accelerate Agentic AI

## Overview

Agentic AI Acceleration Program 전용 파워입니다. AWS Specialist SA와 고객 개발자가 함께 프로덕션 수준의 AI 에이전트를 구축하는 co-building 과정을 체계적으로 지원합니다.

이 파워는 AI-DLC(AI-Driven Development Life Cycle) 방법론을 차용하여 에이전트 개발을 3단계 적응형 워크플로우로 구조화합니다:

- **Inception** — 무엇을 왜 만드는지 정의 (요구사항 분석, 멀티에이전트 패턴 선택, 아키텍처 설계)
- **Construction** — 어떻게 만드는지 실행 (구현, 테스트, 품질 검증)
- **Operations** — 배포하고 운영 (AgentCore 배포, 모니터링, 프로덕션 검증)

기술 스택으로 Strands Agents SDK와 Amazon Bedrock AgentCore를 사용하며, 두 MCP 서버를 통해 문서 검색과 개발 가이드를 실시간으로 제공합니다.

## Adaptive Workflow (적응형 워크플로우)

AI-DLC의 핵심 원칙: **워크플로우가 작업에 맞춰 적응한다.**

프로젝트 복잡도에 따라 각 단계의 깊이가 달라집니다:

| 깊이 | 적용 조건 | Inception | Construction |
|------|----------|-----------|-------------|
| Minimal | 단순하고 명확한 요청 | 의도 분석만 | 바로 구현 |
| Standard | 일반적 복잡도 | 요구사항 + 아키텍처 | 설계 + 구현 + 테스트 |
| Comprehensive | 복잡하고 고위험 | 전체 분석 + 리스크 평가 | 전체 설계 + 구현 + 테스트 + 검증 |

각 단계 완료 시 **승인 게이트**를 통해 SA와 고객이 함께 리뷰하고 승인합니다.

## State Tracking (상태 추적)

프로젝트 루트에 `aidlc-docs/aidlc-state.md`를 생성하여 진행 상황을 추적합니다. 세션이 끊겨도 이어서 작업할 수 있습니다.

```markdown
# Project State
## Stage Progress
### 🔵 INCEPTION PHASE
- [x] 요구사항 분석
- [x] 멀티에이전트 패턴 선택
- [ ] 아키텍처 설계
### 🟢 CONSTRUCTION PHASE
- [ ] 프로젝트 셋업
- [ ] 에이전트 구현
- [ ] 테스트
### 🟡 OPERATIONS PHASE
- [ ] AgentCore 배포
- [ ] 모니터링 설정
```

## When to Use This Power

- Acceleration Program에 참여하는 고객과 co-building을 시작할 때
- 새로운 Agentic AI 프로젝트의 요구사항을 정리하고 설계할 때
- Strands SDK + Bedrock AgentCore 기반 에이전트를 구현할 때
- 에이전트를 AgentCore Runtime에 배포하고 모니터링할 때
- 프로그램 진행 상황을 체계적으로 관리할 때

## Available Steering Files

이 파워는 상황에 맞는 상세 가이드를 steering 파일로 제공합니다:

- **setup** — 프로젝트 초기화: 서브 에이전트(Planner, Developer, Inspector) 생성, steering 파일 구성, 상태 추적 셋업
- **program-guide** — Acceleration Program 전체 진행 가이드 (Phase 1~4: Qualification → Workshop → Co-Build → Wrap-up)
- **inception** — AI-DLC Inception 단계: 요구사항 분석, 멀티에이전트 패턴 의사결정, 아키텍처 설계
- **construction** — AI-DLC Construction 단계: FAST 아키텍처, SSE 스트리밍, 코드 구현, 테스트
- **operations** — AI-DLC Operations 단계: AgentCore 배포, Cognito 인증, 모니터링
- **best-practices** — Agentic coding 모범 사례, 멀티에이전트 패턴 의사결정 트리, Critical Rules

## Available MCP Servers

### strands-agents
Strands Agents SDK 문서 검색 및 조회.

- `search_docs` — Strands SDK 문서 검색 (에이전트 개념, 도구, 모델 프로바이더, 멀티에이전트 패턴 등)
- `fetch_doc` — 특정 문서 페이지 전체 내용 조회

### agentcore-mcp-server
Amazon Bedrock AgentCore 문서 검색, 런타임/메모리/게이트웨이 관리.

- `search_agentcore_docs` — AgentCore 문서 검색
- `fetch_agentcore_doc` — 특정 AgentCore 문서 조회
- `manage_agentcore_runtime` — 에이전트 배포 및 런타임 관리 가이드
- `manage_agentcore_memory` — AgentCore Memory 리소스 관리
- `manage_agentcore_gateway` — MCP Gateway 배포 및 관리

## MCP 서버 활용 원칙

개발 중 정보가 필요할 때 **MCP 서버를 우선 사용**합니다:

| 주제 | MCP 도구 | 용도 |
|------|---------|------|
| Strands SDK | `search_docs`, `fetch_doc` | 에이전트 구현, 도구 정의, 멀티에이전트 패턴 |
| AgentCore | `search_agentcore_docs`, `fetch_agentcore_doc` | 배포, Memory, Gateway 통합 |
| AgentCore Runtime | `manage_agentcore_runtime` | 배포 요구사항, CLI 워크플로우 |
| AgentCore Memory | `manage_agentcore_memory` | Memory 리소스 생성, CLI 명령어 |
| AgentCore Gateway | `manage_agentcore_gateway` | Gateway 배포, 도구 변환 |

MCP 도구로 접근 불가한 경우에만 웹 검색을 사용합니다.

## Quick Start

### 0. 프로젝트 초기화 (최초 1회)
Power 설치 후 프로젝트에서 처음 개발을 시작할 때:
```
readSteering("accelerate-agentic-ai", "setup.md")
```
→ 서브 에이전트(Planner, Developer, Inspector), steering 파일, 상태 추적 파일이 자동 생성됩니다.

### 1. 프로그램 시작
프로그램 진행 가이드를 읽어 현재 Phase를 확인합니다:
```
readSteering("accelerate-agentic-ai", "program-guide.md")
```

### 2. 에이전트 개발 시작
AI-DLC 단계에 따라 진행합니다:

**Inception (기획/설계):**
```
readSteering("accelerate-agentic-ai", "inception.md")
```

**Construction (구현/테스트):**
```
readSteering("accelerate-agentic-ai", "construction.md")
```

**Operations (배포/운영):**
```
readSteering("accelerate-agentic-ai", "operations.md")
```

### 3. 모범 사례 참고
```
readSteering("accelerate-agentic-ai", "best-practices.md")
```

## Tech Stack

| 구성 요소 | 기술 | 용도 |
|-----------|------|------|
| Agent SDK | Strands Agents SDK | 에이전트 개발 프레임워크 |
| Runtime | Bedrock AgentCore | 에이전트 배포 및 운영 |
| Model | Bedrock (Claude Sonnet 4, Nova 등) | LLM 추론 |
| Memory | AgentCore Memory | 에이전트 상태 및 지식 관리 |
| Gateway | AgentCore Gateway | API를 에이전트 도구로 변환 |
| IDE | Kiro | Agentic coding 환경 |
| Frontend | React + Vite + TypeScript + Tailwind | UI (필요 시) |
| DB | SQLite (로컬) → DynamoDB (프로덕션) | 데이터 저장 |

## AWS 참고 자료

| 자료 | 링크 | 용도 |
|------|------|------|
| Agentic AI Foundations | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-foundations/ | 에이전트 개념 및 아키텍처 기초 |
| Agentic AI Frameworks | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/ | Strands, Bedrock Agent 비교 |
| Operationalizing Agentic AI | https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-operationalizing-agentic-ai/ | 거버넌스, 모니터링, 배포 전략 |
| Serverless Architectures | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-serverless/ | Lambda, EventBridge 기반 아키텍처 |
| FAST Template | https://github.com/awslabs/fullstack-solution-template-for-agentcore | Fullstack AgentCore 레퍼런스 |
| AgentCore Samples | https://github.com/awslabs/amazon-bedrock-agentcore-samples | Memory, Gateway, A2A 예제 |
| Strands SDK Docs | https://strandsagents.com/latest/documentation/ | 공식 SDK 문서 |
| Strands Samples | https://github.com/strands-agents/samples | 샘플 프로젝트 |
| AI-DLC Workflows | https://github.com/awslabs/aidlc-workflows | AI-DLC 방법론 |

## Troubleshooting

### Strands SDK 관련

**Error: "Module 'strands' not found"**
- `pip install strands-agents strands-agents-tools` 실행
- 가상환경이 활성화되어 있는지 확인

**Error: "Access denied to model"**
- Bedrock 콘솔에서 모델 접근 권한 활성화 필요
- 사용할 모델(예: Claude Sonnet 4)을 Model access에서 활성화

### AgentCore 관련

**Error: "AWS authentication failed"**
- `aws login` 또는 AWS credentials 설정 확인
- AgentCore CLI 설치: `pip install "bedrock-agentcore-starter-toolkit>=0.1.21"`

**Error: "Could not find entrypoint module"**
- `.bedrock_agentcore.yaml`은 `agentcore configure` CLI로 생성 (수동 작성 금지)
- entrypoint 모듈 경로가 올바른지 확인

### MCP 서버 연결

**MCP 서버가 시작되지 않는 경우:**
1. Kiro Powers 패널에서 서버 상태 확인
2. 서버 재시작 시도
3. `uvx` 명령어가 설치되어 있는지 확인 (`pip install uv`)

---

**Program:** Agentic AI Acceleration Program
**Team:** AWS Korea AI/ML Specialist SA Team
