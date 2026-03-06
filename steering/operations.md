# AI-DLC Operations 단계

배포하고 운영하는 단계입니다.
구현된 에이전트를 AgentCore Runtime에 배포하고, 인증을 설정하고, 모니터링을 구성하며, 프로덕션 준비를 검증합니다.

---

## Operations 단계 흐름

```
1. 배포 준비 → 2. AgentCore 배포 → 3. 인증 설정 → 4. 배포 검증 → 5. 모니터링 설정 → 6. 프로덕션 검증
```

---

## 1. 배포 준비

### 1.1 배포 전 체크리스트

| 항목 | 확인 | 비고 |
|------|------|------|
| 모든 테스트 통과 | ☐ | Unit, Integration, E2E |
| 환경 변수 정리 | ☐ | 프로덕션 값 준비 |
| 의존성 정리 | ☐ | requirements.txt 최신화 |
| AgentCore 설정 확인 | ☐ | `agentcore configure`로 생성 |
| AWS 인증 확인 | ☐ | 배포 권한 확인 |
| Bedrock 모델 접근 권한 | ☐ | 프로덕션 리전에서 확인 |
| Critical Rules 위반 없음 | ☐ | construction.md 참조 |

### 1.2 의존성 정리

```bash
# requirements.txt 확인
cat requirements.txt

# 필요한 패키지만 명시 (freeze 대신 직접 관리 권장)
# requirements.txt 예시:
strands-agents>=0.1.0
strands-agents-tools>=0.1.0
boto3>=1.35.0
```

---

## 2. AgentCore 배포

### 2.1 AgentCore CLI 워크플로우

```bash
# 1. AWS 인증 확인
aws sts get-caller-identity

# 2. AgentCore 설정 (이미 configure 완료된 경우 스킵)
agentcore configure \
  --entrypoint patterns/{agent-name}/main.py \
  --name MyAgent \
  --non-interactive \
  --region us-east-1 \
  --runtime PYTHON_3_12

# 3. AWS에 배포 (CodeBuild 사용, Docker 불필요)
agentcore launch

# 4. 배포 상태 확인
agentcore status

# 5. 테스트 호출
agentcore invoke '{"prompt": "테스트 질문"}'
```

### 2.2 배포 옵션

```bash
# 특정 리전에 배포
agentcore launch --region ap-northeast-2

# 태그 추가
agentcore launch --tags project=acceleration,team=customer-name
```

### 2.3 로컬 vs 배포 차이

| 항목 | 로컬 (`agentcore dev`) | 배포 (`agentcore launch`) |
|------|----------------------|--------------------------|
| 엔드포인트 | `localhost:8080/invocations` | AgentCore Runtime URL |
| 인증 | 없음 (직접 호출) | Cognito JWT Bearer Token |
| 빌드 | 즉시 실행 | CodeBuild로 빌드 |
| Hot Reload | 지원 | 미지원 (재배포 필요) |

### 2.4 배포 문제 해결

**"Authentication failed" 에러:**
```bash
aws login
# 또는
aws configure
```

**"Model access denied" 에러:**
- Bedrock 콘솔 → Model access → 해당 모델 활성화
- 배포 리전에서 모델이 사용 가능한지 확인

**"Entrypoint not found" 에러:**
- `agentcore configure`로 설정 재생성
- `main.py` 상단에 `sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))` 확인

---

## 3. 인증 설정 (Cognito)

프로덕션 배포 시 Amazon Cognito로 인증을 설정합니다.

### 3.1 Cognito 인증 4곳

FAST 레퍼런스 아키텍처 기준:

```
1. 프론트엔드 로그인 — 사용자 인증
2. 프론트엔드 → AgentCore Runtime — Bearer Token
3. 에이전트 → AgentCore Gateway — Bearer Token
4. 프론트엔드 → API Gateway — Bearer Token (REST API 호출 시)
```

### 3.2 프론트엔드 인증 통합

```typescript
// 로컬 개발: 인증 없이 직접 호출
// 배포: Cognito JWT Bearer Token 필요
const apiUrl = import.meta.env.VITE_AGENT_API_URL || "/api";

const response = await fetch(`${apiUrl}/invocations`, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    // 배포 시에만 추가
    ...(token && { "Authorization": `Bearer ${token}` }),
  },
  body: JSON.stringify({ prompt: userMessage }),
});
```

---

## 4. 배포 검증

### 4.1 기본 동작 확인

```bash
# 배포된 에이전트에 테스트 요청
agentcore invoke '{"prompt": "안녕하세요, 테스트입니다."}'

# 도구 호출이 포함된 요청
agentcore invoke '{"prompt": "고객 C001의 정보를 알려주세요."}'
```

### 4.2 배포 검증 체크리스트

| 검증 항목 | 확인 | 비고 |
|----------|------|------|
| 에이전트 응답 정상 | ☐ | 기본 질문에 올바른 응답 |
| 도구 호출 정상 | ☐ | 각 도구가 정상 동작 |
| SSE 스트리밍 정상 | ☐ | 토큰 단위 실시간 표시 |
| 에러 처리 정상 | ☐ | 잘못된 입력에 적절한 응답 |
| 응답 시간 적정 | ☐ | 요구사항 내 응답 시간 |
| 메모리 사용 적정 | ☐ | OOM 없음 |
| 로그 출력 정상 | ☐ | CloudWatch에 로그 확인 |
| 인증 동작 정상 | ☐ | Cognito 토큰 검증 (배포 시) |

### ⛔ 승인 게이트: 배포 검증
배포된 에이전트를 SA와 고객이 함께 테스트합니다. 승인 후 모니터링 설정으로 진행합니다.

---

## 5. 모니터링 설정

### 5.1 AgentCore Observability

AgentCore의 내장 관측성 기능을 활용합니다:

```python
# 에이전트 코드에 트레이싱 추가
agent = Agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=tools,
    trace_enabled=True,  # 트레이싱 활성화
)
```

### 5.2 CloudWatch 메트릭

| 메트릭 | 설명 | 알람 기준 (권장) |
|--------|------|-----------------|
| Invocations | 호출 횟수 | - |
| Duration | 실행 시간 | > 요구사항의 2배 |
| Errors | 에러 횟수 | > 0 (5분 내) |
| Throttles | 스로틀링 횟수 | > 0 |
| ConcurrentExecutions | 동시 실행 수 | > 80% 한도 |

### 5.3 CloudWatch 알람 설정

```bash
# 에러 알람
aws cloudwatch put-metric-alarm \
  --alarm-name "my-agent-errors" \
  --metric-name Errors \
  --namespace AgentCore \
  --statistic Sum \
  --period 300 \
  --threshold 1 \
  --comparison-operator GreaterThanOrEqualToThreshold \
  --evaluation-periods 1 \
  --alarm-actions "arn:aws:sns:REGION:ACCOUNT_ID:agent-alerts"

# 응답 시간 알람
aws cloudwatch put-metric-alarm \
  --alarm-name "my-agent-latency" \
  --metric-name Duration \
  --namespace AgentCore \
  --statistic Average \
  --period 300 \
  --threshold 30000 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 2 \
  --alarm-actions "arn:aws:sns:REGION:ACCOUNT_ID:agent-alerts"
```

### 5.4 로그 모니터링

```bash
# CloudWatch Logs에서 에이전트 로그 확인
aws logs tail /aws/agentcore/my-agent --follow

# 에러 로그만 필터링
aws logs filter-log-events \
  --log-group-name /aws/agentcore/my-agent \
  --filter-pattern "ERROR"
```

---

## 6. 프로덕션 검증

### 6.1 프로덕션 준비 체크리스트

**인프라:**
- [ ] 배포 성공 및 정상 동작 확인
- [ ] 오토스케일링 설정 확인
- [ ] 리전 및 가용 영역 설정 확인

**모니터링:**
- [ ] CloudWatch 메트릭 수집 확인
- [ ] 알람 설정 완료
- [ ] 로그 수집 확인
- [ ] 트레이싱 활성화 확인

**보안:**
- [ ] IAM 역할/정책 최소 권한 확인
- [ ] 시크릿 관리 (Secrets Manager) 확인
- [ ] Cognito 인증 설정 확인
- [ ] 데이터 암호화 확인

**운영:**
- [ ] 롤백 절차 문서화
- [ ] 장애 대응 절차 문서화
- [ ] 비용 모니터링 설정
- [ ] 온콜/담당자 지정

### 6.2 비용 추정

| 항목 | 단가 | 예상 사용량 | 월 비용 |
|------|------|------------|---------|
| Bedrock (입력 토큰) | 모델별 상이 | - | - |
| Bedrock (출력 토큰) | 모델별 상이 | - | - |
| AgentCore Runtime | 실행 시간 기반 | - | - |
| AgentCore Memory | 저장/조회 기반 | - | - |
| CloudWatch | 로그/메트릭 | - | - |
| Cognito | MAU 기반 | - | - |
| **합계** | | | **-** |

### 6.3 운영 문서 템플릿

```markdown
# [프로젝트명] 운영 가이드

## 1. 시스템 개요
- 아키텍처 다이어그램
- 주요 컴포넌트 및 엔드포인트

## 2. 배포
- 배포 명령어: `agentcore launch`
- 환경 변수 목록
- 롤백 절차: 이전 버전 재배포

## 3. 모니터링
- CloudWatch 대시보드 URL
- 주요 메트릭 및 알람
- 로그 위치: `/aws/agentcore/{agent-name}`

## 4. 장애 대응
- 에스컬레이션 경로
- 일반적인 장애 시나리오 및 대응
- 롤백 절차

## 5. 비용 관리
- 월간 비용 추정
- 비용 최적화 방안
- 비용 알람 설정
```

---

## Operations 완료 체크리스트

- [ ] AgentCore 배포 성공
- [ ] 인증 설정 완료 (Cognito, 필요 시)
- [ ] 배포 검증 완료 (기본 동작, 도구 호출, SSE 스트리밍, 에러 처리)
- [ ] 모니터링 설정 완료 (메트릭, 알람, 로그, 트레이싱)
- [ ] 프로덕션 준비 체크리스트 통과
- [ ] 비용 추정 완료
- [ ] 운영 문서 작성 완료
- [ ] 고객 인수 테스트 완료
- [ ] 고객 최종 승인

**프로그램 마무리:** `readSteering("accelerate-agentic-ai", "program-guide.md")` → Phase 4: Wrap-up
