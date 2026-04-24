---
title: "Plan Mode"
type: concept
sources: [raw/articles/2026-04-21-how-i-reduced-claude-code-token-consumption-by-50%.md]
related: [Claude Code 토큰 최적화, 3-에이전트 팀 구조, 하네스 엔지니어링]
---

# Plan Mode

Claude Code에서 `Shift+Tab`으로 활성화하는 계획 전용 모드. 아무 파일도 변경하지 않고 단계별 실행 계획만 먼저 출력한다.

## 워크플로우

```
Shift+Tab (Plan mode 활성화)
→ 작업 지시
→ Claude가 계획 출력 (파일 수정 없음)
→ 사용자가 계획 검토·수정
→ Shift+Tab (Plan mode 해제)
→ 실행
```

## 토큰 절감 원리

일반 모드에서는 Claude Code가 시행착오 방식으로 실행 → 오류 수정 → 반복하며 매 반복마다 토큰 소비.

Plan mode에서는 실행 전에 방향 오류를 잡아 잘못된 접근법으로 낭비되는 토큰을 제거.

**절감 효과**: 20~30%

## 사용 기준

| 상황 | Plan Mode |
|---|---|
| 변수 이름 변경, 오타 수정 등 단순 변경 | 건너뛰기 |
| 3개 이상 파일에 영향을 미치는 작업 | 반드시 사용 |
| "사용자 인증 추가" 같은 복잡한 요청 | 사용 |

## 3-에이전트 팀 구조와의 관계

Plan mode는 단일 에이전트 컨텍스트에서 [[concepts/3-에이전트 팀 구조]]의 아키텍트 역할을 수동으로 수행하는 방식이다.

- Plan mode: 인간이 직접 계획 검토
- 3-에이전트 구조: 아키텍트 에이전트가 자동으로 브리프 작성 후 빌더에게 전달

## 관련

- [[concepts/Claude Code 토큰 최적화]]
- [[concepts/3-에이전트 팀 구조]]
- [[summaries/2026-04-21-claude-code-token-50-percent]]
