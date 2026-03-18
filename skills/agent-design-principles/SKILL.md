---
name: agent-design-principles
description: 좋은 에이전트를 설계하는 핵심 원칙 - Anthropic과 AWS 공식 문서 기반. "에이전트 설계", "설계 원칙", "좋은 에이전트", "agent design"을 언급할 때 사용합니다.
---

# 에이전트 설계 원칙

Anthropic "Building Effective Agents"와 AWS "Agentic AI Foundations" 공식 문서의 핵심 원칙을 통합합니다.

---

## 핵심 원칙 3가지 (Anthropic)

### 1. 단순성 우선 (Simplicity First)

복잡한 프레임워크보다 기본 API부터 시작하라.
증명된 필요성이 있을 때만 복잡성을 추가하라.

**실천 방법:**
- 단일 에이전트 + 여러 도구로 시작
- 멀티에이전트는 단일 에이전트로 해결 불가능할 때만 도입
- 평가(evaluation) 없이는 복잡성 추가 금지
- "내 에이전트에 이 기능이 정말 필요한가?"를 항상 자문

### 2. 투명성 (Transparency)

에이전트의 계획 단계와 추론 과정을 명시적으로 표시하라.

**실천 방법:**
- 도구 호출 전 "왜 이 도구를 선택했는지" 로깅
- 중간 결과를 사용자에게 표시 (SSE 스트리밍 활용)
- 실패 시 원인과 대안을 명확히 전달
- System Prompt에 추론 과정 표시 지시 포함

### 3. ACI 설계 (Agent-Computer Interface)

도구 문서화에 UI 설계만큼 투자하라.

**실천 방법:**
- 도구 docstring에 사용 시나리오, 엣지 케이스, 다른 도구와의 경계 명시
- 파라미터명과 설명으로 모호성 제거
- Poka-yoke 원칙: 실수할 수 없는 구조 설계 (상대 경로 대신 절대 경로 등)
- 실제 사용자(LLM) 관점에서 도구 사용이 직관적인지 테스트

---

## 에이전트 vs 워크플로우

| 항목 | 워크플로우 | 에이전트 |
|------|----------|--------|
| 실행 경로 | 사전 정의된 코드 경로 | LLM이 동적으로 결정 |
| 적합성 | 잘 정의된 작업 | 개방형 문제 |
| 예측성 | 높음 | 낮음 |
| 비용 | 낮음 | 높음 |

**잘 정의된 작업에는 워크플로우**, 유연성이 필요하면 에이전트를 선택한다.

---

## Anthropic 5가지 워크플로우 패턴

### 1. Prompt Chaining (순차 체이닝)
작업을 순차적 단계로 분해, 각 LLM 호출이 이전 출력을 처리.
- **적합**: 마케팅 카피 생성 후 번역, 문서 개요 → 검증 → 작성
- **장점**: 각 단계를 단순화하여 정확도 향상

### 2. Routing (라우팅)
입력을 분류하여 특화된 후속 작업으로 지시.
- **적합**: 고객 문의 유형별 분류, 모델 비용-성능 최적화
- **장점**: 입력 유형별 최적화

### 3. Parallelization (병렬화)
- **섹셔닝**: 독립 작업 병렬 실행 (콘텐츠 스크리닝)
- **투표**: 동일 작업 다중 실행 (코드 취약점 검토)

### 4. Orchestrator-Workers (오케스트레이터-워커)
중앙 LLM이 작업 분해 → 워커에 위임 → 결과 종합.
- **적합**: 예측 불가능한 세부 작업이 필요한 경우

### 5. Evaluator-Optimizer (평가자-최적화자)
한 LLM은 응답 생성, 다른 LLM은 피드백 제공 (반복).
- **조건**: 명확한 평가 기준 존재, 피드백으로 개선 가능

---

## AWS Three Pillars

### 자율성 (Autonomy)
에이전트는 외부 개입 없이 독립적으로 결정하고 행동할 수 있어야 한다.

### 비동기성 (Asynchronicity)
장기 실행 작업을 지원하고, 결과를 비동기적으로 처리할 수 있어야 한다.

### 에이전시 (Agency)
에이전트는 목표를 이해하고, 계획을 수립하고, 환경에 적응할 수 있어야 한다.

---

## Perceive-Reason-Act 루프

모든 에이전트의 핵심 동작 패턴:

```
1. Perceive (인지): 환경에서 정보를 수집 (사용자 입력, 도구 결과, 컨텍스트)
2. Reason (추론): 수집된 정보를 바탕으로 다음 행동을 결정 (LLM 추론)
3. Act (행동): 결정된 행동을 실행 (도구 호출, 응답 생성)
→ 루프 반복: 행동 결과를 다시 인지하여 다음 추론에 반영
```

**Strands SDK 구현:**
```python
agent = Agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=[search_tool, action_tool],
)
# agent(prompt) 호출 시 자동으로 Perceive-Reason-Act 루프 실행
result = agent("고객 C001의 이탈 위험을 분석해주세요")
```

---

## 실제 적용 가이드

### 에이전트 프로젝트 시작 시 체크리스트

1. **단일 에이전트로 충분한가?** → 대부분 Yes
2. **평가 기준이 명확한가?** → 없으면 먼저 정의
3. **도구 문서화가 충분한가?** → LLM이 올바르게 선택할 수 있어야 함
4. **실패 시나리오를 고려했는가?** → 폴백과 에러 처리 설계
5. **비용 추정을 했는가?** → 토큰 소비와 API 호출 비용

### 참고 문서

- Anthropic "Building Effective Agents": https://www.anthropic.com/engineering/building-effective-agents
- AWS Agentic AI Foundations: https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-foundations/
