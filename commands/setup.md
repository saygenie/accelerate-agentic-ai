---
name: setup
description: 새 프로젝트에서 AI-DLC를 시작하기 위한 초기화. 상태 추적 파일과 기본 디렉토리 구조를 생성합니다.
---

# 프로젝트 초기화

현재 프로젝트에 AI-DLC 개발 환경을 초기화합니다.

## 수행할 작업

1. **상태 추적 파일 생성**: `aidlc-docs/aidlc-state.md`
   - AI-DLC 3단계(Inception → Construction → Operations)의 진행 상황을 추적
   - 세션이 끊겨도 이어서 작업할 수 있도록 상태 유지

2. **산출물 디렉토리 생성**:
   - `aidlc-docs/inception/requirements/` - 요구사항 문서
   - `aidlc-docs/inception/architecture/` - 아키텍처 설계 문서
   - `aidlc-docs/construction/` - 구현 관련 문서
   - `aidlc-docs/operations/` - 운영 관련 문서

3. **현재 상태 확인**: 이미 초기화된 프로젝트인지 확인

## 초기화 후 다음 단계

- **Inception 시작**: planner 에이전트를 호출하여 요구사항 정리
- **가이드 참고**: inception 스킬로 상세 가이드 확인
- **상태 업데이트**: 각 단계 완료 시 `aidlc-docs/aidlc-state.md` 업데이트

## 상태 추적 파일 형식

```markdown
# Project State

**Project**: [프로젝트명]
**Created**: [날짜]
**Program Phase**: Co-Build

## Stage Progress

### INCEPTION PHASE
- [ ] 요구사항 분석
- [ ] 멀티에이전트 패턴 선택
- [ ] 아키텍처 설계

### CONSTRUCTION PHASE
- [ ] 프로젝트 셋업
- [ ] 에이전트 구현
- [ ] 도구 구현
- [ ] 통합 테스트

### OPERATIONS PHASE
- [ ] AgentCore 배포
- [ ] 인증 설정
- [ ] 모니터링 설정

## Current Status
현재 단계: Inception - 요구사항 분석
```

위 형식으로 `aidlc-docs/aidlc-state.md`를 생성하고, 필요한 디렉토리를 만들어주세요.
프로젝트명은 현재 디렉토리명을 기반으로 하되, 사용자에게 확인합니다.
