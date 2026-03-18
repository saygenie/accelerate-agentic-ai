---
name: evaluation
description: 에이전트 평가 방법 - 성능 측정, 품질 검증, 비용 최적화. "평가", "evaluation", "테스트", "성능", "품질", "비용 최적화"를 언급할 때 사용합니다.
---

# 에이전트 평가 방법

**평가 없이는 복잡성 추가 금지.** 이것이 Anthropic이 강조하는 에이전트 개발의 핵심 원칙입니다.

---

## 평가의 중요성

에이전트에 새로운 기능이나 복잡성을 추가하기 전에 반드시:
1. 현재 성능의 **기준선(baseline)**을 측정하고
2. 변경 후 성능이 **실제로 개선되었는지** 검증한다

측정할 수 없으면 개선할 수 없다.

---

## 평가 유형

### 1. Manual Evaluation (수동 평가)

가장 기본적이지만 가장 중요한 평가 방법.

```python
# 직접 에이전트를 호출하여 응답 품질 확인
agent = Agent(model=model, system_prompt=PROMPT, tools=tools)
result = agent("고객 C001의 이탈 위험을 분석해주세요")
print(result)
```

**평가 관점:**
- 응답이 정확한가?
- 올바른 도구를 선택했는가?
- 불필요한 도구 호출이 없는가?
- 응답 형식이 적절한가?
- 범위 밖 요청을 적절히 거부하는가?

### 2. Structured Testing (구조화 테스트)

```python
# tests/test_tools.py
import pytest

def test_get_customer_info():
    result = get_customer_info("C001")
    assert "name" in result
    assert result["name"] != ""

def test_get_customer_info_invalid():
    result = get_customer_info("")
    assert "error" in result
```

### 3. LLM Judge (LLM 판정)

다른 LLM을 사용하여 에이전트 응답 품질을 평가.

```python
judge_prompt = """
다음 에이전트 응답을 평가하세요.

질문: {question}
응답: {response}

평가 기준:
1. 정확성 (1-5): 정보가 정확한가?
2. 완전성 (1-5): 필요한 정보를 모두 포함하는가?
3. 관련성 (1-5): 질문에 적절히 답하는가?

JSON으로 점수와 이유를 반환하세요.
"""
```

### 4. Tool Selection Accuracy (도구 선택 정확도)

```python
# record_direct_tool_call=True로 도구 호출 패턴 분석
agent = Agent(
    model=model,
    tools=tools,
    record_direct_tool_call=True
)
```

---

## 성능 측정 지표

### 응답 품질 지표

| 지표 | 설명 | 측정 방법 |
|------|------|----------|
| 정확도 | 올바른 답변 비율 | 수동 평가 / LLM Judge |
| 도구 선택 정확도 | 올바른 도구 선택 비율 | 로그 분석 |
| Hallucination 비율 | 사실이 아닌 정보 비율 | 수동 평가 |
| 범위 외 거부율 | 범위 밖 요청 거부 비율 | 테스트 케이스 |

### 성능 지표

| 지표 | 설명 | 목표 |
|------|------|------|
| 응답 시간 (TTFT) | 첫 토큰까지 시간 | < 2초 |
| 총 응답 시간 | 전체 응답 완료 시간 | 요구사항에 따라 |
| 토큰 소비 | 입력+출력 토큰 수 | 최소화 |
| 도구 호출 수 | 요청당 도구 호출 수 | 최소화 |

### 비용 지표

| 지표 | 설명 |
|------|------|
| 요청당 비용 | 토큰 비용 + API 비용 |
| 월간 예상 비용 | 예상 트래픽 기반 |
| 비용 대비 정확도 | 비용 효율성 |

---

## 비용 최적화 전략

### 모델 선택 최적화
```
분류/라우팅 → Claude Haiku 또는 Nova Lite (저비용)
범용 작업 → Claude Sonnet 4 (균형)
복잡한 추론 → Claude Opus (고성능)
```

### 토큰 최적화
- System Prompt를 간결하게 유지
- `cachePoint`를 활용하여 반복 프롬프트 캐싱 (1024+ 토큰)
- 긴 컨텍스트는 요약 후 전달
- 불필요한 도구 제거

```python
from strands.types.content import SystemContentBlock

system_prompt_blocks = [
    SystemContentBlock(text=long_system_prompt),
    SystemContentBlock(cachePoint={"type": "ephemeral"}),
]
```

### 도구 호출 최적화
- 필요한 정보를 한 번에 가져오는 도구 설계
- 병렬 도구 호출 활용 (Strands SDK 지원)
- 불필요한 도구 호출 최소화 (docstring 개선)

---

## A/B 테스트 방법

```python
# 두 가지 System Prompt 비교
prompt_a = "버전 A 프롬프트..."
prompt_b = "버전 B 프롬프트..."

test_cases = [
    "고객 C001 정보를 알려주세요",
    "지난 달 매출을 분석해주세요",
    ...
]

results_a = [Agent(system_prompt=prompt_a, tools=tools)(q) for q in test_cases]
results_b = [Agent(system_prompt=prompt_b, tools=tools)(q) for q in test_cases]

# LLM Judge로 비교 평가
```

---

## 참고 문서

- Strands 평가 프레임워크: https://strandsagents.com/latest/documentation/docs/user-guide/observability-evaluation/evaluation/
- Anthropic "Building Effective Agents" (평가 섹션): https://www.anthropic.com/engineering/building-effective-agents
