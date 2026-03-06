# AI-DLC Inception 단계

무엇을(WHAT) 왜(WHY) 만드는지 정의하는 단계입니다.
요구사항을 분석하고, 멀티에이전트 패턴을 선택하고, 아키텍처를 설계합니다.

이 단계는 프로젝트 복잡도에 따라 **적응형 깊이**로 실행됩니다:
- **Minimal**: 단순하고 명확한 요청 → 의도 분석 + 간단한 설계
- **Standard**: 일반적 복잡도 → 요구사항 수집 + 아키텍처 설계
- **Comprehensive**: 복잡하고 고위험 → 전체 분석 + 리스크 평가 + 상세 설계

---

## Inception 단계 흐름

```
1. 요구사항 분석 → 2. 멀티에이전트 패턴 선택 → 3. 아키텍처 설계 → 4. 리스크 평가 → 5. 작업 단위 분할
```

각 단계 완료 시 **승인 게이트**를 통해 SA와 고객이 함께 리뷰합니다.

---

## 1. 요구사항 분석 (Requirements Analysis)

### 1.1 의도 분석 (Intent Analysis)

먼저 요청의 성격을 파악합니다:

- **요청 명확도**: 명확(Clear) / 모호(Vague) / 불완전(Incomplete)
- **요청 유형**: 신규 에이전트 / 기존 에이전트 개선 / 마이그레이션
- **범위 추정**: 단일 에이전트 / 멀티에이전트 / 시스템 전체
- **복잡도 추정**: 단순(Trivial) / 보통(Moderate) / 복잡(Complex)

### 1.2 고객 요구사항 수집

에이전트를 설계하기 전에 다음 질문들을 고객과 함께 답합니다.
구조화된 질문 형식을 사용하여 명확한 답변을 수집합니다:

```markdown
## Question: 에이전트 목적
이 에이전트가 해결하려는 핵심 비즈니스 문제는 무엇인가요?

A) 고객 서비스 자동화 (문의 분류, 답변 생성, 에스컬레이션)
B) 데이터 분석 및 인사이트 (리포트 생성, 패턴 분석, 예측)
C) 업무 프로세스 자동화 (워크플로우 실행, 승인, 알림)
D) 의사결정 지원 (추천, 최적화, 시뮬레이션)
E) 기타 (아래에 설명)

[Answer]:
```

**비즈니스 요구사항:**
- 이 에이전트가 해결하려는 비즈니스 문제는 무엇인가?
- 현재 이 문제를 어떻게 처리하고 있는가? (수동 프로세스, 기존 시스템 등)
- 성공 기준은 무엇인가? (응답 시간, 정확도, 처리량 등)
- 에이전트의 최종 사용자는 누구인가?

**기술 요구사항:**
- 에이전트가 접근해야 하는 데이터 소스는? (DB, API, 파일 등)
- 기존 시스템과의 통합 포인트는?
- 보안 및 컴플라이언스 요구사항은?
- 예상 트래픽/사용량은?

**에이전트 행동 요구사항:**
- 에이전트가 수행해야 하는 핵심 작업(Task)은?
- 에이전트가 사용해야 하는 도구(Tool)는?
- 멀티턴 대화가 필요한가, 단일 요청-응답인가?
- 에이전트의 자율성 수준은? (완전 자동 vs 사람 승인 필요)

### 1.3 요구사항 문서 템플릿

```markdown
# [프로젝트명] 요구사항 정의서

**Project**: [프로젝트/에이전트 이름]
**Date**: [YYYY-MM-DD]
**Author**: [작성자]
**Stakeholder**: [고객 담당자]

---

## Executive Summary
[2-3문장으로 무엇을 만들고 왜 만드는지 요약]

## Background and Context

### 문제 정의 (Problem Statement)
[어떤 문제를 해결하는가? 어떤 페인 포인트가 있는가?]

### 현재 상태 (Current State)
[현재 어떻게 처리하고 있는가? 수동 프로세스, 기존 도구의 한계는?]

### 목표 상태 (Desired Future State)
[이 에이전트가 만들어진 후 무엇이 달라지는가?]

### 성공 기준 (Success Criteria)
[어떻게 성공을 측정하는가? 정량적 지표는?]

---

## Functional Requirements

### 핵심 기능
1. **[기능명]**
   - 사용자가 [행동]을 하면
   - 에이전트는 [결과]를 반환한다
   - 지원 조건: [조건]

### 사용자 상호작용 (User Interactions)
[사용자가 에이전트와 어떻게 상호작용하는지 구체적 대화 흐름]

```
사용자: "[예시 질문]"
에이전트: [도구 호출] "[예시 응답]"
```

### 엣지 케이스 (Edge Cases)
- [비정상 입력 처리]
- [외부 서비스 장애 시 처리]
- [모호한 요청 처리]

---

## Non-Functional Requirements

### 성능 (Performance)
- 응답 시간: [목표]
- 동시 사용자: [목표]

### 보안 (Security)
- PII 처리 방안
- API 키/시크릿 관리 (AWS Secrets Manager)
- IAM 최소 권한 원칙

### 비용 (Cost)
- 월간 예상 비용
- 토큰 소비 모니터링
- 캐싱 전략

---

## Technical Requirements

### 플랫폼
- Strands Agents SDK + Bedrock AgentCore
- Python 3.12+
- 모델: [선택한 모델]

### 통합 (Integrations)
- [외부 서비스/API 목록]

### 데이터 요구사항
- [필요한 데이터, 출처, 형식]

### 제약 조건
- [기술적 제약]
- [비용 제약]
- [일정 제약]

---

## Scope

### In Scope
- [포함되는 기능 목록]

### Out of Scope
- [명시적으로 제외되는 기능]

### Future Considerations
- [향후 추가 가능한 기능]

---

## Risks and Mitigation

| 리스크 | 영향 | 가능성 | 대응 방안 |
|--------|------|--------|----------|
| [리스크] | 높음/중간/낮음 | 높음/중간/낮음 | [대응] |

---

## Open Questions
1. [아직 결정되지 않은 사항]
2. [추가 확인이 필요한 사항]

---

## Acceptance Criteria
- [ ] [인수 기준 1]
- [ ] [인수 기준 2]
```

### ⛔ 승인 게이트: 요구사항 리뷰
요구사항 문서를 SA와 고객이 함께 리뷰합니다. 승인 후 다음 단계로 진행합니다.

---

## 2. 멀티에이전트 패턴 선택

### 2.1 핵심 질문: 단일 에이전트로 충분한가?

**단일 에이전트 우선 원칙**: 대부분의 경우 단일 에이전트 + 여러 도구로 충분합니다.
멀티에이전트가 필요한 경우에만 아래 의사결정 프레임워크를 적용합니다.

### 2.2 패턴 의사결정 트리

다음 질문을 순서대로 적용합니다:

```
1. 작업이 독립적인 하위 작업으로 깔끔하게 분해되는가?
   → Yes: Agents-as-Tools 고려
   → No: 다음 질문으로

2. 에이전트 간 반복적 피드백/핸드오프로 결과가 개선되는가?
   → Yes: Swarm 고려
   → No: 다음 질문으로

3. 정보 흐름의 방향을 엄격히 제어하거나 조건부 분기가 필요한가?
   → Yes: Graph 고려
   → No: 다음 질문으로

4. 명확한 단계별 순서와 의존성이 있는 파이프라인인가?
   → Yes: Workflow 고려

5. 복합 패턴이 필요한가?
   → 복잡한 실제 문제는 단일 패턴으로 해결되지 않을 수 있다.
     패턴 조합을 적극 고려하라.
```

### 2.3 패턴 상세

#### Agents-as-Tools (계층적 위임)
- **구조**: 오케스트레이터(매니저)가 전문가 에이전트를 도구처럼 호출하고 결과를 통합
- **적합한 시나리오**:
  - 사용자 요청이 독립적인 하위 작업으로 자연스럽게 분해되는 경우
  - 각 하위 작업이 서로 다른 전문성을 요구하는 경우
  - 최종 결과를 하나의 통합된 응답으로 합쳐야 하는 경우
- **예시**: 멀티도메인 어시스턴트, 고객 이탈 분석(감지→진단→개입→추적)
- **Strands SDK**: `agent.as_tool()` 또는 `@tool` 래퍼

```python
@tool
def specialist_agent(query: str) -> str:
    """도메인 특화 쿼리를 처리합니다."""
    agent = Agent(system_prompt=SPECIALIST_PROMPT, tools=[domain_tool])
    return str(agent(query))

orchestrator = Agent(tools=[specialist_agent, other_specialist])
```

#### Swarm (탈중앙 협업)
- **구조**: 동등한 에이전트들이 중앙 통제 없이 핸드오프로 협업
- **적합한 시나리오**:
  - 다양한 관점에서 동시에 탐색해야 하는 브레인스토밍
  - 반복적 자기 개선(비평→수정→재비평)이 품질을 높이는 경우
  - 하나의 에이전트가 실패해도 다른 에이전트가 보완할 수 있는 경우
- **예시**: 금융 분석(리서치→분석→리포트 핸드오프), 콘텐츠 생성

```python
from strands.multiagent.swarm import Swarm
swarm = Swarm(agents=[analyst, researcher, writer])
result = swarm("이 문제를 분석해주세요")
```

#### Graph (구조화된 네트워크)
- **구조**: 개발자가 에이전트 간 방향성 연결(엣지)을 명시적으로 설계
- **적합한 시나리오**:
  - 조건부 분기(conditional edge)가 필요한 복잡한 의사결정 트리
  - 보안상 특정 에이전트만 외부 도구/API에 접근해야 하는 경우
  - 팩트체커가 항상 생성 에이전트의 출력을 검증해야 하는 경우
- **예시**: 고객 문의 라우팅, 기업 의사결정(재무+기술+사회영향 분석 병렬 후 통합)

```python
from strands.multiagent.graph import GraphBuilder, Node
graph = GraphBuilder()
graph.add_node(Node("validate", validate_agent))
graph.add_edge("validate", "process", condition=lambda state: state["valid"])
```

#### Workflow (파이프라인/DAG)
- **구조**: 사전 정의된 순서와 의존성에 따라 에이전트가 단계별로 실행
- **적합한 시나리오**:
  - 명확한 단계별 구조가 있는 프로세스 (입력→처리→검증→출력)
  - 독립적인 단계를 병렬 실행하여 효율을 높일 수 있는 경우
  - 일시 중지/재개가 필요한 장기 실행 프로세스
- **예시**: 문서 처리 파이프라인(IDP), 채용 심사

**중요: 모든 아이디어를 Agents-as-Tools로 구현하지 말 것. 각 문제의 구조를 분석하고 가장 적합한 패턴을 선택하라.**

Strands SDK 공식 문서 참고:
- Agents-as-Tools: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/agents-as-tools/
- Workflow: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/workflow/
- Graph: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/graph/
- Swarm: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/swarm/

---

## 3. 아키텍처 설계

### 3.1 모델 선택

| 모델 | 특징 | 권장 용도 |
|------|------|----------|
| Claude Sonnet 4 | 균형 잡힌 성능/비용 | 범용 에이전트 (기본 선택) |
| Claude Haiku | 빠른 응답, 저비용 | 분류, 라우팅, 간단한 추출 |
| Claude Opus | 최고 성능 | 복잡한 추론, 고위험 의사결정 |
| Nova Pro | AWS 네이티브, 비용 효율 | 일반 작업 |
| Nova Lite | 초저비용 | 간단한 작업 |

**모델 ID 규칙**: cross-region inference profile 형식 사용
```
us.anthropic.claude-sonnet-4-20250514-v1:0
```
`bedrock/` 접두사를 절대 붙이지 않는다.

### 3.2 도구(Tool) 설계

```markdown
## Tool Inventory

| 도구명 | 유형 | 설명 | 입력 | 출력 |
|--------|------|------|------|------|
| search_knowledge | Python Tool | 지식 베이스 검색 | query: str | List[Document] |
| get_customer_info | MCP Tool | 고객 정보 조회 | customer_id: str | CustomerInfo |
| send_notification | Python Tool | 알림 발송 | message: str, channel: str | bool |
```

### 3.3 메모리 전략

| 메모리 유형 | 용도 | AgentCore 지원 |
|------------|------|---------------|
| Conversation Memory | 대화 히스토리 유지 | Strands 기본 제공 |
| Semantic Memory | 장기 지식 저장/검색 | AgentCore Memory |
| Event Memory | 이벤트 기반 기억 | AgentCore Memory |

### 3.4 FAST 레퍼런스 아키텍처

AWS FAST (Fullstack AgentCore Solution Template)를 핵심 레퍼런스로 참고합니다:
- GitHub: https://github.com/awslabs/fullstack-solution-template-for-agentcore

```
사용자 → Cognito 로그인 → React 프론트엔드 (Amplify Hosting)
                              ↓ Bearer Token (Streamable HTTP)
                         AgentCore Runtime
                              ↓
                    ├── AgentCore Memory (세션 유지)
                    ├── AgentCore Gateway (외부 도구)
                    └── AgentCore Code Interpreter
```

### 3.5 아키텍처 설계 문서 템플릿

```markdown
# [프로젝트명] 아키텍처 설계서

## 1. 시스템 개요
- 아키텍처 다이어그램 (Mermaid)
- 주요 컴포넌트 설명

## 2. 에이전트 설계
- 에이전트 패턴: [선택한 패턴] — 선택 이유
- 모델: [선택한 모델]
- System Prompt 초안

## 3. 도구 설계
- Tool Inventory
- 각 도구의 상세 스펙

## 4. 데이터 흐름
- 입력 → 처리 → 출력 흐름
- 에러 처리 흐름

## 5. 인프라 설계
- 배포 환경 (AgentCore Runtime)
- 네트워크 구성
- 보안 설정 (Cognito, IAM)
```

### ⛔ 승인 게이트: 아키텍처 리뷰
아키텍처 설계서를 SA와 고객이 함께 리뷰합니다. 승인 후 다음 단계로 진행합니다.

---

## 4. 리스크 평가

### 리스크 체크리스트

| 리스크 영역 | 확인 항목 | 상태 |
|------------|----------|------|
| 데이터 | 학습/테스트 데이터가 충분한가? | ☐ |
| 데이터 | PII/민감 데이터 처리 방안이 있는가? | ☐ |
| 모델 | 선택한 모델이 요구사항을 충족하는가? | ☐ |
| 모델 | Hallucination 대응 전략이 있는가? | ☐ |
| 통합 | 외부 시스템 API가 안정적인가? | ☐ |
| 통합 | 인증/권한 관리가 설계되었는가? | ☐ |
| 비용 | 예상 비용이 예산 내인가? | ☐ |
| 보안 | 보안 검토가 완료되었는가? | ☐ |

---

## 5. 작업 단위 분할

### 작업 분할 원칙
- 각 작업은 독립적으로 개발/테스트 가능해야 함
- 작업 간 의존성을 최소화
- 우선순위 지정 (P0: 필수, P1: 중요, P2: 선택)

```markdown
## 작업 목록

### P0 (필수)
- [ ] Task 1: 기본 에이전트 구조 구현 (예상: 2시간)
- [ ] Task 2: 핵심 도구 구현 (예상: 4시간)
- [ ] Task 3: System Prompt 작성 및 튜닝 (예상: 2시간)

### P1 (중요)
- [ ] Task 4: 에러 처리 및 폴백 로직 (예상: 2시간)
- [ ] Task 5: 테스트 작성 (예상: 3시간)

### P2 (선택)
- [ ] Task 6: 성능 최적화 (예상: 2시간)
- [ ] Task 7: 모니터링 대시보드 (예상: 2시간)
```

---

## Inception 완료 체크리스트

- [ ] 요구사항 정의서 작성 완료 및 승인
- [ ] 멀티에이전트 패턴 결정 (선택 이유 문서화)
- [ ] 아키텍처 설계서 작성 완료 및 승인
- [ ] 모델 선택 완료
- [ ] 도구 목록 정의 완료
- [ ] 리스크 평가 완료
- [ ] 작업 단위 분할 완료

**다음 단계:** `readSteering("accelerate-agentic-ai", "construction.md")`
