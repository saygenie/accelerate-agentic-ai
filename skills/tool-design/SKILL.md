---
name: tool-design
description: 좋은 도구(tool)를 설계하는 방법 - Anthropic ACI 원칙 기반. "도구 설계", "tool design", "@tool", "docstring", "도구 작성"을 언급할 때 사용합니다.
---

# 도구 설계 Best Practices

에이전트가 올바르게 도구를 선택하고 사용할 수 있도록 설계하는 방법입니다.
Anthropic의 ACI(Agent-Computer Interface) 원칙과 Strands SDK 도구 작성 규칙을 통합합니다.

---

## 핵심 원칙

### 1. 단일 책임 (Single Responsibility)
각 도구는 하나의 명확한 작업만 수행한다.

```python
# 나쁜 예: 하나의 도구가 너무 많은 일을 함
@tool
def manage_customer(action: str, customer_id: str, data: dict) -> str:
    """고객을 관리합니다."""  # 조회? 수정? 삭제?

# 좋은 예: 역할별 분리
@tool
def get_customer(customer_id: str) -> dict:
    """고객 정보를 조회합니다."""

@tool
def update_customer(customer_id: str, field: str, value: str) -> dict:
    """고객 정보를 수정합니다."""
```

### 2. 명확한 문서화 (Clear Documentation)

모델은 docstring을 읽고 도구 사용 여부를 결정한다.
**docstring은 도구의 UI다.**

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
        query: 검색 키워드 또는 상품명 (예: "무선 이어폰")
        category: 상품 카테고리 (all, electronics, clothing, food)
        max_results: 반환할 최대 결과 수 (기본값: 5, 최대: 20)
    """
```

**docstring 체크리스트:**
- [ ] 도구가 하는 일을 첫 줄에 명확히 설명
- [ ] 언제 사용하는지 설명
- [ ] 언제 사용하지 않는지 명시 (다른 도구와의 경계)
- [ ] Args에 파라미터별 설명 + 예시 + 제약조건
- [ ] 엣지 케이스 언급

### 3. Poka-yoke 원칙 (실수 방지)

매개변수 구조를 변경하여 실수 가능성을 줄인다.

```python
# 나쁜 예: 상대 경로 → 실수 가능
@tool
def read_file(path: str) -> str:
    """파일을 읽습니다. Args: path: 파일 경로"""

# 좋은 예: 절대 경로 필수 → 실수 불가능
@tool
def read_file(absolute_path: str) -> str:
    """파일을 읽습니다. Args: absolute_path: 파일의 절대 경로 (예: /home/user/data.csv)"""
```

### 4. 자연스러운 포맷

모델이 인터넷에서 자연스럽게 접한 텍스트 포맷에 가깝게 유지한다.
- Diff 작성의 라인 수 계산 복잡성 제거
- JSON 내 코드의 이스케이핑 부담 경감
- 충분한 "생각 공간" 제공

### 5. 입력 검증과 에러 처리

```python
@tool
def get_order_status(order_id: str) -> dict:
    """주문 상태를 조회합니다.

    Args:
        order_id: 주문 ID (예: "ORD-2024-001")
    """
    if not order_id or not order_id.strip():
        return {"error": "주문 ID를 입력해주세요."}

    try:
        result = db.get_order(order_id)
        if not result:
            return {"error": f"주문 '{order_id}'을 찾을 수 없습니다."}
        return result
    except Exception as e:
        logger.error(f"주문 조회 실패: {e}")
        return {"error": "주문 조회 중 오류가 발생했습니다. 잠시 후 다시 시도해주세요."}
```

### 6. 멱등성 (Idempotency)

가능하면 같은 입력에 같은 결과를 반환한다.

---

## 도구 수량 가이드

```python
# 나쁜 예: 너무 많은 도구 → 모델이 혼란
agent = Agent(tools=[tool1, tool2, ..., tool20])

# 좋은 예: 필요한 도구만 등록
agent = Agent(tools=[search_tool, action_tool, escalate_tool])
```

**권장:** 에이전트당 도구 5-10개 이하. 도구가 많아지면 멀티에이전트 패턴 고려.

---

## 데이터 레이어 분리

도구에서 데이터를 하드코딩하지 않고, db/ 레이어를 통해 조회한다.

```python
# 나쁜 예: 도구에 데이터 하드코딩
@tool
def get_menu(category: str) -> str:
    return "아메리카노: 4500원, 라떼: 5000원"

# 좋은 예: DB에서 조회
@tool
def get_menu(category: str) -> str:
    """메뉴를 조회합니다."""
    return db.get_menu_by_category(category)
```

---

## 참고 문서

- Anthropic Tool Use: https://docs.anthropic.com/en/docs/build-with-claude/tool-use/overview
- Anthropic "Building Effective Agents" (ACI 섹션): https://www.anthropic.com/engineering/building-effective-agents
- Strands Agents Tools: https://strandsagents.com/latest/documentation/docs/user-guide/concepts/tools/
