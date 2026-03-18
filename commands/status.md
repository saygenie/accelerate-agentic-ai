---
name: status
description: 현재 AI-DLC 진행 상태를 확인하고 다음 단계를 안내합니다.
---

# AI-DLC 상태 확인

현재 프로젝트의 AI-DLC 진행 상태를 확인하고 다음 단계를 안내합니다.

## 수행할 작업

1. `aidlc-docs/aidlc-state.md` 파일을 읽어 현재 진행 상태를 확인합니다.
2. 완료된 단계와 진행 중인 단계를 요약합니다.
3. 다음으로 수행해야 할 작업을 안내합니다.
4. 관련 스킬이나 에이전트를 추천합니다.

## 상태 파일이 없는 경우

`aidlc-docs/aidlc-state.md`가 없으면 프로젝트가 아직 초기화되지 않은 것입니다.
`/accelerate-agentic-ai:setup` 명령어로 초기화를 안내합니다.

## 표시 형식

```
=== AI-DLC 진행 상태 ===

현재 단계: [Inception / Construction / Operations]
진행률: [완료 항목 / 전체 항목]

--- Inception ---
[x] 요구사항 분석
[x] 멀티에이전트 패턴 선택
[ ] 아키텍처 설계          ← 현재 진행 중

--- Construction ---
[ ] 프로젝트 셋업
...

--- 다음 단계 ---
아키텍처 설계를 진행합니다.
planner 에이전트를 호출하거나 inception 스킬을 참고하세요.
```
