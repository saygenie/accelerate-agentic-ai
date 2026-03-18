---
name: anthropic-references
description: Anthropic 공식 문서 가이드 - Building Effective Agents, Tool Use, Prompt Engineering. "Anthropic", "Claude 문서", "Building Effective Agents"를 언급할 때 사용합니다.
---

# Anthropic 공식 문서 가이드

Anthropic의 에이전트 개발 관련 핵심 공식 문서를 요약하고 참조 방법을 안내합니다.

---

## 1. Building Effective Agents

**URL:** https://www.anthropic.com/engineering/building-effective-agents

Anthropic 엔지니어링 팀이 공유한 에이전트 구축의 핵심 가이드.

### 핵심 요약

**3가지 설계 원칙:**
1. **단순성 우선**: 복잡한 프레임워크 대신 기본 API부터 시작. 필요할 때만 복잡성 추가
2. **투명성**: 에이전트의 계획과 추론 과정을 명시적으로 표시
3. **ACI 설계**: 도구 문서화에 UI 설계만큼 투자

**5가지 워크플로우 패턴:**
1. Prompt Chaining - 순차적 단계 분해
2. Routing - 입력 유형별 분기
3. Parallelization - 독립 작업 병렬/투표
4. Orchestrator-Workers - 중앙 분해 + 워커 위임
5. Evaluator-Optimizer - 생성 + 평가 반복

**에이전트 적용 성공 사례:**
- 고객 지원: 대화형 인터페이스 + 도구 통합, 성공 기준 명확
- 코딩 에이전트: 자동화된 테스트로 검증 가능, SWE-bench 성과

### 참조 방법
이 문서의 내용을 agent-design-principles, multi-agent-patterns, tool-design 스킬에서 실천적으로 다룹니다.

---

## 2. Tool Use Overview

**URL:** https://docs.anthropic.com/en/docs/build-with-claude/tool-use/overview

Claude의 도구 사용(function calling) 공식 문서.

### 핵심 요약

**기본 구조:**
- 도구 정의 (name, description, input_schema) → API 호출 시 전달
- Claude가 도구 선택 → tool_use 응답 → 개발자가 실행 → tool_result 반환

**Best Practices:**
- 도구 description을 상세하고 명확하게 작성
- 파라미터 description에 예시와 제약조건 포함
- 도구 간 경계를 명확히 정의
- `strict: true` 스키마 검증 활용

### 참조 방법
도구 설계 시 tool-design 스킬과 함께 이 문서를 참조합니다.

---

## 3. Prompt Engineering

**URL:** https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview

Claude 프롬프트 엔지니어링 공식 가이드.

### 핵심 요약

**효과적인 프롬프트 원칙:**
- 명확하고 구체적인 지시
- 구조화된 형식 (역할 → 규칙 → 예시 → 제약)
- Few-shot 예시 활용
- 출력 형식 명시

**System Prompt 작성:**
```
1. 역할 정의 - 에이전트의 정체성
2. 행동 규칙 - 구체적 행동 지침
3. 도구 사용 가이드 - 언제 어떤 도구를
4. 출력 형식 - 응답 형식 지정
5. 제약 사항 - 하지 말아야 할 것
6. 예시 - Few-shot (선택)
```

### 참조 방법
에이전트 System Prompt 작성 시 이 문서를 참조합니다.

---

## 활용 팁

1. **에이전트 설계 시**: Building Effective Agents의 3원칙 + 5패턴 참조
2. **도구 개발 시**: Tool Use Overview의 best practices 참조
3. **프롬프트 작성 시**: Prompt Engineering의 구조와 예시 참조
4. **평가 시**: Building Effective Agents의 "평가 없이 복잡성 추가 금지" 원칙 적용
