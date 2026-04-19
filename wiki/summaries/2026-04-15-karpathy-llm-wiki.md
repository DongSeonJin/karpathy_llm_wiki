---
title: "LLM Wiki 패턴"
type: summary
source: raw/articles/2026-04-15-karpathy-llm-wiki.md
author: Andrej Karpathy
date: 2026-04-15
concepts: [LLM Wiki, RAG, Compounding Knowledge, Persistent Wiki, Ingest, Query, Lint]
entities: [Andrej Karpathy, Obsidian, Vannevar Bush Memex, qmd]
---

# LLM Wiki 패턴 — 요약

## 핵심 아이디어

RAG는 질문할 때마다 원문을 재탐색한다. LLM Wiki는 다르다 — 소스를 추가할 때 LLM이 한 번 읽고 wiki를 구축하며, 이후 질문은 이미 정리된 wiki를 통해 답한다. **지식이 컴파일되어 축적된다.**

## 아키텍처 3레이어

- **Raw sources** — 불변 원본. LLM은 읽기만 함.
- **Wiki** — LLM이 작성·유지하는 마크다운 파일 모음. Summaries, entity pages, concept pages, comparisons, overview, synthesis.
- **Schema (CLAUDE.md)** — LLM의 행동 규칙과 워크플로를 정의하는 설정 파일.

## Operations

- **Ingest**: 소스 추가 → LLM이 읽고 → summary 작성 → 관련 페이지 업데이트 → index/log 기록. 소스 1개가 wiki 10~15개 페이지에 영향.
- **Query**: wiki 탐색 → 답변 생성 → 가치 있는 답변은 wiki에 저장.
- **Lint**: 주기적 wiki 점검 — 모순, 오래된 클레임, 고아 페이지, 누락된 cross-reference.

## Index / Log

- `index.md` — 콘텐츠 카탈로그. LLM이 query 시 먼저 읽는 진입점.
- `log.md` — append-only 운영 일지. `## [YYYY-MM-DD] ingest | 제목` 형식으로 파싱 가능.

## 왜 작동하는가

유지보수 비용이 0에 가깝다. LLM은 cross-reference 업데이트를 빠뜨리지 않고, 지치지 않는다. 인간의 역할은 소스 큐레이션과 좋은 질문을 하는 것.

## 관련 개념

- [[concepts/LLM Wiki 패턴]] — 핵심 개념
- [[concepts/RAG vs LLM Wiki]] — 비교
- [[concepts/Compounding Knowledge]] — 복리 축적 구조
- [[entities/Andrej Karpathy]] — 저자
- [[entities/Obsidian]] — wiki 브라우저
- [[entities/Vannevar Bush Memex]] — 개념적 선조
