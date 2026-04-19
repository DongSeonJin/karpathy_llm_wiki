---
title: "RAG vs LLM Wiki"
type: concept
sources: [raw/articles/2026-04-15-karpathy-llm-wiki.md]
related: [LLM Wiki 패턴, Compounding Knowledge]
---

# RAG vs LLM Wiki

## 비교

| | RAG | LLM Wiki |
|---|---|---|
| 지식 합성 시점 | 질문할 때마다 | 소스 추가 시 1회 |
| 결과 보존 | 채팅 기록에만 | wiki 파일로 영구 저장 |
| 누적 효과 | 없음 | 소스가 쌓일수록 wiki가 풍부해짐 |
| 교차 참조 | 매번 재발견 | 이미 연결되어 있음 |
| 유지 비용 | LLM이 매번 처음부터 | LLM이 변경분만 업데이트 |
| 토큰 비용 | 질문마다 원문 전체 | wiki(압축본)만 읽음 |

## 핵심 차이

RAG는 **검색**, LLM Wiki는 **컴파일**이다.

## 관련

- [[LLM Wiki 패턴]]
- [[Compounding Knowledge]]
