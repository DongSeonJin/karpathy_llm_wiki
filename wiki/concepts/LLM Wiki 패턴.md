---
title: "LLM Wiki 패턴"
type: concept
sources: [raw/articles/2026-04-15-karpathy-llm-wiki.md]
related: [RAG vs LLM Wiki, Compounding Knowledge, Persistent Wiki]
---

# LLM Wiki 패턴

소스를 추가할 때 LLM이 한 번 읽고 wiki를 구축하는 지식 관리 패턴. 이후 질문은 원문이 아닌 wiki를 통해 답한다.

## RAG와의 차이

RAG는 **검색** — 질문마다 원문을 재탐색한다.
LLM Wiki는 **컴파일** — 소스 추가 시 1회 처리 후 wiki에 영구 저장한다.

## 핵심 특성

- **영속성**: wiki 파일로 지식이 남는다
- **복리 축적**: 소스가 쌓일수록 wiki가 풍부해진다
- **cross-reference**: 이미 연결되어 있다 — 매번 재발견할 필요 없음
- **유지 비용 근사 0**: LLM이 bookkeeping을 담당

## 구성 요소

- `raw/` — 불변 원본 소스
- `wiki/` — LLM이 작성·유지하는 마크다운 파일
- `CLAUDE.md` — LLM 행동 규칙 및 워크플로 스키마

## 관련

- [[RAG vs LLM Wiki]] — 상세 비교
- [[Compounding Knowledge]] — 복리 축적 메커니즘
- [[summaries/2026-04-15-karpathy-llm-wiki]] — 출처
- [[entities/Andrej Karpathy]] — 제안자
