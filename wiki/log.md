# Wiki Log

운영 일지. append-only. `grep "^## \[" wiki/log.md` 로 파싱 가능.

---

## [2026-04-21] ingest | Claude Code 토큰 50% 절감 (omitsu, 32blog.com)
- source: raw/articles/2026-04-21-how-i-reduced-claude-code-token-consumption-by-50%.md
- 생성: summaries/2026-04-21-claude-code-token-50-percent.md
- 생성: concepts/Plan Mode.md, concepts/서브에이전트 위임.md
- 업데이트: concepts/Claude Code 토큰 최적화.md (두 소스 통합, 기법 전체 목록 추가)
- 업데이트: index.md, synthesis.md

## [2026-04-21] ingest | Claude Code 토큰 90% 절감 3기법 (헤이제임스)
- source: raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md
- 생성: summaries/2026-04-21-claude-code-token-optimization.md
- 생성: concepts/Claude Code 토큰 최적화.md, concepts/3-에이전트 팀 구조.md
- 생성: entities/qmd.md
- 업데이트: entities/Claude Code.md (소스 추가, 토큰 최적화 섹션 추가)
- 업데이트: index.md (summary, concept, entity 추가)
- 업데이트: synthesis.md (세 번째 소스 통합 인사이트 추가)
- 비고: raw/notes/2026-04-07-harness-engineering.md는 기존 하네스 엔지니어링 파일 리네임 — 동일 내용, 재ingest 생략

## [2026-04-20] ingest | Karpathy LLM Wiki (re-ingest)
- source: raw/articles/2026-04-15-karpathy-llm-wiki.md
- 생성: summaries/2026-04-15-karpathy-llm-wiki.md
- 생성: concepts/LLM Wiki 패턴.md, concepts/RAG vs LLM Wiki.md, concepts/Compounding Knowledge.md
- 생성: entities/Andrej Karpathy.md, entities/Obsidian.md, entities/Vannevar Bush Memex.md

## [2026-04-20] ingest | 하네스 엔지니어링 (re-ingest)
- source: raw/notes/하네스 엔지니어링.md
- 생성: summaries/2026-04-06-harness-engineering.md
- 생성: concepts/하네스 엔지니어링.md, concepts/Context Corruption.md, concepts/Context File.md, concepts/Auto Enforcement System.md, concepts/Garbage Collection.md
- 생성: entities/Claude Code.md
- 생성: comparisons/CLAUDE.md — 하네스 vs LLM Wiki 스키마.md
- 업데이트: synthesis.md (두 소스 통합 인사이트)
