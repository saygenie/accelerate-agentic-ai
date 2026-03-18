---
name: construction
description: AI-DLC Phase 2 실행 - 환경 설정, 에이전트 구현, 도구 개발, 테스트. "구현", "개발", "코딩", "construction", "테스트"를 언급할 때 사용합니다.
---

# AI-DLC Construction 단계

어떻게(HOW) 만드는지 실행하는 단계입니다.
Inception에서 설계한 내용을 바탕으로 코드를 구현하고, 테스트하고, 품질을 검증합니다.

---

## Construction 단계 흐름

```
1. 프로젝트 셋업 → 2. 에이전트 구현 → 3. 도구 구현 → 4. 통합 및 테스트 → 5. 품질 검증
```

---

## 1. 프로젝트 셋업

### 개발 환경 준비

```bash
mkdir my-agent-project && cd my-agent-project
uv venv --python 3.12 .venv
source .venv/bin/activate
uv pip install strands-agents strands-agents-tools
uv pip install "bedrock-agentcore-starter-toolkit>=0.1.21"
```

### 표준 프로젝트 구조

```
patterns/{agent-name}/
├── main.py                   # BedrockAgentCoreApp 엔트리포인트
├── agents/
│   ├── orchestrator.py       # 메인 오케스트레이터
│   └── {role}_agent.py       # 각 서브에이전트
├── tools/
│   └── {domain}_tools.py     # 커스텀 @tool 함수
├── prompts/
│   └── {role}_prompt.py      # 시스템 프롬프트 상수
├── db/
│   ├── connection.py
│   └── queries.py
├── config.py
└── requirements.txt
```

### AgentCore 프로젝트 초기화

```bash
agentcore configure \
  --entrypoint patterns/{agent-name}/main.py \
  --name MyAgent \
  --non-interactive \
  --region us-east-1 \
  --runtime PYTHON_3_12
```

**절대 `.bedrock_agentcore.yaml`을 수동으로 작성하지 않는다.** 항상 `agentcore configure` CLI를 사용한다.

---

## 2. 에이전트 구현

### 엔트리포인트 패턴 (SSE 스트리밍)

```python
import asyncio, os, sys
sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))

from bedrock_agentcore import BedrockAgentCoreApp
app = BedrockAgentCoreApp()

@app.handler
async def invoke(payload: dict):
    prompt = payload.get("prompt", "")
    queue = asyncio.Queue()
    orchestrator = create_orchestrator(callback_handler=StreamingCallbackHandler(queue))
    task = asyncio.create_task(asyncio.to_thread(orchestrator, prompt))
    while not task.done() or not queue.empty():
        try:
            event = await asyncio.wait_for(queue.get(), timeout=0.1)
            yield event
        except asyncio.TimeoutError:
            continue
    yield {"type": "done"}
```

### 기본 에이전트 구조

```python
from strands import Agent
from strands.models.bedrock import BedrockModel

model = BedrockModel(
    model_id="us.anthropic.claude-sonnet-4-20250514-v1:0",
    max_tokens=4096,
    temperature=0.1
)

agent = Agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=[],
)
```

### 멀티에이전트 구현 (Agents-as-Tools 예시)

```python
from strands.types.tools import tool

@tool
def specialist_agent(query: str) -> str:
    """도메인 특화 쿼리를 처리합니다."""
    agent = Agent(system_prompt=SPECIALIST_PROMPT, tools=[domain_tool])
    return str(agent(query))

orchestrator = Agent(tools=[specialist_agent])
```

---

## 3. 도구 구현

### Python 도구 작성 규칙

```python
@tool
def get_employee_leave_balance(employee_id: str) -> dict:
    """직원의 남은 휴가 일수를 조회합니다.

    Args:
        employee_id: 직원 ID (예: "EMP001")
    """
    if not employee_id or not employee_id.strip():
        return {"error": "직원 ID를 입력해주세요."}
    try:
        result = db.get_leave_balance(employee_id)
        if not result:
            return {"error": f"직원 '{employee_id}'을 찾을 수 없습니다."}
        return result
    except Exception as e:
        logger.error(f"휴가 조회 실패: {e}")
        return {"error": "휴가 조회 중 오류가 발생했습니다."}
```

**도구 작성 체크리스트:**
- [ ] 명확한 docstring (모델이 읽고 도구 선택을 결정함)
- [ ] Args 섹션에 파라미터 설명 + 예시
- [ ] 입력 검증 + try/except 에러 처리
- [ ] 로깅

---

## 4. 통합 및 테스트

### 테스트 전략

| 테스트 유형 | 대상 | 방법 |
|------------|------|------|
| Manual Evaluation | 에이전트 응답 품질 | 직접 agent() 호출 |
| Structured Testing | 도구 함수 | pytest + JSON 테스트 케이스 |
| LLM Judge | 응답 품질 평가 | 다른 LLM으로 응답 평가 |

### 로컬 테스트

```bash
python data/seed.py --action seed
agentcore dev
agentcore invoke --dev '{"prompt": "테스트 질문"}'
```

---

## 5. 품질 검증

### Critical Rules (절대 하지 말아야 할 것)

| 규칙 | 잘못된 예 | 올바른 예 |
|------|----------|----------|
| 모델 ID에 `bedrock/` 접두사 금지 | `bedrock/anthropic.claude...` | `us.anthropic.claude-sonnet-4-20250514-v1:0` |
| `.bedrock_agentcore.yaml` 수동 작성 금지 | 직접 YAML 편집 | `agentcore configure` CLI 사용 |
| Python 패키지 디렉토리에 하이픈 금지 | `my-agent/` | `my_agent/` |
| API 키 하드코딩 금지 | `API_KEY = "sk-abc..."` | `os.environ["API_KEY"]` |
| 시뮬레이션 데이터 하드코딩 금지 | `total = 12` | `total = len(data)` |

---

## Construction 완료 체크리스트

- [ ] 에이전트 코드 구현 완료
- [ ] 모든 도구 구현 및 테스트 완료
- [ ] 로컬 E2E 테스트 통과
- [ ] Critical Rules 위반 없음
- [ ] 코드 리뷰 완료 및 승인

**다음 단계:** Operations phase로 진행
