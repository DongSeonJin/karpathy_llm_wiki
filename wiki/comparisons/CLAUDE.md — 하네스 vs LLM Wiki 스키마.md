---
title: "CLAUDE.md — 하네스 vs LLM Wiki 스키마"
type: comparison
sources: [raw/notes/하네스 엔지니어링.md, raw/articles/2026-04-15-karpathy-llm-wiki.md]
date: 2026-04-20
---

# CLAUDE.md — 하네스 vs LLM Wiki 스키마

두 소스 모두 `CLAUDE.md`를 핵심 도구로 사용하지만 강조점이 다르다.

## 비교

| 관점 | 목적 | 내용 |
|---|---|---|
| 하네스 엔지니어링 | AI 행동 제어 | 금지 규칙, Hook 설정, 구조적 제약 |
| LLM Wiki | wiki 운영 스키마 | 디렉터리 구조, ingest/query/lint 워크플로, 파일 컨벤션 |

## 교차점

**같은 파일이 두 역할을 동시에 수행한다.**

- 하네스: `fleeting/ 읽지 않는다`, `raw/ 수정하지 않는다` → 구조적 제약
- LLM Wiki: `ingest 시 summaries/ 작성`, `log.md에 기록` → 운영 워크플로

## 결론

이 볼트의 CLAUDE.md는 하네스 엔지니어링과 LLM Wiki 패턴이 자연스럽게 합쳐진 결과물이다. 두 패턴은 서로 다른 문제를 풀지만, Context File이라는 동일한 수단을 사용한다.

## 관련

- [[concepts/Context File]]
- [[concepts/하네스 엔지니어링]]
- [[concepts/LLM Wiki 패턴]]
