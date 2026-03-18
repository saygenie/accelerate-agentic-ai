---
name: aws-references
description: AWS 공식 문서 가이드 - Agentic AI Foundations, Frameworks, Operationalizing, Strands SDK. "AWS 문서", "Strands", "AgentCore", "AWS 가이드"를 언급할 때 사용합니다.
---

# AWS 공식 문서 가이드

AWS의 에이전트 개발 관련 핵심 공식 문서를 요약하고 참조 방법을 안내합니다.

---

## 1. Agentic AI Foundations

**URL:** https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-foundations/

에이전트 AI의 개념, 아키텍처, 설계 원칙의 기초.

### 핵심 요약

**에이전트의 정의:**
- 환경을 인지하고, 상태를 추론하며, 의도를 가지고 행동하는 실체
- 목표 지향적, 문맥 인식적 의사결정 시스템

**Three Pillars:**
- **자율성(Autonomy)**: 외부 개입 없이 독립적으로 결정/행동
- **비동기성(Asynchronicity)**: 장기 실행 작업 지원, 비동기 처리
- **에이전시(Agency)**: 목표 이해, 계획 수립, 환경 적응

**Perceive-Reason-Act 루프:**
```
인지(Perceive) → 추론(Reason) → 행동(Act) → 반복
```

### 참조 방법
에이전트 개념 이해와 아키텍처 설계 시 참조. inception 스킬의 기초 지식.

---

## 2. Agentic AI Frameworks

**URL:** https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/

Strands Agents, Bedrock Agent 등 프레임워크 비교.

### 핵심 요약

**프레임워크 비교 포인트:**
- 코드 우선 vs 노코드
- 커스터마이즈 수준
- AWS 서비스 통합 깊이
- 멀티에이전트 지원

**Strands Agents SDK 특징:**
- 코드 우선(code-first) 접근
- Python 네이티브
- MCP 네이티브 지원
- 멀티에이전트 패턴 내장 (Agents-as-Tools, Swarm, Graph, Workflow)

### 참조 방법
프레임워크 선택 시 참조. oss-frameworks 스킬에서 상세 비교.

---

## 3. Operationalizing Agentic AI

**URL:** https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-operationalizing-agentic-ai/

에이전트 운영화 전략: 거버넌스, 모니터링, 배포.

### 핵심 요약

**운영화 핵심 영역:**
- 거버넌스: 에이전트 행동 범위, 권한, 승인 정책
- 모니터링: 성능, 비용, 정확도 추적
- 배포 전략: 단계적 롤아웃, 롤백 계획
- 비용 관리: 토큰 소비, API 호출 비용 모니터링

### 참조 방법
operations 스킬에서 배포/운영 시 참조.

---

## 4. Serverless Architectures for Agentic AI

**URL:** https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-serverless/

Lambda, EventBridge 기반 서버리스 에이전트 아키텍처.

### 핵심 요약
- Lambda 기반 에이전트 실행
- EventBridge로 이벤트 기반 워크플로우
- Step Functions로 오케스트레이션
- 서버리스 에이전트의 장단점

### 참조 방법
AgentCore 외 서버리스 대안이 필요할 때 참조.

---

## 5. Strands Agents SDK

**URL:** https://strandsagents.com/latest/documentation/

Strands Agents SDK 공식 문서.

### 핵심 섹션

| 섹션 | URL | 용도 |
|------|-----|------|
| Quickstart | /docs/quickstart/ | 빠른 시작 |
| Agent | /docs/user-guide/concepts/agents/ | 에이전트 개념 |
| Tools | /docs/user-guide/concepts/tools/ | 도구 정의 |
| Model Providers | /docs/user-guide/concepts/model-providers/ | 모델 설정 |
| Multi-Agent | /docs/user-guide/concepts/multi-agent/ | 멀티에이전트 패턴 |
| Evaluation | /docs/user-guide/observability-evaluation/evaluation/ | 평가 |

### MCP 서버 활용
Strands SDK 문서는 MCP 서버(`strands-agents`)를 통해 실시간 검색 가능:
- `search_docs`: 키워드 검색
- `fetch_doc`: 특정 페이지 조회

---

## 6. 추가 레퍼런스

| 자료 | URL | 용도 |
|------|-----|------|
| FAST Template | https://github.com/awslabs/fullstack-solution-template-for-agentcore | 풀스택 레퍼런스 |
| AgentCore Samples | https://github.com/awslabs/amazon-bedrock-agentcore-samples | Memory, Gateway, A2A 예제 |
| Strands Samples | https://github.com/strands-agents/samples | 샘플 프로젝트 |
| AI-DLC Workflows | https://github.com/awslabs/aidlc-workflows | AI-DLC 방법론 |

---

## 활용 팁

1. **코딩 중 Strands 사용법이 궁금하면**: MCP 서버 `search_docs` 사용
2. **AgentCore 배포가 궁금하면**: MCP 서버 `manage_agentcore_runtime` 사용
3. **아키텍처 설계가 궁금하면**: Agentic AI Foundations 참조
4. **운영 전략이 궁금하면**: Operationalizing Agentic AI 참조
