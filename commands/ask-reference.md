---
name: ask-reference
description: 에이전트 설계 관련 질문에 대해 적절한 공식 문서와 스킬을 추천합니다.
argument-hint: <에이전트 설계에 대한 질문>
---

# 레퍼런스 추천

에이전트 설계 관련 질문을 받아 적절한 공식 문서와 스킬을 추천합니다.

## 수행할 작업

사용자의 질문 "${question}"을 분석하여:

1. **관련 스킬 추천**: 질문에 가장 적합한 스킬을 안내합니다.
2. **공식 문서 추천**: 관련 공식 문서 URL을 제공합니다.
3. **MCP 서버 활용 안내**: 실시간으로 최신 문서를 조회할 수 있는 MCP 서버를 안내합니다.

## 추천 매핑

| 질문 키워드 | 추천 스킬 | 추천 문서 |
|------------|----------|----------|
| 설계 원칙, 좋은 에이전트 | agent-design-principles | Anthropic Building Effective Agents |
| 도구, tool, docstring | tool-design | Anthropic Tool Use Overview |
| 멀티에이전트, 패턴 | multi-agent-patterns | Strands Multi-Agent docs |
| 평가, 테스트, 성능 | evaluation | Strands Evaluation docs |
| 요구사항, 기획 | inception | AWS Agentic AI Foundations |
| 구현, 코딩 | construction | Strands SDK docs |
| 배포, 운영, AgentCore | operations | AWS Operationalizing Agentic AI |
| 프레임워크 비교 | oss-frameworks | AWS Agentic AI Frameworks |
| Anthropic, Claude | anthropic-references | Anthropic 공식 문서 |
| AWS, Strands, Bedrock | aws-references | AWS 공식 문서 |

## MCP 서버 안내

- **Strands SDK 관련**: `search_docs` / `fetch_doc` (strands-agents MCP)
- **AgentCore 관련**: `search_agentcore_docs` / `fetch_agentcore_doc` (agentcore MCP)

질문을 분석하고 가장 관련 있는 스킬과 문서를 추천해주세요.
