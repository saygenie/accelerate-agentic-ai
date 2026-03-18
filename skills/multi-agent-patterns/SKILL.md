---
name: multi-agent-patterns
description: 멀티에이전트 패턴 심화 가이드 - Anthropic 5패턴 + Strands 4패턴 통합. "멀티에이전트", "패턴", "multi-agent", "오케스트레이터", "swarm", "graph"를 언급할 때 사용합니다.
---

# 멀티에이전트 패턴 심화 가이드

Anthropic의 5가지 워크플로우 패턴과 Strands SDK의 4가지 멀티에이전트 패턴을 통합합니다.

---

## 패턴 선택 결정 트리

```
단일 에이전트 + 도구로 충분한가?
  → Yes: 단일 에이전트 (대부분의 경우)
  → No: 아래 진행

작업이 순차적 단계로 분해 가능한가?
  → Yes: Prompt Chaining / Workflow

입력 유형별로 다른 처리가 필요한가?
  → Yes: Routing

독립적 작업을 동시에 실행해야 하는가?
  → Yes: Parallelization

예측 불가능한 하위 작업이 필요한가?
  → Yes: Orchestrator-Workers / Agents-as-Tools

반복적 피드백으로 품질 개선이 필요한가?
  → Yes: Evaluator-Optimizer / Swarm

조건부 분기와 엄격한 흐름 제어가 필요한가?
  → Yes: Graph
```

---

## Anthropic 5가지 패턴

### 1. Prompt Chaining (순차 체이닝)

작업을 순차적 단계로 분해, 각 LLM 호출이 이전 출력을 처리.

```
[입력] → LLM 1 (분석) → 게이트 → LLM 2 (생성) → LLM 3 (검증) → [출력]
```

**사용 사례:** 문서 생성 (개요 → 작성 → 검증), 데이터 처리 파이프라인
**장점:** 각 단계가 단순해져 정확도 향상, 중간 게이트로 품질 제어

### 2. Routing (라우팅)

입력을 분류하여 특화된 후속 작업으로 분기.

```
[입력] → 분류기 → 유형A → 전문가A
                → 유형B → 전문가B
                → 유형C → 전문가C
```

**사용 사례:** 고객 문의 분류, 모델 비용-성능 최적화
**장점:** 유형별 최적화, 비용 절감 (단순 작업에 작은 모델 사용)

### 3. Parallelization (병렬화)

**섹셔닝:** 독립 작업 동시 실행 → 결과 통합
**투표:** 동일 작업 여러 번 실행 → 다수결/종합

**사용 사례:** 콘텐츠 스크리닝 (병렬 검사), 코드 리뷰 (다중 관점)

### 4. Orchestrator-Workers (오케스트레이터-워커)

중앙 LLM이 작업 분해 → 워커에 위임 → 결과 종합.

**사용 사례:** 복수 파일 수정, 다중 소스 정보 수집
**특징:** 예측 불가능한 세부 작업에 적합

### 5. Evaluator-Optimizer (평가자-최적화자)

생성 LLM과 평가 LLM이 반복적으로 품질 개선.

**조건:** 명확한 평가 기준 존재, 피드백으로 개선 가능
**사용 사례:** 번역 품질 개선, 복합 검색

---

## Strands SDK 4가지 패턴

### 1. Agents-as-Tools (계층적 위임)

오케스트레이터가 전문가 에이전트를 도구처럼 호출.

```python
from strands import Agent
from strands.types.tools import tool

@tool
def risk_scorer(query: str) -> str:
    """고객 이탈 위험도를 스코어링합니다."""
    agent = Agent(system_prompt=RISK_PROMPT, tools=[score_tool])
    return str(agent(query))

orchestrator = Agent(tools=[risk_scorer, diagnosis_agent])
```

**적합:** 독립적 하위 작업, 각 작업이 다른 전문성 요구
**참고:** https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/agents-as-tools/

### 2. Swarm (탈중앙 협업)

동등한 에이전트들이 중앙 통제 없이 핸드오프로 협업.

```python
from strands.multiagent.swarm import Swarm
swarm = Swarm(agents=[analyst, researcher, writer])
result = swarm("이 문제를 분석해주세요")
```

**적합:** 반복적 자기 개선, 다양한 관점 탐색
**참고:** https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/swarm/

### 3. Graph (구조화된 네트워크)

개발자가 에이전트 간 방향성 연결(엣지)을 명시적으로 설계.

```python
from strands.multiagent.graph import GraphBuilder, Node
graph = GraphBuilder()
graph.add_node(Node("validate", validate_agent))
graph.add_edge("validate", "process", condition=lambda state: state["valid"])
```

**적합:** 조건부 분기, 보안 제어, 팩트체킹
**참고:** https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/graph/

### 4. Workflow (파이프라인/DAG)

사전 정의된 순서와 의존성에 따라 에이전트가 단계별로 실행.

**적합:** 고정된 프로세스, 병렬 실행, 장기 실행 프로세스
**참고:** https://strandsagents.com/latest/documentation/docs/user-guide/concepts/multi-agent/workflow/

---

## 패턴 매핑 (Anthropic ↔ Strands)

| Anthropic 패턴 | Strands 대응 | 비고 |
|---------------|-------------|------|
| Prompt Chaining | Workflow | 순차 파이프라인 |
| Routing | Graph | 조건부 분기 |
| Parallelization | Workflow (병렬) | 독립 작업 병렬 |
| Orchestrator-Workers | Agents-as-Tools | 위임 + 통합 |
| Evaluator-Optimizer | Swarm / Graph (사이클) | 반복 개선 |

---

## 안티패턴

1. **모든 것을 Agents-as-Tools로 해결하려는 유혹** → 문제 구조를 분석하고 적합한 패턴 선택
2. **불필요한 멀티에이전트** → 단일 에이전트로 해결 가능한지 먼저 검증
3. **패턴 선택 이유 미문서화** → 반드시 이유를 기록
