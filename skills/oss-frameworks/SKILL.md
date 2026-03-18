---
name: oss-frameworks
description: 오픈소스 에이전트 프레임워크 비교 가이드 - Strands, LangGraph, CrewAI, AutoGen. "프레임워크 비교", "LangGraph", "CrewAI", "AutoGen", "어떤 프레임워크"를 언급할 때 사용합니다.
---

# 오픈소스 에이전트 프레임워크 비교

주요 오픈소스 에이전트 프레임워크를 비교하고 선택 기준을 제시합니다.

---

## 프레임워크 비교 요약

| 항목 | Strands Agents | LangGraph | CrewAI | AutoGen |
|------|---------------|-----------|--------|---------|
| **개발사** | AWS | LangChain | CrewAI | Microsoft |
| **언어** | Python | Python/JS | Python | Python/.NET |
| **접근 방식** | 코드 우선 | 그래프 기반 | 역할 기반 | 대화 기반 |
| **AWS 통합** | 네이티브 | 플러그인 | 플러그인 | 플러그인 |
| **MCP 지원** | 네이티브 | 커뮤니티 | 제한적 | 제한적 |
| **멀티에이전트** | 4패턴 내장 | 그래프 기반 | Crew/Task | GroupChat |
| **프로덕션 배포** | AgentCore | LangServe | 자체 | 자체 |
| **라이선스** | Apache 2.0 | MIT | MIT | MIT |

---

## 1. Strands Agents (AWS)

**공식:** https://strandsagents.com/latest/documentation/
**GitHub:** https://github.com/strands-agents

### 강점
- **AWS 통합 최강**: Bedrock, AgentCore, S3, DynamoDB 네이티브
- **MCP 네이티브**: Model Context Protocol 내장 지원
- **4가지 멀티에이전트 패턴**: Agents-as-Tools, Swarm, Graph, Workflow
- **AgentCore 배포**: `agentcore launch` 한 줄로 프로덕션 배포
- **코드 우선**: 프레임워크 추상화 최소, Python 코드로 직접 제어

### 적합한 경우
- AWS 인프라 기반 프로젝트
- Bedrock 모델 사용
- AgentCore로 프로덕션 배포 계획
- MCP 서버 통합 필요

```python
from strands import Agent
from strands.models.bedrock import BedrockModel

agent = Agent(
    model=BedrockModel(model_id="us.anthropic.claude-sonnet-4-20250514-v1:0"),
    tools=[my_tool],
)
```

---

## 2. LangGraph (LangChain)

**공식:** https://langchain-ai.github.io/langgraph/

### 강점
- **그래프 기반 워크플로우**: 복잡한 조건부 분기와 사이클 지원
- **멀티모달**: 텍스트, 이미지, 오디오 통합
- **풍부한 에코시스템**: LangChain 도구/체인 호환
- **LangSmith 통합**: 관측성, 평가, 디버깅

### 적합한 경우
- 복잡한 워크플로우 (조건부 분기, 사이클)
- 다양한 모델 프로바이더 사용
- LangChain 에코시스템 활용
- 멀티모달 에이전트

---

## 3. CrewAI

**공식:** https://docs.crewai.com/

### 강점
- **역할 기반 설계**: Agent에 역할, 목표, 배경 부여
- **멀티에이전트 협업 최적**: Crew + Task 구조
- **직관적 API**: 비개발자도 이해하기 쉬운 구조
- **프로세스 유형**: Sequential, Hierarchical 지원

### 적합한 경우
- 팀 협업 시뮬레이션
- 역할 기반 멀티에이전트
- 빠른 프로토타이핑

---

## 4. AutoGen (Microsoft)

**공식:** https://microsoft.github.io/autogen/

### 강점
- **대화 기반 멀티에이전트**: GroupChat 패턴
- **코드 실행**: 자동 코드 생성 + 실행
- **Microsoft 에코시스템**: Azure, M365 통합
- **커스터마이즈**: 유연한 에이전트 정의

### 적합한 경우
- Azure/Microsoft 인프라
- 코드 생성 + 실행 자동화
- 복잡한 다자간 대화 시뮬레이션

---

## 선택 기준 결정 트리

```
AWS 인프라를 사용하는가?
  → Yes: Strands Agents (AgentCore 배포, Bedrock 네이티브)

복잡한 그래프 워크플로우가 필요한가?
  → Yes: LangGraph (조건부 분기, 사이클)

팀 역할 기반 협업이 핵심인가?
  → Yes: CrewAI (직관적 역할 설계)

Azure/Microsoft 생태계인가?
  → Yes: AutoGen (Azure 통합)

빠른 프로토타이핑이 목적인가?
  → CrewAI 또는 Strands (간결한 API)

프로덕션 배포가 중요한가?
  → Strands + AgentCore (managed 배포)
  → LangGraph + LangServe
```

---

## 이 프로젝트의 기본 선택: Strands Agents

이 가이드(accelerate-agentic-ai)는 **Strands Agents SDK + Amazon Bedrock AgentCore**를 기본 스택으로 사용합니다.

**이유:**
1. AWS Acceleration Program의 기술 스택
2. AgentCore로 프로덕션 배포 용이
3. Bedrock 모델 네이티브 지원
4. MCP 서버 네이티브 지원
5. 4가지 멀티에이전트 패턴 내장
