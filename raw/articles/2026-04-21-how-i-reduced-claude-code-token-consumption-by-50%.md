---
title: "How I Reduced Claude Code Token Consumption by 50%"
type: "article"
source_url: "https://32blog.com/en/claude-code/claude-code-token-cost-reduction-50-percent"
author:
  - "omitsu"
site: "32blog.com"
published: 2026-02-26
captured_at: 2026-04-21
description: "Cut Claude Code costs in half with practical techniques: .claudeignore setup, Plan mode, targeted prompts, MCP server management, and session strategy."
tags:
  - "raw/article"
---
클로드 코드(Claude Code)를 사용하기 시작하면 API 요금이 예상보다 훨씬 많이 나오는 경우가 있는데, 이는 레딧(Reddit)에서 가장 흔한 불만 중 하나입니다. 토큰이 어디에 쓰이는지 분석하고 필요한 부분을 수정하면 사용량을 크게 줄일 수 있습니다. 자세한 방법은 여기를 참조하세요.

`.claudeignore` 이 글을 끝까지 읽으시면 토큰이 어디로 가는지 파악하는 방법과 실제로 효과가 있었던 해결 방법( 플랜 모드, 프롬프트 관리, [MCP 서버](https://docs.anthropic.com/en/docs/claude-code/mcp) 관리, 세션 관리) 에 대한 구체적인 명령어를 알게 될 것입니다.

> [!note] Note
> 정보
> 
> 이 글은 예상치 못한 높은 Claude Code 요금 청구서를 받고 비용 증가 원인을 파악하고 싶은 개발자를 위한 글입니다. 변경 사항을 적용하기 전에 사용량 패턴을 파악하려는 경우 이 글부터 시작하세요.

## 클로드 코드에서 토큰을 가장 많이 소모하는 5가지 요인은 무엇인가요?

최적화를 시작하기 전에 먼저 무엇과 싸워야 하는지 알아야 합니다. 토큰 낭비는 주로 다섯 가지 원인에 집중되는 경향이 있습니다.

**1\. 과도한 컨텍스트 읽기:**`node_modules` Claude Code는 빌드 아티팩트 와 같이 건드릴 필요가 없는 파일을 읽으려고 시도할 수 있습니다 `.git`. 이는 종종 가장 큰 낭비 요소입니다.

**2\. 모호한 지시로 인해 "더 보기 좋게 만들어 보세요"라는 식의 의견 교환이 반복되면서** 클로드 코드는 명확한 설명을 위한 질문을 하게 됩니다. 한 번의 왕복으로 끝나야 할 작업이 결국 네 번의 왕복으로 이어집니다.

**3\. 상시 가동되는 MCP 서버:** 연결된 모든 MCP 서버는 모든 메시지에 도구 목록을 컨텍스트에 추가합니다. 5개의 서버가 지속적으로 실행되면 사용자가 아무 말도 하기 전에 턴당 수백 개의 토큰이 추가됩니다.

**4\. 과도하게 커진 CLAUDE.md 파일:** 프로젝트 지침 파일에 지난 6개월 동안의 모든 결정, 배경 설명, 맥락 정보가 포함되어 있다면 Claude Code는 매 세션 시작 시 이 모든 정보를 불러옵니다.

**5\. 장시간 세션을 실행하면** 대화 기록이 누적됩니다. 세션을 재설정하지 않고 오래 실행할수록 새 메시지를 보낼 때마다 더 많은 토큰이 소모됩니다.

<svg viewBox="0 0 860 126" role="img" aria-label="데이터 흐름: 낭비 식별 → 노이즈 제거 → 프롬프트 개선 → 50% 감소"><defs><marker id="fd-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M 0 0 L 10 5 L 0 10 z" fill="oklch(66% 0.15 145)"></path></marker></defs><g><rect x="30" y="40" width="140" height="56" rx="6" fill="oklch(12% 0.02 155)" stroke="oklch(80% 0.18 145)" stroke-width="1.5"></rect><text x="100" y="63" text-anchor="middle" fill="oklch(92% 0.01 155)" font-family="var(--font-mono), monospace" font-size="13" font-weight="600">Identify Waste</text> <text x="100" y="80" text-anchor="middle" fill="oklch(48% 0.015 155)" font-family="var(--font-mono), monospace" font-size="10">/context + /cost</text> <g><line x1="170" y1="68" x2="250" y2="68" stroke="oklch(66% 0.15 145)" stroke-width="1.5" marker-end="url(#fd-arrow)"></line><text x="210" y="60" text-anchor="middle" fill="oklch(66% 0.15 145)" font-family="var(--font-mono), monospace" font-size="9">Measure</text> <path id="fd-path-d0" d="M170,68 L250,68" fill="none" stroke="none"></path></g></g><g><rect x="250" y="40" width="140" height="56" rx="6" fill="oklch(12% 0.02 155)" stroke="oklch(80% 0.18 145)" stroke-width="1.5"></rect><text x="320" y="63" text-anchor="middle" fill="oklch(92% 0.01 155)" font-family="var(--font-mono), monospace" font-size="13" font-weight="600">Remove Noise</text><text x="320" y="80" text-anchor="middle" fill="oklch(48% 0.015 155)" font-family="var(--font-mono), monospace" font-size="10">.claudeignore</text> <g><line x1="390" y1="68" x2="470" y2="68" stroke="oklch(66% 0.15 145)" stroke-width="1.5" marker-end="url(#fd-arrow)"></line><text x="430" y="60" text-anchor="middle" fill="oklch(66% 0.15 145)" font-family="var(--font-mono), monospace" font-size="9">Filter</text> <path id="fd-path-d1" d="M390,68 L470,68" fill="none" stroke="none"></path></g></g><g><rect x="470" y="40" width="140" height="56" rx="6" fill="oklch(12% 0.02 155)" stroke="oklch(80% 0.18 145)" stroke-width="1.5"></rect><text x="540" y="63" text-anchor="middle" fill="oklch(92% 0.01 155)" font-family="var(--font-mono), monospace" font-size="13" font-weight="600">Improve Prompts</text> <text x="540" y="80" text-anchor="middle" fill="oklch(48% 0.015 155)" font-family="var(--font-mono), monospace" font-size="10">Plan mode + specifics</text> <g><line x1="610" y1="68" x2="690" y2="68" stroke="oklch(66% 0.15 145)" stroke-width="1.5" marker-end="url(#fd-arrow)"></line><text x="650" y="60" text-anchor="middle" fill="oklch(66% 0.15 145)" font-family="var(--font-mono), monospace" font-size="9">Optimize</text> <path id="fd-path-d2" d="M610,68 L690,68" fill="none" stroke="none"></path></g></g><g><rect x="690" y="40" width="140" height="56" rx="6" fill="oklch(12% 0.02 155)" stroke="oklch(80% 0.18 145)" stroke-width="1.5"></rect><text x="760" y="63" text-anchor="middle" fill="oklch(92% 0.01 155)" font-family="var(--font-mono), monospace" font-size="13" font-weight="600">50% Reduction</text> <text x="760" y="80" text-anchor="middle" fill="oklch(48% 0.015 155)" font-family="var(--font-mono), monospace" font-size="10">Half the bill</text></g></svg>

먼저 `/context` 컨텍스트 사용량을 시각적으로 분석하고 `/cost` 세션 사용 시간을 확인해 보세요. 이 두 가지를 통해 어떤 부분이 가장 큰 문제인지 빠르게 파악할 수 있습니다.

## .claudeignore 파일에서 불필요한 파일 읽기를 방지하는 방법은 무엇인가요?

가장 투자 대비 효과가 높은 해결책은 [`.claudeignore`](https://docs.anthropic.com/en/docs/claude-code/overview) 파일을 추가하는 것입니다. 이는 기존 방식과 정확히 동일하게 작동하며 `.gitignore`, Claude Code에게 어떤 경로를 완전히 건너뛸지 알려줍니다.

`.claudeignore` 프로젝트 루트 디렉터리에 다음 파일을 생성하세요.

```bash
# .claudeignore

# Build artifacts
.next/
dist/
build/
out/

# Dependencies
node_modules/
.pnp/
.pnp.js

# Caches
.cache/
.turbo/
*.tsbuildinfo

# Logs
*.log
npm-debug.log*

# Test output
coverage/
.nyc_output/

# Environment files (security too)
.env
.env.local
.env.*.local

# Database files
*.db
*.sqlite
prisma/migrations/

# Media and binaries
public/images/
*.png
*.jpg
*.gif
*.mp4
```

저장 후 다시 시작하세요 `claude`. Next.js 프로젝트에서 제외 `.next/` 기능만으로도 컨텍스트 크기가 일반적으로 30~40% 줄어듭니다. 핵심은 Claude Code가 읽을 필요가 없는 모든 것을 제외하는 것입니다. 단순히 명백한 것뿐만 아니라, 생성된 타입 정의, 테스트 픽스처, CLAUDE.md에 이미 포함된 문서 등 모두 제외 대상이 될 수 있습니다.

> [!note] Note
> 팁
> 
> Next.js 프로젝트에서 컨텍스트를 30~40% 줄여주는 효과를 보려면 이 작업을 가장 `.next/` 먼저 하세요. 다른 어떤 작업보다 빠르게 효과를 볼 수 있으며, 단점도 없습니다.`.claudeignore`

## 플랜 모드는 어떻게 토큰 소모량을 절반으로 줄일까요?

계획 모드(키로 전환 `Shift+Tab`)는 Claude Code에게 아무런 변경 없이 계획을 생성하도록 지시합니다. 이는 토큰 낭비의 가장 큰 원인인 시행착오 실행을 제거하기 때문에 가장 효과적인 기법 중 하나입니다.

일반 모드에서 Claude Code는 여러 가지 시도를 하고, 오류가 발생하면 이를 수정하며 반복 작업을 수행합니다. 각 반복 작업에는 토큰이 소모됩니다. 계획 모드에서는 먼저 단계별 계획을 출력합니다. 어떤 파일을 건드리고, 어떤 내용을 어떤 순서로 변경할지 보여줍니다. 사용자는 이 계획을 검토하고 불필요한 부분을 삭제한 후, 일반 모드로 돌아가 실행합니다.

```
# Plan mode workflow
Press Shift+Tab to enable Plan mode
→ Give your task
→ Claude outputs a plan (no files changed)
→ Review and adjust the plan
→ Press Shift+Tab to disable Plan mode
→ Execute with the refined plan in context
```

"사용자 인증 추가"와 같은 프롬프트의 경우, 계획 모드를 건너뛰면 Claude Code가 바로 실행에 들어가 잘못된 접근 방식을 선택할 가능성이 있으며, 사용자는 다섯 번의 메시지를 통해 이를 수정해야 합니다. 계획 모드는 토큰이 실행에 사용되기 전에 이러한 결정 사항을 미리 알려줍니다. 작업 규모가 클수록 시간 절약 효과도 커집니다.

## 모호한 프롬프트는 토큰 비용을 얼마나 증가시키나요?

응답 품질은 토큰 소비에 직접적이고 측정 가능한 영향을 미칩니다. 다음 예를 살펴보세요.

**비용이 많이 드는 프롬프트:**

```
"Add a login feature"
```

클로드 코드는 다음과 같은 질문을 던질 것입니다. 어떤 인증 라이브러리를 사용할까요? 쿠키 기반 세션일까요, 아니면 JWT일까요? 어떤 디렉터리를 사용할까요? UI 구성 요소는 어떻게 할까요? 코드를 작성하기 전에 이렇게 네 번의 질문과 답변이 오고 갑니다.

**효율적인 안내:**

```
"Add Google OAuth login using NextAuth.js v5.
JWT sessions. Implement in /app/auth/.
Add auth guards to the existing middleware.ts."
```

이 문제는 한 번의 과정으로 해결됩니다.

제가 사용하는 프레임워크는 코드를 보내기 전에 5W1H 질문에 답하는 것입니다. 즉, 정확히 무엇을, 코드베이스의 어디에서, 어떻게 (어떤 라이브러리나 패턴을 사용할지), 언제 (순서 제약 조건이 있는지), 누가 (관련이 있다면 어떤 사용자 역할인지)를 파악하는 것입니다. 제가 직접 답할 수 있는 질문이라면 Claude Code가 묻기 전에 프롬프트에 직접 입력합니다.

또 하나의 규칙: **메시지당 하나의 작업만 보내세요**. "로그인 추가, 테스트 작성, README 업데이트"와 같이 하나의 메시지로 보내면 Claude Code는 모든 작업을 동시에 처리해야 합니다. 이렇게 작업을 따로 보내면 전체 토큰 비용이 줄어듭니다. 언뜻 보기에는 직관적이지 않지만요.

> [!note] Note
> 경고
> 
> 여러 작업을 하나의 메시지로 묶는 것은 비효율적입니다. Claude Code는 모든 종속성을 한 번에 컨텍스트에 유지하려고 시도하는데, 이로 인해 작업을 개별적으로 전송하는 것보다 더 많은 토큰이 소모됩니다. "메시지당 하나의 작업" 방식이 속도는 느려 보일 수 있지만, 전체 토큰 비용을 꾸준히 줄여줍니다.

## MCP 서버를 실제로 필요할 때만 실행하는 방법은 무엇인가요?

[MCP 서버는](https://docs.anthropic.com/en/docs/claude-code/mcp) 강력하지만, 연결된 각 서버는 모든 메시지의 컨텍스트에 자체 도구 정의를 추가합니다. 서버가 5개 연결되어 있는 경우, 해당 서버를 사용하지 않을 때조차도 모든 메시지 교환 시마다 그 오버헤드 비용이 발생합니다.

Claude Code에서 \`slash\` 명령어를 사용하여 현재 실행 중인 서버를 확인하세요 `/mcp`. 연결된 모든 서버 목록을 보여주고 세션 중에 서버를 켜거나 끌 수 있습니다.

명령줄에서 서버를 추가하거나 제거하려면 다음 단계를 따르세요.

```bash
# Add a server
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres postgresql://localhost/mydb

# Remove it when you're done
claude mcp remove postgres

# List all configured servers
claude mcp list
```

해결책은 간단합니다. 매 세션마다 사용하는 서버만 유지하는 것입니다. 필요할 때 데이터베이스, GitHub 또는 기타 특수 서버를 추가하고 사용이 끝나면 제거하세요. 메시지당 절약되는 시간은 적지만, 하루 종일 작업할 경우 누적되어 상당한 효과를 볼 수 있습니다.

기본적으로 MCP 서버는 하나도 연결하지 않는 것이 좋습니다. 분석을 위해 데이터베이스를 쿼리해야 할 때 Postgres 서버를 추가하고 작업을 완료한 후 제거하세요. 이렇게 하면 메시지당 토큰 오버헤드를 최소화할 수 있습니다.

## \`/compact\`와 \`/clear\`는 언제 사용해야 할까요?

클로드 코드(Claude Code)는 대화 기록을 관리하는 두 가지 명령어를 제공합니다. 이 명령어를 적절한 시점에 사용하면 장시간 대화에서 상당한 효과를 볼 수 있습니다.

**`/compact`** 대화 내용을 요약하여 토큰 수를 줄이면서도 맥락을 유지합니다. 대화는 계속되지만, 더욱 효율적으로 진행됩니다.

**`/clear`** 대화를 완전히 초기화합니다. 백지상태로 돌아갑니다.

제가 결정하는 방법은 다음과 같습니다.

```
Use /compact when:
- You're still on the same task but the conversation is getting long
- You need the context from earlier in the session
- You're midway through a session (roughly 500+ exchanges)

Use /clear when:
- You're switching to a completely different task
- Previous conversation context is irrelevant
- You're starting a new feature from scratch
- You're resuming work the next day
```

피해야 할 실패 유형은 장시간 대화가 이어지도록 방치하는 것입니다. 대화 기록이 쌓일수록 Claude Code는 이전에 나눈 모든 대화 내용, 심지어 더 이상 관련성이 없는 내용까지 일관성 있게 유지하려고 합니다. 하지만 오래된 맥락은 응답 품질을 저하시킬 뿐만 아니라 비용을 증가시킬 수 있습니다.

확신이 서지 않을 때는, `/compact`. 작업을 전환할 때는, `/clear`.

## CLAUDE.md를 효율적으로 유지하려면 어떻게 해야 할까요?

[CLAUDE.md](https://www.anthropic.com/engineering/claude-code-best-practices) 파일은 매 세션 시작 시 컨텍스트에 로드됩니다. 만약 파일 길이가 600줄이라면, 작업을 시작하기도 전에 그 600줄에 대한 비용을 지불해야 합니다.

**이것을 잘라내세요:**

- 역사적 맥락("우리는 11월에 Y 때문에 X를 결정했습니다")
- 완료된 작업 세부 정보
- 클로드 코드(Claude Code)는 외부 문서 링크에 접근할 수 없습니다.
- 긴 면책 조항 또는 정책 설명

**이것을 보관하세요:**

- 기술 스택 (최대 3줄, 글머리 기호 목록)
- 디렉토리 구조 개요
- 가장 중요한 코딩 규칙
- 현재 활성화된 작업

목표: **200줄 미만**. 더 많은 줄이 필요한 경우, 세부 정보를 별도의 파일로 분할하고 `docs/` Claude Code가 필요에 따라 해당 파일을 읽도록 하십시오.

```markdown
# CLAUDE.md

## Stack
- Next.js 15 + TypeScript + Tailwind v4
- Prisma + PostgreSQL
- NextAuth.js v5

## Structure
- /app — App Router pages
- /components — UI components
- /lib — Utilities and helpers

## Conventions
- Default to Server Components
- Data fetching via Server Actions or Route Handlers
- Tests with Vitest + Testing Library

## Active Task
- Building user dashboard
- Details: docs/current-sprint.md
```

세부 정보는 에 있습니다 `docs/current-sprint.md`. Claude Code는 매 세션 시작 시가 아니라 필요할 때 해당 파일을 읽습니다.

## 실제로 얼마나 절약할 수 있을까요?

중간 규모의 Next.js 프로젝트에 하루 3~4시간을 투자하는 개발자의 현실적인 시간 배분은 다음과 같습니다.

| 기술 | 토큰 감소 |
| --- | --- |
| `.claudeignore` 설정 | 30~40% |
| 계획 모드 습관 | 20~30% |
| 신속하고 정확하게 | 15~25% |
| MCP 서버 정리 | 5~10% |
| `/compact` 용법 | 10~15% |
| CLAUDE.md 트리밍 | 5~10% |

이러한 요소들은 단순히 합산되는 것이 아니라 복합적으로 작용하여, 실제 결과는 최적화가 전혀 이루어지지 않은 기준선 대비 40~55%의 감소 효과를 가져옵니다. "50% 감소"는 달성 가능한 목표이며, 무리한 목표가 아닙니다.

클로드 코드의 비용은 사용량에 비례하여 증가합니다. 낭비를 줄이면 동일한 월 지출로 두 배의 생산적인 작업을 할 수 있습니다.

---

## 하위 에이전트 위임은 토큰 사용을 어떻게 분배할 수 있습니까?

Claude Code에는 하위 에이전트를 별도의 프로세스에서 실행하는 "에이전트" 도구가 있습니다. 이를 통해 메인 컨텍스트 창을 유지하면서 조사 및 탐색 작업을 별도의 프로세스로 위임할 수 있습니다.

**하위 에이전트가 효과적일 때:**

- 파일 탐색("이 함수는 어디에 사용되나요?")
- 파일 간 검색
- 의존성 조사
- 테스트 실행 및 결과 요약

하위 에이전트는 별도의 컨텍스트 창에서 실행됩니다. 메인 창에서 10개 파일에 대한 조사를 실행하면 모든 파일 내용이 컨텍스트를 사용합니다. 하위 에이전트에 작업을 위임하면 요약 정보만 메인 창으로 반환됩니다.

**CLAUDE.md 예시는 하위 에이전트 사용을 장려하기 위한 것입니다.**

```markdown
설명하다
# Cost Optimization
- Delegate exploration of 3+ files to subagents
- Run tests via subagents and return results only
- For code reviews, launch parallel subagents per file
```

> [!note] Note
> 경고
> 
> 하위 에이전트를 실행하는 데 토큰이 소모됩니다. 1~2개의 파일을 간단히 읽는 경우에는 직접 실행하는 것이 더 효율적입니다. 3개 이상의 파일을 탐색할 때부터는 위임 기능을 고려해 보세요.

---

## 자주 묻는 질문

### 클로드 코드의 실제 월 요금은 얼마인가요?

사용량에 따라 다릅니다. API 사용자는 토큰당 비용을 지불하며, 중간 규모 프로젝트에서 사용량이 많은 날에는 5~15달러가 발생할 수 있습니다. Anthropic은 Claude Code 사용량이 포함된 [Claude Max](https://docs.anthropic.com/en/docs/claude-code/overview) 구독 플랜도 제공합니다. 이 글에서 소개하는 최적화 방법은 결제 모델과 관계없이 적용됩니다. 토큰 사용량이 줄어들면 요금이 절감되거나 플랜 내에서 여유 자금이 생기기 때문입니다.

### .claudeignore 파일이 응답 품질에 영향을 미치나요?

아니요, Claude Code에 필요 없는 파일만 제외하면 문제없습니다. 빌드 아티팩트 `node_modules`, 바이너리 파일은 신호가 아니라 노이즈를 유발합니다. 이러한 파일을 제외하면 Claude Code가 관련 소스 파일에 집중하기 때문에 응답 품질이 향상됩니다. 다만, 과도하게 제외하는 것이 위험할 수 있습니다. Claude Code가 편집해야 하는 소스 파일은 제외하지 마세요.

### /compact와 /clear의 차이점은 무엇인가요?

`/compact` 대화 기록을 요약하여 토큰 수를 줄이면서 컨텍스트를 유지합니다. 요약된 내용을 바탕으로 세션이 계속됩니다. `/clear` 모든 내용을 삭제하고 처음부터 다시 시작합니다. `/compact` 대화가 길어질 때 작업 중간에 사용합니다. `/clear` 완전히 다른 작업으로 전환할 때 사용합니다. 더 많은 내장 명령은 [Claude 코드 명령 치트시트를 참조하세요.](https://32blog.com/en/claude-code/claude-code-commands-cheatsheet)

### 클로드 코드에 월별 지출 한도를 설정할 수 있나요?

[네. API 사용자는 Anthropic 콘솔의](https://console.anthropic.com/) 결제 설정에서 사용량 제한을 설정할 수 있습니다 . 고정 사용량 제한과 알림 임계값을 모두 설정할 수 있습니다. 구독 플랜 사용자의 경우, 플랜에 명시된 사용량 제한이 적용됩니다.

### MCP 서버는 제가 사용하지 않을 때도 토큰을 소모하나요?

네, 연결된 모든 MCP 서버는 각 메시지의 컨텍스트에 도구 정의를 삽입합니다. 서버의 도구를 전혀 호출하지 않더라도 정의는 여전히 존재합니다. 따라서 활발하게 사용하지 않는 서버의 연결을 끊는 것이 간단한 해결책입니다.

### 플랜 모드가 클로드 코드(Claude Code)를 직접 실행하는 것보다 느린가요?

실제 시간으로 따지면 계획 모드는 계획 검토라는 추가 단계를 하나 더 거치게 됩니다. 하지만 클로드 코드가 잘못된 방향으로 나아가 되돌아가는 것을 방지해주기 때문에 거의 항상 전체 시간을 절약해줍니다. 변수 이름 변경이나 오타 수정과 같은 간단한 변경에는 계획 모드를 건너뛰세요. 하지만 3개 이상의 파일에 영향을 미치는 작업에는 계획 모드를 사용하는 것이 좋습니다.

### CLAUDE.md 파일이 너무 긴지 어떻게 알 수 있나요?

새 세션을 시작할 때 실행하세요 `/context`. CLAUDE.md가 컨텍스트의 10~15% 이상을 차지한다면, 용량을 줄이는 것이 좋습니다. [CLAUDE.md 디자인 패턴](https://32blog.com/en/claude-code/claude-code-context-management-claude-md-patterns) 문서에서는 용량을 줄이기 위한 다섯 가지 패턴을 다룹니다. 200줄 미만을 목표로 하고, 세부 정보는 `docs/` Claude Code가 필요에 따라 읽어들이는 별도의 파일로 분리하는 것이 좋습니다.

### 서브에이전트를 사용하면 실제로 전체 토큰을 절약할 수 있나요?

작업 크기에 따라 다릅니다. 서브에이전트 시작에는 오버헤드가 발생하므로 간단한 1~2개 파일 읽기 작업은 서브에이전트에 위임하면 토큰이 낭비됩니다. 하지만 3개 이상의 파일을 대상으로 하는 조사(종속성 감사, 파일 간 검색, 테스트 실행 등)의 경우 서브에이전트를 사용하면 결과를 메인 컨텍스트 창에 표시하지 않고 요약 정보만 반환할 수 있습니다. 따라서 대규모 파일 탐색 작업을 서브에이전트에 위임하면 메인 세션에서 직접 실행하는 것보다 토큰을 훨씬 절약할 수 있습니다.

---

## 마무리

영향력 순서대로 나열하면 다음과 같습니다.

1. **추가`.claudeignore`** `node_modules` —,, 및 바이너리 제외 `.next/`. 가장 큰 단일 이득.
2. **대규모 작업에는 계획 모드를 사용하세요**. 실행 전에 계획을 검토하십시오.
3. **질문을 구체적으로 작성하고**, 보내기 전에 5W1H 질문에 스스로 답하십시오.
4. **MCP 서버를 간소화하세요**. 매 세션마다 사용하는 서버만 상시 가동 상태로 유지하십시오.
5. **`/compact`** 대화 내용이 누적되지 않도록 **세션 중간에 확인** **하세요.**
6. **CLAUDE.md 파일은 200줄 이내로 유지하고**, 자세한 내용은 별도의 파일로 옮기세요.

로 시작하세요 `.claudeignore`. 첫 번째 세션부터 차이를 느끼실 수 있을 겁니다.

관련 기사:

- [Claude 코드의 '컨텍스트 창 초과' 오류를 올바르게 수정하는 방법](https://32blog.com/en/claude-code/claude-code-context-window-exceeded-fix)
- [클로드 코드 컨텍스트 관리: CLAUDE.md 디자인 패턴](https://32blog.com/en/claude-code/claude-code-context-management-claude-md-patterns)
- [클로드 코드 명령어 치트 시트](https://32blog.com/en/claude-code/claude-code-commands-cheatsheet)
- [장시간 세션을 위한 클로드 코드 메모리 관리](https://32blog.com/en/claude-code/claude-code-memory-management-long-session-guide)
- [Claude 코드 문제 해결: 완벽 가이드](https://32blog.com/en/claude-code/claude-code-troubleshooting-complete-guide)