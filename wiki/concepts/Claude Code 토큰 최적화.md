---
title: "Claude Code 토큰 최적화"
type: concept
sources: [raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md, raw/articles/2026-04-21-how-i-reduced-claude-code-token-consumption-by-50%.md]
related: [하네스 엔지니어링, 3-에이전트 팀 구조, qmd, Plan Mode, 서브에이전트 위임]
---

# Claude Code 토큰 최적화

Claude Code 사용 시 발생하는 토큰 낭비를 구조적으로 줄이는 방법론. 두 소스(헤이제임스, omitsu)에서 검증된 기법들을 통합.

## 문제 진단

중간 규모 프로젝트에서 토큰의 **80~90%가 코드 탐색**에 소비된다. 실제 코드 작성에는 10%만 사용.

주요 낭비 원인:
- 매 질문마다 반복되는 glob → grep → 파일 읽기 탐색 루프
- 과도하게 긴 `claude.md` (매 대화마다 전체 로드)
- 불필요한 파일 (node_modules, 빌드 산출물) 읽기
- 단일 에이전트의 범위 이탈 (요청하지 않은 파일까지 읽고 수정)
- 모호한 지시로 인한 반복 왕복
- 상시 가동 MCP 서버의 도구 정의 오버헤드
- 장시간 세션의 대화 기록 누적

측정 도구: `/context` (컨텍스트 구성 시각화) + `/cost` (세션 비용 확인)

## 기법 전체 목록

| 기법 | 절감 | 소스 |
|---|---|---|
| `.claudeignore` 설정 | 30~40% | 두 소스 공통 |
| Plan Mode (Shift+Tab) | 20~30% | omitsu |
| 구체적 프롬프트 (5W1H) | 15~25% | omitsu |
| 코드베이스 사전 인덱싱 (qmd) | 탐색 토큰 92% | 헤이제임스 |
| MCP 서버 최소화 | 5~10% | 두 소스 공통 |
| /compact·/clear 세션 관리 | 10~15% | 두 소스 공통 |
| CLAUDE.md 200줄 이내 | 5~10% | 두 소스 공통 |
| 3-에이전트 팀 구조 | 범위 이탈 차단 | 헤이제임스 |
| 서브에이전트 위임 (3파일+) | 탐색 분리 | omitsu |

## 핵심 기법 상세

### .claudeignore — 가장 즉각적인 효과
불필요한 파일 읽기를 원천 차단. Next.js 프로젝트에서 `.next/` 제외만으로 30~40% 절감.

### Plan Mode — 실행 전 계획 검토
→ [[concepts/Plan Mode]] 참조

### 코드베이스 사전 인덱싱
→ [[entities/qmd]] 참조

### 역할 분리 구조
→ [[concepts/3-에이전트 팀 구조]] 참조

### 서브에이전트 위임
→ [[concepts/서브에이전트 위임]] 참조

## 하네스 엔지니어링과의 관계

토큰 최적화는 [[concepts/하네스 엔지니어링]]의 실용적 구현이다.
- Context File(claude.md) 최적화 = 하네스의 Context File 구성 요소
- 3-에이전트 구조 = Auto Enforcement System의 역할 기반 버전
- .claudeignore = 불필요한 컨텍스트 차단(Garbage Collection)

## 관련

- [[concepts/Plan Mode]]
- [[concepts/3-에이전트 팀 구조]]
- [[concepts/서브에이전트 위임]]
- [[entities/qmd]]
- [[entities/Claude Code]]
- [[summaries/2026-04-21-claude-code-token-optimization]]
- [[summaries/2026-04-21-claude-code-token-50-percent]]
