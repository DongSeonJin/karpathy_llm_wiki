---
title: "소스 통합 인사이트"
type: synthesis
updated: 2026-04-21
sources: [raw/articles/2026-04-15-karpathy-llm-wiki.md, raw/notes/2026-04-07-harness-engineering.md, raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md]
---

# 소스 통합 인사이트

## 핵심 발견: 두 패턴의 수렴

현재 두 소스(LLM Wiki, 하네스 엔지니어링)는 표면상 다른 주제를 다루지만, 깊이 보면 **같은 문제의 두 면**이다.

> "AI를 어떻게 믿을 수 있는 파트너로 만드는가"

- **LLM Wiki**: AI가 지식을 잘 쌓고 유지하게 하는 구조
- **하네스 엔지니어링**: AI가 실수하지 않고 올바르게 행동하게 하는 구조

둘 다 **프롬프트가 아닌 구조**로 문제를 푼다.

## CLAUDE.md의 통합적 역할

가장 구체적인 수렴 지점은 CLAUDE.md다.

- 하네스: AI 행동을 제약하는 Context File
- LLM Wiki: wiki 운영 규칙을 정의하는 Schema

이 볼트의 CLAUDE.md는 두 역할을 동시에 수행한다. 이것은 의도된 설계가 아니라 두 패턴이 자연스럽게 합쳐진 결과다.

## 개발자 역할 변화라는 공통 맥락

하네스 엔지니어링은 개발자 역할이 **실행자 → 감독자**로 바뀐다고 말한다.
LLM Wiki는 인간의 역할이 **소스 큐레이션과 좋은 질문**이라고 말한다.

두 관점 모두 같은 방향을 가리킨다: **인간은 방향을 잡고, AI는 grunt work를 한다.**

## 세 번째 수렴: 토큰 최적화는 하네스의 실전 구현

세 번째 소스(Claude Code 토큰 최적화)는 하네스 엔지니어링의 추상 철학을 **측정 가능한 기법**으로 구체화한다.

| 하네스 개념 | 토큰 최적화 구현 |
|---|---|
| Context File 최적화 | claude.md 200줄 이내 + .claudeignore |
| Auto Enforcement System | 3-에이전트 팀의 역할 제약 |
| Garbage Collection | /compact, /clear 세션 관리 |

- **LLM Wiki**: 지식을 잘 쌓는 구조
- **하네스 엔지니어링**: AI가 실수하지 않는 구조
- **토큰 최적화**: 위 두 구조를 효율적으로 운용하는 실전 기법

세 패턴은 계층 관계를 형성한다: LLM Wiki(what) → 하네스(how) → 토큰 최적화(how efficiently).

## 네 번째 소스: 두 토큰 최적화 소스의 수렴

헤이제임스(영상)와 omitsu(아티클)는 독립적으로 같은 기법들에 도달했다.

**두 소스 공통**: .claudeignore, CLAUDE.md 200줄 이내, MCP 최소화, /compact·/clear
**헤이제임스 고유**: qmd 코드베이스 인덱싱(탐색 92% 절감), 3-에이전트 팀 구조
**omitsu 고유**: Plan Mode(Shift+Tab), 5W1H 프롬프트 프레임워크, 서브에이전트 위임, /context·/cost 측정

두 소스를 합치면 완전한 토큰 최적화 스택이 구성된다:
- **측정** → /context, /cost
- **노이즈 제거** → .claudeignore, CLAUDE.md 트리밍, MCP 정리
- **탐색 효율화** → qmd 인덱싱, 서브에이전트 위임
- **실행 제어** → Plan Mode, 3-에이전트 팀, 5W1H 프롬프트

## 앞으로 소스가 쌓이면 탐색할 질문들

- 하네스 엔지니어링과 LLM Wiki를 결합한 최적의 CLAUDE.md 구조는?
- Lint(LLM Wiki)와 Garbage Collection(하네스)을 통합 운영할 수 있는가?
- 다른 AI 에이전트 도구(Cursor, Aider 등)에도 같은 패턴이 적용되는가?
- 3-에이전트 구조와 LLM Wiki ingest 워크플로를 결합할 수 있는가?
- Plan Mode와 3-에이전트 팀 구조는 언제 각각 적합한가?
