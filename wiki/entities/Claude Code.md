---
title: "Claude Code"
type: entity
category: tool
sources: [raw/notes/하네스 엔지니어링.md, raw/articles/2026-04-15-karpathy-llm-wiki.md]
---

# Claude Code

Anthropic의 CLI 기반 AI 코딩 에이전트.

## 이 볼트에서의 역할

- LLM Wiki의 **프로그래머** 역할: wiki 파일 작성·유지
- 하네스 엔지니어링의 **주요 적용 대상**: `CLAUDE.md`로 행동 제어
- `/graphify` 스킬을 통해 그래프 파이프라인 실행

## CLAUDE.md와의 관계

`CLAUDE.md`는 Claude Code가 매 세션 자동으로 읽는 Context File이다. 하네스(행동 제어)와 LLM Wiki 스키마(wiki 운영 규칙)를 동시에 담당한다.

## 관련

- [[concepts/하네스 엔지니어링]]
- [[concepts/Context File]]
- [[concepts/LLM Wiki 패턴]]
