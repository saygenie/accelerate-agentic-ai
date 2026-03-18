---
name: operations
description: AI-DLC Phase 3 실행 - AgentCore 배포, Cognito 인증, 모니터링, 프로덕션 검증. "배포", "운영", "operations", "모니터링", "AgentCore"를 언급할 때 사용합니다.
---

# AI-DLC Operations 단계

배포하고 운영하는 단계입니다.
구현된 에이전트를 AgentCore Runtime에 배포하고, 인증을 설정하고, 모니터링을 구성합니다.

---

## Operations 단계 흐름

```
1. 배포 준비 → 2. AgentCore 배포 → 3. 인증 설정 → 4. 배포 검증 → 5. 모니터링 설정 → 6. 프로덕션 검증
```

---

## 1. 배포 준비

| 항목 | 확인 |
|------|------|
| 모든 테스트 통과 | ☐ |
| 환경 변수 정리 | ☐ |
| 의존성 정리 (requirements.txt) | ☐ |
| AgentCore 설정 확인 | ☐ |
| AWS 인증 확인 | ☐ |
| Bedrock 모델 접근 권한 | ☐ |

---

## 2. AgentCore 배포

### CLI 워크플로우

```bash
aws sts get-caller-identity          # AWS 인증 확인
agentcore configure                   # 설정 (완료 시 스킵)
agentcore launch                      # 배포 (CodeBuild, Docker 불필요)
agentcore status                      # 상태 확인
agentcore invoke '{"prompt": "테스트"}' # 테스트 호출
```

### 로컬 vs 배포 차이

| 항목 | 로컬 (`agentcore dev`) | 배포 (`agentcore launch`) |
|------|----------------------|--------------------------|
| 엔드포인트 | localhost:8080 | AgentCore Runtime URL |
| 인증 | 없음 | Cognito JWT Bearer Token |
| 빌드 | 즉시 실행 | CodeBuild |
| Hot Reload | 지원 | 미지원 (재배포 필요) |

---

## 3. 인증 설정 (Cognito)

FAST 레퍼런스 아키텍처 기준 4곳에서 인증:
1. 프론트엔드 로그인 - 사용자 인증
2. 프론트엔드 → AgentCore Runtime - Bearer Token
3. 에이전트 → AgentCore Gateway - Bearer Token
4. 프론트엔드 → API Gateway - Bearer Token (REST API)

---

## 4. 배포 검증

```bash
agentcore invoke '{"prompt": "안녕하세요, 테스트입니다."}'
agentcore invoke '{"prompt": "고객 C001의 정보를 알려주세요."}'
```

| 검증 항목 | 확인 |
|----------|------|
| 에이전트 응답 정상 | ☐ |
| 도구 호출 정상 | ☐ |
| SSE 스트리밍 정상 | ☐ |
| 에러 처리 정상 | ☐ |
| 응답 시간 적정 | ☐ |

---

## 5. 모니터링 설정

### CloudWatch 메트릭

| 메트릭 | 알람 기준 |
|--------|---------|
| Duration | > 요구사항의 2배 |
| Errors | > 0 (5분 내) |
| Throttles | > 0 |
| ConcurrentExecutions | > 80% 한도 |

### 로그 모니터링

```bash
aws logs tail /aws/agentcore/my-agent --follow
aws logs filter-log-events \
  --log-group-name /aws/agentcore/my-agent \
  --filter-pattern "ERROR"
```

---

## 6. 프로덕션 검증

### 프로덕션 준비 체크리스트

**인프라:** 배포 성공, 오토스케일링, 리전 설정
**모니터링:** 메트릭, 알람, 로그, 트레이싱
**보안:** IAM 최소 권한, Secrets Manager, Cognito, 암호화
**운영:** 롤백 절차, 장애 대응, 비용 모니터링

---

## Operations 완료 체크리스트

- [ ] AgentCore 배포 성공
- [ ] 인증 설정 완료 (Cognito)
- [ ] 배포 검증 완료
- [ ] 모니터링 설정 완료
- [ ] 프로덕션 준비 체크리스트 통과
- [ ] 비용 추정 완료
- [ ] 운영 문서 작성 완료
- [ ] 고객 최종 승인
