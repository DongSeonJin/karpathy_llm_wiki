---
title: "하네스 엔지니어링"
type: summary
source: raw/notes/하네스 엔지니어링.md
channel: 캐슬 AI
date: 2026-04-06
concepts: [하네스 엔지니어링, Context Corruption, Auto Enforcement System, Garbage Collection, Context File]
entities: [Claude Code]
---

# 하네스 엔지니어링 — 요약

## 핵심 아이디어

AI 에이전트가 실수를 반복하지 않도록 **구조적 환경을 설계**하는 방법론. 프롬프트로 부탁하는 게 아니라, 구조적으로 실수가 불가능하게 만드는 것이 핵심 철학.

> "모델 성능 탓을 하기 전에 내가 설계한 하네스를 먼저 점검하라"

## 왜 필요한가

| 문제 | 해결 |
|---|---|
| Context Corruption (긴 대화 시 앞 내용 망각) | 온보딩 문서(claude.md)를 매 세션마다 자동으로 읽게 설정 |
| 엉뚱한 작업 수행 (DB 삭제 등) | Hook으로 자동 검사 → 실수 원천 봉쇄 |

## 하네스 3대 구성 요소

1. **Context File** (`claude.md`, `agent.md`) — AI에게 지침과 지도를 제공
2. **자동 강제 시스템** — 린터·테스트로 코드 품질 자동 교정 루프
3. **가비지 컬렉션** — 주기적 코드 청소, 나쁜 패턴 재발 방지

## 실무 적용

1. `claude.md` 작성 및 설정
2. Pre-commit 훅 설정
3. 린터/테스트 자동화 파이프라인 구축

## 개발자 역할 변화

기존 "코드를 직접 작성하는 실행자" → "AI가 잘 작동하는 환경을 설계하는 감독자"

## 관련 개념

- [[concepts/하네스 엔지니어링]] — 핵심 개념
- [[concepts/Context Corruption]] — 핵심 문제
- [[concepts/Context File]] — 해결 수단
- [[concepts/Auto Enforcement System]] — 해결 수단
- [[entities/Claude Code]] — 주요 적용 대상
