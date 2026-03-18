---
name: inception
description: AI-DLC Phase 1 실행 - 요구사항 분석, 멀티에이전트 패턴 선택, 아키텍처 설계. 새 에이전트 프로젝트를 기획하거나 "inception", "요구사항", "설계", "아키텍처"를 언급할 때 사용합니다.
---

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

에이전트를 설계하기 전에 다음 질문들을 고객과 함께 답합니다:

**비즈니스 요구사항:**
- 이 에이전트가 해결하려는 비즈니스 문제는 무엇인가?
- 현재 이 문제를 어떻게 처리하고 있는가?
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

`aidlc-docs/inception/requirements/requirements.md`에 다음 구조로 작성합니다:

```markdown
# [프로젝트명] 요구사항 정의서

**Project**: [프로젝트/에이전트 이름]
**Date**: [YYYY-MM-DD]

## Executive Summary
[2-3문장으로 무엇을 만들고 왜 만드는지 요약]

## Functional Requirements
### 핵심 기능
1. 사용자가 [행동]을 하면 에이전트는 [결과]를 반환한다

## Non-Functional Requirements
- 응답 시간, 보안, 비용

## Scope
- In Scope / Out of Scope

## Acceptance Criteria
- [ ] [인수 기준]
```

---

## 2. 멀티에이전트 패턴 선택

### 단일 에이전트 우선 원칙
대부분의 경우 단일 에이전트 + 여러 도구로 충분합니다.
멀티에이전트가 필요한 경우에만 아래 의사결정 프레임워크를 적용합니다.

### 패턴 의사결정 트리

```
1. 작업이 독립적인 하위 작업으로 깔끔하게 분해되는가?
   → Yes: Agents-as-Tools 고려

2. 에이전트 간 반복적 피드백/핸드오프로 결과가 개선되는가?
   → Yes: Swarm 고려

3. 정보 흐름의 방향을 엄격히 제어하거나 조건부 분기가 필요한가?
   → Yes: Graph 고려

4. 명확한 단계별 순서와 의존성이 있는 파이프라인인가?
   → Yes: Workflow 고려
```

### 패턴 상세

| 패턴 | 구조 | 적합한 시나리오 |
|------|------|---------------|
| **Agents-as-Tools** | 오케스트레이터가 전문가를 도구로 호출 | 독립적 하위 작업 분해 |
| **Swarm** | 동등한 에이전트들이 핸드오프로 협업 | 반복적 자기 개선 |
| **Graph** | 개발자가 노드/엣지를 명시적으로 설계 | 조건부 분기, 보안 제어 |
| **Workflow** | 사전 정의된 순서의 파이프라인 | 고정된 단계별 프로세스 |

**Strands SDK 공식 문서 참조:**
- Agents-as-Tools: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/agents-as-tools/
- Swarm: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/swarm/
- Graph: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/graph/
- Workflow: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/workflow/

---

## 3. 아키텍처 설계

### 모델 선택

| 모델 | 특징 | 권장 용도 |
|------|------|----------|
| Claude Sonnet 4 | 균형 잡힌 성능/비용 | 범용 에이전트 (기본 선택) |
| Claude Haiku | 빠른 응답, 저비용 | 분류, 라우팅, 간단한 추출 |
| Claude Opus | 최고 성능 | 복잡한 추론, 고위험 의사결정 |

**모델 ID 규칙**: cross-region inference profile 형식 사용
```
us.anthropic.claude-sonnet-4-20250514-v1:0
```

### 도구(Tool) 설계

```markdown
| 도구명 | 유형 | 설명 | 입력 | 출력 |
|--------|------|------|------|------|
| search_knowledge | Python Tool | 지식 베이스 검색 | query: str | List[Document] |
```

### FAST 레퍼런스 아키텍처

AWS FAST (Fullstack AgentCore Solution Template)를 핵심 레퍼런스로 참고:
- GitHub: https://github.com/awslabs/fullstack-solution-template-for-agentcore

```
사용자 → Cognito 로그인 → React 프론트엔드
                              ↓ Bearer Token
                         AgentCore Runtime
                              ↓
                    ├── AgentCore Memory
                    ├── AgentCore Gateway
                    └── AgentCore Code Interpreter
```

---

## 4. 리스크 평가

| 리스크 영역 | 확인 항목 |
|------------|----------|
| 데이터 | 학습/테스트 데이터가 충분한가? PII 처리 방안은? |
| 모델 | 선택한 모델이 요구사항 충족? Hallucination 대응 전략? |
| 통합 | 외부 시스템 API가 안정적인가? 인증/권한 관리? |
| 비용 | 예상 비용이 예산 내인가? |

---

## 5. 작업 단위 분할

각 작업은 독립적으로 개발/테스트 가능해야 합니다.
우선순위: P0(필수), P1(중요), P2(선택)

---

## Inception 완료 체크리스트

- [ ] 요구사항 정의서 작성 완료 및 승인
- [ ] 멀티에이전트 패턴 결정 (선택 이유 문서화)
- [ ] 아키텍처 설계서 작성 완료 및 승인
- [ ] 모델 선택 완료
- [ ] 도구 목록 정의 완료
- [ ] 리스크 평가 완료
- [ ] 작업 단위 분할 완료

**다음 단계:** Construction phase로 진행
