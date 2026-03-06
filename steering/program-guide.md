# Co-Building 진행 가이드

이 문서는 SA와 고객 개발자가 함께 에이전트를 개발하는 Co-Building 과정의 진행 흐름을 안내합니다.

---

## Overview

"Show Together, Don't Tell" — 강의가 아닌 co-building입니다.
SA와 고객 개발자가 Kiro를 활용해 실제 프로덕션 에이전트를 함께 만듭니다.

Kiro가 기본 개발 환경이며, AI-DLC 워크플로우를 따라 체계적으로 진행합니다.
Kiro는 Vibe 모드로 사용합니다. Spec 모드 전환 안내가 나오면 No를 선택합니다.

---

## 진행 방식

- 고객 AWS 계정에서 고객 데이터로 작업
- AI-DLC 3단계를 따라 진행:

```
Day 1-2: Inception (기획/설계)
  → readSteering("accelerate-agentic-ai", "inception.md")

Day 2-4: Construction (구현/테스트)
  → readSteering("accelerate-agentic-ai", "construction.md")

Day 4-5: Operations (배포/검증)
  → readSteering("accelerate-agentic-ai", "operations.md")
```

---

## 일일 루틴

1. **Morning Stand-up** (15분): 전일 진행 상황, 오늘 목표, 블로커 공유
2. **Co-Building Session** (오전): AI-DLC 단계에 따른 개발
3. **Co-Building Session** (오후): 개발 계속
4. **Daily Wrap-up** (15분): 진행 상황 정리, 내일 계획

---

## Co-Build 체크리스트

### Inception 완료 확인
- [ ] 요구사항 정의서 작성 및 승인
- [ ] 멀티에이전트 패턴 결정
- [ ] 아키텍처 설계서 작성 및 승인
- [ ] 작업 단위 분할 완료

### Construction 완료 확인
- [ ] 에이전트 코드 구현 완료
- [ ] 도구 구현 및 테스트 완료
- [ ] 로컬 E2E 테스트 통과
- [ ] 코드 리뷰 완료

### Operations 완료 확인
- [ ] AgentCore 배포 성공
- [ ] 배포 검증 완료
- [ ] 모니터링 설정 완료
- [ ] 운영 문서 작성 완료

---

## Wrap-up

Co-Building이 완료되면 산출물을 정리합니다.

### 산출물 체크리스트
- [ ] 프로덕션 수준 에이전트 코드베이스 (Git 저장소)
- [ ] 배포된 에이전트 (AgentCore Runtime)
- [ ] 아키텍처 문서
- [ ] 운영 가이드 (배포, 모니터링, 트러블슈팅)
- [ ] 비용 추정 문서
- [ ] 향후 확장 로드맵
