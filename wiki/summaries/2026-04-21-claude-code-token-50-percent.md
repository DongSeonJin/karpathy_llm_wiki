---
title: "How I Reduced Claude Code Token Consumption by 50%"
type: summary
source: raw/articles/2026-04-21-how-i-reduced-claude-code-token-consumption-by-50%.md
author: omitsu
site: 32blog.com
published: 2026-02-26
captured: 2026-04-21
---

# How I Reduced Claude Code Token Consumption by 50%

Claude Code 토큰 낭비의 5대 원인을 진단하고 실전 기법 6가지로 40~55% 절감하는 방법.

---

## 5대 낭비 원인

1. **과도한 컨텍스트 읽기** — node_modules, 빌드 산출물 등 불필요한 파일 읽기
2. **모호한 지시** — "더 보기 좋게 만들어 보세요" → 4번의 왕복 발생
3. **상시 가동 MCP 서버** — 연결된 서버마다 모든 메시지에 도구 정의 추가
4. **과도하게 커진 CLAUDE.md** — 매 세션마다 전체 로드
5. **장시간 세션** — 대화 기록 누적으로 새 메시지마다 토큰 증가

측정 방법: `/context` (컨텍스트 구성 시각화) + `/cost` (세션 사용량 확인)

---

## 기법 1: .claudeignore (30~40% 절감)

가장 투자 대비 효과가 높은 기법. Next.js 프로젝트에서 `.next/` 하나만 제외해도 컨텍스트 30~40% 감소.

차단 대상: 빌드 산출물, node_modules, 캐시, 로그, 테스트 산출물, .env, DB 파일, 미디어/바이너리

주의: Claude Code가 실제로 편집해야 하는 소스 파일은 제외하지 말 것.

## 기법 2: Plan Mode (20~30% 절감)

`Shift+Tab`으로 활성화. 아무 변경 없이 계획만 먼저 출력.

```
Plan mode → 계획 출력 → 검토·수정 → Plan mode 해제 → 실행
```

- 단순 변경(변수 이름, 오타): 건너뛰기
- 3파일 이상에 영향을 미치는 작업: 반드시 사용

→ [[concepts/Plan Mode]] 참조

## 기법 3: 구체적 프롬프트 — 5W1H 프레임워크 (15~25% 절감)

보내기 전에 스스로 답해야 할 질문:
- **What**: 정확히 무엇을
- **Where**: 코드베이스의 어디에서
- **How**: 어떤 라이브러리·패턴으로
- **When**: 순서 제약 조건
- **Who**: 어떤 사용자 역할 (관련 시)

**규칙**: 메시지당 하나의 작업만 전송. 여러 작업을 묶으면 Claude가 모든 의존성을 동시에 컨텍스트에 유지하려 해 토큰 증가.

## 기법 4: MCP 서버 최소화 (5~10% 절감)

필요할 때 추가 → 완료 후 제거 방식.

```bash
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres postgresql://localhost/mydb
claude mcp remove postgres
claude mcp list
```

현재 연결 서버 확인: `/mcp`

## 기법 5: 세션 관리 /compact, /clear (10~15% 절감)

| 명령어 | 사용 시점 |
|---|---|
| `/compact` | 같은 작업 중 대화가 길어질 때, 컨텍스트 유지 필요 시 |
| `/clear` | 완전히 다른 작업으로 전환, 다음 날 재시작 시 |

## 기법 6: CLAUDE.md 200줄 이내 (5~10% 절감)

유지: 기술 스택(3줄), 디렉터리 구조 개요, 핵심 코딩 규칙, 현재 활성 작업
제거: 역사적 맥락, 완료된 작업 상세, 링크, 긴 면책 조항

세부 정보는 `docs/current-sprint.md` 같은 별도 파일로 분리 → Claude Code가 필요할 때만 읽게.

측정: `/context` 실행 후 CLAUDE.md가 컨텍스트의 10~15% 이상 차지하면 트리밍 필요.

## 보너스: 서브에이전트 위임

3개 이상 파일 탐색 시 서브에이전트에 위임 → 요약만 메인 컨텍스트로 반환.

- 적합: 파일 탐색, 의존성 조사, 테스트 실행 결과 요약
- 부적합: 1~2개 파일 단순 읽기 (오버헤드가 더 큼)

→ [[concepts/서브에이전트 위임]] 참조

---

## 효과 요약

| 기법 | 절감 |
|---|---|
| .claudeignore | 30~40% |
| Plan Mode | 20~30% |
| 구체적 프롬프트 | 15~25% |
| MCP 서버 정리 | 5~10% |
| /compact 활용 | 10~15% |
| CLAUDE.md 트리밍 | 5~10% |
| **합산 (복합 효과)** | **40~55%** |

---

## 관련

- [[concepts/Claude Code 토큰 최적화]] — 통합 개념 페이지
- [[concepts/Plan Mode]]
- [[concepts/서브에이전트 위임]]
- [[summaries/2026-04-21-claude-code-token-optimization]] — 헤이제임스 영상 (qmd, 3-에이전트)
- [[concepts/3-에이전트 팀 구조]]
