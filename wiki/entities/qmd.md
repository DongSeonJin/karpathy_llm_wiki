---
title: "qmd"
type: entity
category: tool
sources: [raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md]
---

# qmd

코드베이스를 사전 인덱싱하여 Claude Code의 파일 탐색 토큰을 92% 절감하는 도구.

- **저장소**: https://github.com/tobi/qmd
- **작동 방식**: 프로젝트의 모든 파일을 미리 색인 → Claude가 검색 엔진처럼 정확한 위치를 바로 조회
- **연동**: MCP 서버로 Claude Code에 연결

## 절감 효과

| 작업 | 기존 | 적용 후 | 절감 |
|---|---|---|---|
| 인증 로직 탐색 | 2,700 토큰 | 250 토큰 | 91% |
| 결제 버그 수정 | 3,950 토큰 | 400 토큰 | 90% |
| DB 스키마 파악 | 6,000 토큰 | 400 토큰 | 93% |
| **평균** | — | — | **92%** |

## 설정

1. qmd 설치 및 프로젝트 인덱싱
2. Claude Code에 MCP 서버로 연결
3. `claude.md`에 추가: "파일 읽기 전 항상 qmd로 먼저 검색"

## 관련

- [[concepts/Claude Code 토큰 최적화]]
- [[entities/Claude Code]]
