---
title: "Context File"
type: concept
sources: [raw/notes/하네스 엔지니어링.md, raw/articles/2026-04-15-karpathy-llm-wiki.md]
related: [하네스 엔지니어링, Context Corruption, LLM Wiki 패턴]
---

# Context File

AI에게 지침과 지도를 제공하는 문서. 두 소스 모두에서 핵심 구성 요소로 등장한다.

## 형태

- `claude.md` / `CLAUDE.md` — Claude Code용
- `agent.md` / `AGENTS.md` — Codex/OpenCode용

## 두 관점에서의 역할

**하네스 엔지니어링 관점:**
Context Corruption을 방지하기 위해 매 세션마다 자동으로 읽히는 지침 파일.

**LLM Wiki 관점:**
Wiki 구조, 컨벤션, 워크플로를 정의하는 Schema. LLM이 일관된 wiki 유지보수 동작을 하도록 만드는 설정.

## 교차점

두 패턴 모두 CLAUDE.md를 핵심 도구로 사용한다. 하네스는 AI 행동 제어에, LLM Wiki는 wiki 운영 규칙에 집중하지만 **같은 파일**이 두 역할을 동시에 수행한다.

## 관련

- [[하네스 엔지니어링]]
- [[LLM Wiki 패턴]]
- [[comparisons/CLAUDE.md — 하네스 vs LLM Wiki 스키마]]
