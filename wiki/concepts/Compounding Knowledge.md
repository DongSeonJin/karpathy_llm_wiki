---
title: "Compounding Knowledge"
type: concept
sources: [raw/articles/2026-04-15-karpathy-llm-wiki.md]
related: [LLM Wiki 패턴, Persistent Wiki]
---

# Compounding Knowledge

지식이 복리처럼 축적되는 구조. 소스가 추가될수록 기존 wiki와 연결되며 더 풍부해진다.

## 메커니즘

새 소스 ingest 시:
1. 새 summary 생성
2. 기존 concept/entity 페이지 업데이트
3. 새로운 cross-reference 형성
4. synthesis 강화

결과: 소스 N개의 가치 > 소스 1개 × N

## RAG와의 차이

RAG는 누적 효과가 없다. 매번 원문에서 재발견한다.
LLM Wiki는 이미 연결되어 있다.

## 관련

- [[LLM Wiki 패턴]]
- [[Persistent Wiki]]
- [[RAG vs LLM Wiki]]
