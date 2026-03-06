# Agentic Coding 모범 사례

Kiro와 함께 에이전트를 개발할 때의 모범 사례, 패턴, 안티패턴, 그리고 과거 실수에서 배운 교훈을 정리합니다.

---

## Kiro를 활용한 Agentic Coding

### AI-DLC 워크플로우 (Spec 대신)

이 Power는 AI-DLC 워크플로우를 따르므로 Kiro Spec 기능은 사용하지 않습니다.
AI-DLC의 Inception 단계가 Spec의 역할(요구사항 → 설계 → 태스크 분할)을 대체합니다.
Kiro는 **Vibe 모드**로 사용하며, Spec 모드 전환 안내가 나오면 No를 선택합니다.

### Kiro Steering 활용

프로젝트 전반에 적용할 규칙은 Steering 파일로 관리합니다:

```markdown
# .kiro/steering/coding-standards.md
---
inclusion: always
---

## 코딩 표준
- Python 3.12+ 사용
- Type hints 필수
- Docstring은 Google 스타일
- 환경 변수로 설정 관리
```

### Kiro Hook 활용

자동화할 작업은 Hook으로 설정합니다:

```json
{
  "name": "Lint on Save",
  "version": "1.0.0",
  "when": {
    "type": "fileEdited",
    "patterns": ["*.py"]
  },
  "then": {
    "type": "runCommand",
    "command": "ruff check --fix"
  }
}
```

### MCP 서버 우선 활용 원칙

정적 문서보다 **MCP 서버를 우선 사용**합니다:

| 주제 | MCP 도구 | 용도 |
|------|---------|------|
| Strands SDK | `search_docs`, `fetch_doc` | 에이전트 구현, 도구 정의, 멀티에이전트 패턴 |
| AgentCore | `search_agentcore_docs`, `fetch_agentcore_doc` | 배포, Memory, Gateway |
| AgentCore Runtime | `manage_agentcore_runtime` | 배포 요구사항, CLI 워크플로우 |

MCP 도구로 접근 불가한 경우에만 웹 검색이나 아래 참조 링크를 사용합니다.

---

## 멀티에이전트 패턴 의사결정 트리

### 핵심 질문: 실행 경로가 어떻게 결정되는가?

**단일 에이전트 우선**: 대부분의 경우 단일 에이전트 + 여러 도구로 충분합니다.

```
단일 도구로 해결 가능? → 단일 에이전트
↓
조건부 분기 + 에러 제어 필요? → Graph
↓
전문가 협업 + 동적 경로? → Swarm
↓
고정 파이프라인 + 병렬 실행? → Workflow
↓
독립적 하위 작업 분해? → Agents-as-Tools
```

### 패턴별 상세

| 패턴 | 실행 흐름 | 사이클 | 적합한 경우 | 선택 기준 |
|------|----------|--------|------------|----------|
| **Agents-as-Tools** | 오케스트레이터가 전문가를 도구로 호출 | 불가 | 독립적 하위 작업 분해 | "각 작업이 다른 전문성을 요구하는가?" |
| **Graph** | 개발자가 노드와 엣지를 정의, LLM이 경로 선택 | 가능 | 조건부 분기, 에러 제어 | "IF-THEN 로직이 명확한가?" |
| **Swarm** | 에이전트가 자율적으로 핸드오프 | 가능 | 전문가 협업, 동적 경로 | "각 단계마다 다른 전문 지식이 필요한가?" |
| **Workflow** | 고정된 DAG, 독립 태스크 병렬 실행 | 불가 | 반복 가능한 파이프라인 | "프로세스가 고정되어 있는가?" |

**중요: 모든 아이디어를 Agents-as-Tools로 수렴하지 말 것.** 각 문제의 구조를 분석하고 위 가이드에 따라 가장 적합한 패턴을 선택하라.

---

## System Prompt 작성 가이드

### 좋은 System Prompt 구조

```
1. 역할 정의 — 에이전트의 정체성과 전문 분야
2. 행동 규칙 — 구체적인 행동 지침
3. 도구 사용 가이드 — 언제 어떤 도구를 사용할지
4. 출력 형식 — 응답 형식 지정
5. 제약 사항 — 하지 말아야 할 것들
6. 예시 — Few-shot 예시 (선택)
```

### 좋은 예시

```
당신은 고객 서비스 에이전트입니다.

## 역할
- 고객 문의를 분류하고 적절한 답변을 제공합니다.
- 복잡한 문의는 담당자에게 에스컬레이션합니다.

## 행동 규칙
- 항상 정중하고 전문적인 톤을 유지합니다.
- 확실하지 않은 정보는 추측하지 않고, 확인 후 답변합니다.

## 사용 가능한 도구
- search_faq: FAQ 검색 — 고객 질문에 대한 답변을 찾을 때
- get_order_status: 주문 상태 조회 — 주문번호로 배송 상태 확인
- escalate_to_human: 담당자 에스컬레이션 — 복잡한 문의 시

## 제약 사항
- 환불/취소는 직접 처리하지 않고 담당자에게 전달합니다.
- 경쟁사 제품에 대한 비교/평가를 하지 않습니다.
```

### 나쁜 예시

```
고객 서비스를 도와주세요.
```

---

## 도구 설계 원칙

1. **단일 책임**: 각 도구는 하나의 명확한 작업만 수행
2. **명확한 Docstring**: 모델이 도구를 올바르게 선택할 수 있도록 상세한 설명
3. **"왜 이렇게?" 주석**: 설계 의도를 코드에 남김
4. **입력 검증**: 잘못된 입력에 대한 방어 코드
5. **에러 처리**: 실패 시 명확한 에러 메시지 반환
6. **멱등성**: 가능하면 같은 입력에 같은 결과

### 도구 Docstring 최적화

모델은 도구의 docstring을 읽고 사용 여부를 결정합니다:

```python
@tool
def search_products(
    query: str,
    category: str = "all",
    max_results: int = 5
) -> str:
    """상품 카탈로그에서 상품을 검색합니다.

    고객이 특정 상품을 찾거나, 카테고리별 상품을 조회할 때 사용합니다.

    이 도구를 사용하지 않아야 하는 경우:
    - 주문 상태 조회 (get_order_status 사용)
    - 고객 정보 조회 (get_customer_info 사용)

    Args:
        query: 검색 키워드 또는 상품명
        category: 상품 카테고리 (all, electronics, clothing, food)
        max_results: 반환할 최대 결과 수 (기본값: 5)
    """
```

---

## 코드 스타일

### Documentation-First 원칙

개발자가 Strands Agents를 처음 접한다고 가정합니다. 기술 용어는 예시와 함께 설명합니다.

**체크리스트:**
- [ ] 용어 첫 등장 시 간단한 정의 포함
- [ ] 코드 예시에 주석으로 "왜" 설명
- [ ] 실제 사용 시나리오 제시
- [ ] Mock vs 실제 구현 구분 명시

### 설정 관리

```python
# config.py — 환경 변수로 중앙 관리
import os

class Config:
    MODEL_ID = os.environ.get(
        "MODEL_ID",
        "us.anthropic.claude-sonnet-4-20250514-v1:0"
    )
    MAX_TOKENS = int(os.environ.get("MAX_TOKENS", "4096"))
    TEMPERATURE = float(os.environ.get("TEMPERATURE", "0.1"))
    LOG_LEVEL = os.environ.get("LOG_LEVEL", "INFO")
```

### 로깅

```python
import logging
logger = logging.getLogger(__name__)

# 에이전트 호출 로깅
logger.info(f"Agent invoked with query: {query[:100]}...")
logger.info(f"Agent response generated in {elapsed:.2f}s")

# 도구 호출 로깅
logger.info(f"Tool '{tool_name}' called with args: {args}")
logger.error(f"Tool '{tool_name}' failed: {error}")
```

---

## 성능 최적화

### 모델 선택 최적화

| 작업 유형 | 권장 모델 | 이유 |
|----------|----------|------|
| 복잡한 추론 | Claude Opus | 최고 정확도 |
| 범용 작업 | Claude Sonnet 4 | 성능/비용 균형 |
| 분류/라우팅 | Claude Haiku 또는 Nova Lite | 빠른 응답, 저비용 |

### 토큰 최적화

- System Prompt를 간결하게 유지 (불필요한 반복 제거)
- 도구 Docstring을 명확하되 간결하게 작성
- 긴 컨텍스트는 요약 후 전달
- `cachePoint`를 활용하여 반복 프롬프트 캐싱 (1024+ 토큰)

```python
from strands.types.content import SystemContentBlock

system_prompt_blocks = [
    SystemContentBlock(text=long_system_prompt),
    SystemContentBlock(cachePoint={"type": "ephemeral"}),
]
```

### 응답 시간 최적화

- SSE 스트리밍 응답 활용으로 체감 응답 시간 단축
- 도구 호출 최소화 (필요한 정보를 한 번에 가져오기)
- 병렬 도구 호출 활용 (Strands SDK 지원)

---

## Critical Rules (절대 하지 말아야 할 것)

과거 프로젝트에서 반복된 실수들. 이 규칙들을 반드시 지킵니다:

### 모델 및 SDK

| # | 규칙 | 잘못된 예 | 올바른 예 |
|---|------|----------|----------|
| 1 | 모델 ID에 `bedrock/` 접두사 금지 | `bedrock/anthropic.claude...` | `us.anthropic.claude-sonnet-4-20250514-v1:0` |
| 2 | cross-region inference profile 사용 | `anthropic.claude-sonnet-4-20250514-v1:0` | `us.anthropic.claude-sonnet-4-20250514-v1:0` |
| 3 | 사용하지 않는 `import boto3` 금지 | `import boto3` (미사용) | 필요할 때만 import (credential 로딩 발생) |

### AgentCore

| # | 규칙 | 잘못된 예 | 올바른 예 |
|---|------|----------|----------|
| 4 | `.bedrock_agentcore.yaml` 수동 작성 금지 | 직접 YAML 편집 | `agentcore configure` CLI 사용 |
| 5 | `main.py` 상단에 sys.path 설정 필수 | path 없이 import | `sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))` |

### Python

| # | 규칙 | 잘못된 예 | 올바른 예 |
|---|------|----------|----------|
| 6 | 패키지 디렉토리에 하이픈 금지 | `my-agent/` | `my_agent/` |
| 7 | API 키 하드코딩 금지 | `API_KEY = "sk-abc..."` | `os.environ["API_KEY"]` |
| 8 | 데이터 하드코딩 금지 | `total = 12` | `total = len(data)` (동적 계산) |

### 프론트엔드

| # | 규칙 | 잘못된 예 | 올바른 예 |
|---|------|----------|----------|
| 9 | `.env`에 절대 URL 금지 | `VITE_AGENT_API_URL=http://...` | 비워두기 (Vite 프록시 사용) |
| 10 | 한국어 IME 처리 필수 | `onChange` 직접 처리 | `onCompositionStart/End` 사용 |
| 11 | 마크다운 테이블 렌더링 | `react-markdown`만 사용 | `react-markdown` + `remark-gfm` |

---

## 한국어 문서 작성 표준

### 기본 원칙
- 모든 문서는 한국어로 작성
- 기술 용어는 "한글(영문)" 형식 (예: 도구(tool), 에이전트(agent))
- 문장은 평서체("~한다", "~해야 한다")로 간결하게
- 코드 주석과 docstring도 한국어로 작성

### 요구사항 작성 스타일

**나쁜 예시** (영어 키워드 혼용):
```
WHEN Manager가 휴가 반려를 요청하면, THE Agent SHALL 신청을 검색한다.
```

**좋은 예시** (자연스러운 한국어):
```
1. 팀장이 특정 직원의 휴가 반려를 요청하면, 에이전트는 해당 직원의 대기 중인 휴가 신청을 검색한다.
2. 대기 중인 신청이 없으면, 에이전트는 "대기 중인 신청을 찾을 수 없습니다" 메시지를 반환한다.
```

---

## 안티패턴 (피해야 할 것들)

### 1. 과도한 도구 등록
```python
# ❌ 나쁜 예: 너무 많은 도구 (모델이 혼란)
agent = Agent(tools=[tool1, tool2, ..., tool20])

# ✅ 좋은 예: 필요한 도구만
agent = Agent(tools=[search_tool, action_tool, escalate_tool])
```

### 2. 에러 처리 누락
```python
# ❌ 나쁜 예
@tool
def get_data(id: str) -> str:
    return db.query(id).to_json()

# ✅ 좋은 예
@tool
def get_data(id: str) -> str:
    """데이터를 조회합니다. Args: id: 데이터 ID"""
    if not id:
        return "ID를 입력해주세요."
    try:
        result = db.query(id)
        if not result:
            return f"ID '{id}'에 해당하는 데이터를 찾지 못했습니다."
        return result.to_json()
    except Exception as e:
        logger.error(f"데이터 조회 실패: {e}")
        return "데이터 조회 중 오류가 발생했습니다."
```

### 3. 테스트 없는 배포
```bash
# ❌ 나쁜 예: 바로 배포
agentcore launch

# ✅ 좋은 예: 로컬 테스트 → 검증 → 배포
agentcore dev              # 1. 로컬 테스트
agentcore invoke --dev ... # 2. 동작 검증
pytest tests/              # 3. 자동화 테스트
agentcore launch           # 4. 배포
```

---

## AWS 참고 자료

| 자료 | 링크 | 용도 |
|------|------|------|
| Agentic AI Foundations | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-foundations/ | 에이전트 개념 기초 |
| Agentic AI Frameworks | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/ | 프레임워크 비교 |
| Operationalizing Agentic AI | https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-operationalizing-agentic-ai/ | 거버넌스, 배포 전략 |
| Serverless Architectures | https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-serverless/ | 서버리스 패턴 |
| FAST Template | https://github.com/awslabs/fullstack-solution-template-for-agentcore | 풀스택 레퍼런스 |
| AgentCore Samples | https://github.com/awslabs/amazon-bedrock-agentcore-samples | 예제 코드 |
| Strands SDK | https://strandsagents.com/latest/documentation/ | 공식 문서 |
| Strands Samples | https://github.com/strands-agents/samples | 샘플 프로젝트 |
| AI-DLC Workflows | https://github.com/awslabs/aidlc-workflows | AI-DLC 방법론 |
