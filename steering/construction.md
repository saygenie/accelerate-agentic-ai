# AI-DLC Construction 단계

어떻게(HOW) 만드는지 실행하는 단계입니다.
Inception에서 설계한 내용을 바탕으로 코드를 구현하고, 테스트하고, 품질을 검증합니다.

---

## Construction 단계 흐름

```
1. 프로젝트 셋업 → 2. 에이전트 구현 → 3. 도구 구현 → 4. 통합 및 테스트 → 5. 품질 검증
```

각 단계 완료 시 **승인 게이트**를 통해 SA와 고객이 함께 리뷰합니다.

---

## 1. 프로젝트 셋업

### 1.1 개발 환경 준비

```bash
# 프로젝트 디렉토리 생성
mkdir my-agent-project && cd my-agent-project

# Python 가상환경 생성 (uv 권장)
uv venv --python 3.12 .venv
source .venv/bin/activate  # macOS/Linux

# 핵심 패키지 설치
uv pip install strands-agents strands-agents-tools
uv pip install "bedrock-agentcore-starter-toolkit>=0.1.21"

# Kiro에서 프로젝트 열기
```

### 1.2 표준 프로젝트 구조

```
my-agent-project/
├── patterns/{agent-name}/        # 에이전트 백엔드
│   ├── main.py                   # BedrockAgentCoreApp 엔트리포인트
│   ├── agents/
│   │   ├── __init__.py
│   │   ├── orchestrator.py       # 메인 오케스트레이터
│   │   └── {role}_agent.py       # 각 서브에이전트
│   ├── tools/
│   │   ├── __init__.py
│   │   └── {domain}_tools.py     # 커스텀 @tool 함수
│   ├── prompts/
│   │   └── {role}_prompt.py      # 시스템 프롬프트 상수
│   ├── db/                       # 데이터 레이어
│   │   ├── connection.py
│   │   ├── models.py
│   │   └── queries.py
│   ├── config.py
│   └── requirements.txt
├── frontend/                     # UI (필요 시)
│   ├── src/
│   ├── package.json
│   └── vite.config.ts
├── data/                         # 시드 데이터
│   └── seed.py
├── tests/
│   ├── test_agent.py
│   └── test_tools.py
├── .python-version               # 3.12
├── .bedrock_agentcore.yaml       # agentcore configure로 생성
└── README.md
```

### 1.3 AgentCore 프로젝트 초기화

```bash
# AgentCore CLI로 설정 생성 (수동 작성 금지)
agentcore configure \
  --entrypoint patterns/{agent-name}/main.py \
  --name MyAgent \
  --non-interactive \
  --region us-east-1 \
  --runtime PYTHON_3_12

# 또는 대화형으로
agentcore configure
```

**절대 `.bedrock_agentcore.yaml`을 수동으로 작성하지 않는다.** 항상 `agentcore configure` CLI를 사용한다.

---

## 2. 에이전트 구현

### 2.1 엔트리포인트 패턴 (SSE 스트리밍)

모든 에이전트는 BedrockAgentCoreApp 엔트리포인트 패턴을 따릅니다:

```python
import asyncio
import os
import sys

# 모듈 경로 설정 (필수)
sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))

from bedrock_agentcore import BedrockAgentCoreApp
from agents.orchestrator import create_orchestrator

app = BedrockAgentCoreApp()

class StreamingCallbackHandler:
    """SSE 스트리밍을 위한 콜백 핸들러"""
    def __init__(self, queue: asyncio.Queue):
        self.queue = queue

    def on_text(self, text: str):
        self.queue.put_nowait({"type": "text", "content": text})

    def on_tool_use(self, tool_name: str, tool_input: dict):
        self.queue.put_nowait({"type": "tool", "name": tool_name})

@app.handler
async def invoke(payload: dict):
    prompt = payload.get("prompt", "")
    queue = asyncio.Queue()
    handler = StreamingCallbackHandler(queue)

    orchestrator = create_orchestrator(callback_handler=handler)

    # 비동기 실행
    task = asyncio.create_task(
        asyncio.to_thread(orchestrator, prompt)
    )

    # SSE 스트리밍
    while not task.done() or not queue.empty():
        try:
            event = await asyncio.wait_for(queue.get(), timeout=0.1)
            yield event
        except asyncio.TimeoutError:
            continue

    yield {"type": "done"}
```

### 2.2 기본 에이전트 구조

```python
from strands import Agent
from strands.models.bedrock import BedrockModel

# 모델 설정
MODEL_ID = "us.anthropic.claude-sonnet-4-20250514-v1:0"

model = BedrockModel(
    model_id=MODEL_ID,
    max_tokens=4096,
    temperature=0.1  # 일관된 응답을 위해 낮은 temperature
)

# System Prompt — 별도 파일로 관리
SYSTEM_PROMPT = """
당신은 [역할]을 수행하는 AI 에이전트입니다.

## 핵심 역할
- [역할 1]
- [역할 2]

## 행동 규칙
- [규칙 1]
- [규칙 2]

## 사용 가능한 도구
- [도구 1]: [용도]
- [도구 2]: [용도]

## 제약 사항
- [제약 1]
- [제약 2]
"""

agent = Agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=[],  # 도구는 3단계에서 추가
)
```

### 2.3 멀티에이전트 구현 (Agents-as-Tools 예시)

```python
from strands import Agent
from strands.types.tools import tool

@tool
def risk_scorer(query: str) -> str:
    """고객 이탈 위험도를 스코어링합니다."""
    agent = Agent(
        model=model,
        system_prompt=RISK_SCORER_PROMPT,
        tools=[score_customer_risk, get_high_risk_customers]
    )
    return str(agent(query))

@tool
def diagnosis_agent(query: str) -> str:
    """이탈 원인을 진단합니다."""
    agent = Agent(
        model=model,
        system_prompt=DIAGNOSIS_PROMPT,
        tools=[diagnose_churn_causes, analyze_ticket_sentiment]
    )
    return str(agent(query))

# 오케스트레이터
orchestrator = Agent(
    model=model,
    system_prompt=ORCHESTRATOR_PROMPT,
    tools=[risk_scorer, diagnosis_agent],
)
```

**패턴 선택 이유를 반드시 문서화한다.** 리서치 문서에 다른 패턴이 명시되어 있는데 변경하는 경우, orchestrator.py 또는 README에 이유를 주석으로 남긴다.

---

## 3. 도구 구현

### 3.1 Python 도구 작성 규칙

```python
from strands.types.tools import tool

@tool
def get_employee_leave_balance(employee_id: str) -> dict:
    """직원의 남은 휴가 일수를 조회합니다.

    Args:
        employee_id: 직원 ID (예: "EMP001")

    Returns:
        {"annual_leave": 15, "sick_leave": 5}

    왜 이렇게?: Mock 데이터를 반환하여 API 계약만 정의합니다.
    실제 HR 시스템 연동은 백엔드 팀이 구현합니다.
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
- [ ] "왜 이렇게?" 주석으로 설계 의도 설명
- [ ] 입력 검증 (빈 값, 잘못된 형식)
- [ ] try/except로 에러 처리
- [ ] 로깅

### 3.2 데이터 레이어

도구에서 하드코딩 데이터를 사용하지 않고, db/ 레이어를 통해 데이터를 조회합니다:

```python
# db/connection.py
import sqlite3
import os

DB_PATH = os.path.join(os.path.dirname(__file__), "..", "..", "data", "agent.db")

def get_connection():
    return sqlite3.connect(DB_PATH)

# db/queries.py
from .connection import get_connection

def get_customer_by_id(customer_id: str) -> dict | None:
    conn = get_connection()
    cursor = conn.execute(
        "SELECT * FROM customers WHERE id = ?", (customer_id,)
    )
    row = cursor.fetchone()
    conn.close()
    if not row:
        return None
    return dict(zip([col[0] for col in cursor.description], row))
```

### 3.3 시드 데이터

```python
# data/seed.py
"""멱등성 보장 시드 스크립트. 여러 번 실행해도 안전."""
import argparse
import sqlite3

def seed():
    conn = sqlite3.connect("data/agent.db")
    conn.execute("DROP TABLE IF EXISTS customers")
    conn.execute("""
        CREATE TABLE customers (
            id TEXT PRIMARY KEY,
            name TEXT NOT NULL,
            ...
        )
    """)
    # 현실적이고 다양한 데이터 (최소 10건)
    conn.executemany("INSERT INTO customers VALUES (?, ?, ...)", data)
    conn.commit()
    conn.close()

def cleanup():
    os.remove("data/agent.db")

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--action", choices=["seed", "cleanup"], default="seed")
    args = parser.parse_args()
    {"seed": seed, "cleanup": cleanup}[args.action]()
```

---

## 4. 통합 및 테스트

### 4.1 테스트 전략

| 테스트 유형 | 대상 | 방법 |
|------------|------|------|
| Manual Evaluation | 에이전트 응답 품질 | 직접 agent() 호출하여 확인 |
| Structured Testing | 도구 함수 | pytest + JSON 테스트 케이스 |
| LLM Judge | 응답 품질 평가 | 다른 LLM으로 응답 평가 |
| Tool-Specific | 도구 선택 정확도 | `record_direct_tool_call=True` |

Strands Evaluation 공식 문서: https://strandsagents.com/latest/documentation/docs/user-guide/observability-evaluation/evaluation/

### 4.2 로컬 테스트 (AgentCore Dev Server)

```bash
# 시드 데이터 생성
python data/seed.py --action seed

# AgentCore 로컬 개발 서버 시작 (hot reload 지원)
agentcore dev

# 다른 터미널에서 테스트 요청
agentcore invoke --dev '{"prompt": "테스트 질문"}'
```

### 4.3 프론트엔드 통합 테스트 (필요 시)

```bash
# 백엔드 (터미널 1)
agentcore dev

# 프론트엔드 (터미널 2)
cd frontend && npm install && npm run dev
```

**Vite 프록시 설정**: `.env`의 `VITE_AGENT_API_URL`을 비워두면 프록시를 타고, 절대 URL을 넣으면 프록시를 우회하므로 주의.

### ⛔ 승인 게이트: 코드 리뷰
구현된 코드를 SA와 고객이 함께 리뷰합니다. 승인 후 Operations 단계로 진행합니다.

---

## 5. 품질 검증

### 5.1 코드 품질 체크리스트

- [ ] 모든 도구에 명확한 docstring + "왜 이렇게?" 주석
- [ ] 에러 처리가 적절한가? (try/except, 폴백 로직)
- [ ] 환경 변수로 설정 관리 (하드코딩 없음)
- [ ] 로깅이 적절히 구현되었는가?
- [ ] 민감 정보가 코드에 노출되지 않는가?
- [ ] 타입 힌트가 적절히 사용되었는가?
- [ ] `data-testid` 속성이 UI 요소에 추가되었는가? (프론트엔드)

### 5.2 에이전트 품질 체크리스트

- [ ] System Prompt가 명확하고 구체적인가?
- [ ] 에이전트가 범위 밖 요청을 적절히 거부하는가?
- [ ] Hallucination 방지 전략이 적용되었는가?
- [ ] 도구 호출이 정확한가? (잘못된 도구 선택 없음)
- [ ] 응답 시간이 요구사항 내인가?
- [ ] 멀티턴 대화에서 컨텍스트를 유지하는가?
- [ ] 멀티에이전트 패턴 선택 이유가 문서화되었는가?

### 5.3 Critical Rules (절대 하지 말아야 할 것)

과거 실수에서 배운 교훈들:

| 규칙 | 잘못된 예 | 올바른 예 |
|------|----------|----------|
| 모델 ID에 `bedrock/` 접두사 금지 | `bedrock/anthropic.claude...` | `us.anthropic.claude-sonnet-4-20250514-v1:0` |
| `.bedrock_agentcore.yaml` 수동 작성 금지 | 직접 YAML 편집 | `agentcore configure` CLI 사용 |
| 사용하지 않는 `import boto3` 금지 | `import boto3` (미사용) | 필요할 때만 import |
| Python 패키지 디렉토리에 하이픈 금지 | `my-agent/` | `my_agent/` |
| 프론트엔드 `.env`에 절대 URL 금지 | `VITE_AGENT_API_URL=http://...` | 비워두기 (프록시 사용) |
| 시뮬레이션 데이터 하드코딩 금지 | `total = 12` | `total = len(data)` (동적 계산) |
| API 키 하드코딩 금지 | `API_KEY = "sk-abc..."` | `os.environ["API_KEY"]` |

---

## Construction 완료 체크리스트

- [ ] 에이전트 코드 구현 완료
- [ ] 모든 도구 구현 및 테스트 완료
- [ ] 로컬 E2E 테스트 통과 (`agentcore dev` + `agentcore invoke`)
- [ ] 코드 품질 검증 완료
- [ ] Critical Rules 위반 없음
- [ ] 코드 리뷰 완료 및 승인

**다음 단계:** `readSteering("accelerate-agentic-ai", "operations.md")`
