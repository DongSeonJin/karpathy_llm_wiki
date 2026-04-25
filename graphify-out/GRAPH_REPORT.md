# Graph Report - raw  (2026-04-25)

## Corpus Check
- Corpus is ~3,671 words - fits in a single context window. You may not need a graph.

## Summary
- 35 nodes · 46 edges · 7 communities detected
- Extraction: 87% EXTRACTED · 13% INFERRED · 0% AMBIGUOUS · INFERRED: 6 edges (avg confidence: 0.78)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Harness Engineering|Harness Engineering]]
- [[_COMMUNITY_LLM Wiki Pattern|LLM Wiki Pattern]]
- [[_COMMUNITY_Token Optimization Techniques|Token Optimization Techniques]]
- [[_COMMUNITY_3-Agent Team Structure|3-Agent Team Structure]]
- [[_COMMUNITY_Wiki Query & Navigation|Wiki Query & Navigation]]
- [[_COMMUNITY_Source Ingestion Pipeline|Source Ingestion Pipeline]]
- [[_COMMUNITY_Persistent Knowledge vs RAG|Persistent Knowledge vs RAG]]

## God Nodes (most connected - your core abstractions)
1. `LLM Wiki Pattern` - 15 edges
2. `Harness Engineering` - 7 edges
3. `Claude Code 토큰 절감 3가지 기법 (헤이제임스)` - 6 edges
4. `3-Agent Team Structure (Architect / Builder / Reviewer)` - 6 edges
5. `Ingest Operation` - 5 edges
6. `index.md (Wiki Content Catalog)` - 4 edges
7. `Wiki Layer (LLM-maintained)` - 3 edges
8. `Schema Layer (CLAUDE.md / AGENTS.md)` - 3 edges
9. `Query Operation` - 3 edges
10. `qmd Codebase Pre-indexing (92% token reduction)` - 3 edges

## Surprising Connections (you probably didn't know these)
- `Schema Layer (CLAUDE.md / AGENTS.md)` --semantically_similar_to--> `CLAUDE.md Optimization (200-line limit)`  [INFERRED] [semantically similar]
  raw/articles/2026-04-15-karpathy-llm-wiki.md → raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md
- `qmd Codebase Pre-indexing (92% token reduction)` --semantically_similar_to--> `index.md (Wiki Content Catalog)`  [INFERRED] [semantically similar]
  raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md → raw/articles/2026-04-15-karpathy-llm-wiki.md
- `Rationale: LLM Wiki vs RAG — incremental compilation` --semantically_similar_to--> `Rationale: Structural prevention over prompting`  [INFERRED] [semantically similar]
  raw/articles/2026-04-15-karpathy-llm-wiki.md → raw/notes/2026-04-07-harness-engineering.md
- `Context Corruption Problem (Long Sessions)` --semantically_similar_to--> `Session Management (/compact, /clear)`  [INFERRED] [semantically similar]
  raw/notes/2026-04-07-harness-engineering.md → raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md
- `Harness Engineering` --semantically_similar_to--> `3-Agent Team Structure (Architect / Builder / Reviewer)`  [INFERRED] [semantically similar]
  raw/notes/2026-04-07-harness-engineering.md → raw/notes/2026-04-21-claude-code-토큰-사용량-90%-절감하는-3가지-실전-기법-(qmd,-3-에이전트).md

## Hyperedges (group relationships)
- **Claude Code Token Optimization Triad: Ignore + CLAUDE.md + Session Management** — claude_token_claudeignore, claude_token_claude_md_optimization, claude_token_session_management [EXTRACTED 0.95]
- **3-Agent Workflow Cycle: Architect → Builder → Reviewer** — claude_token_architect_role, claude_token_builder_role, claude_token_reviewer_role [EXTRACTED 1.00]
- **Harness Engineering Three Components: Context File + Auto Enforcement + Garbage Collection** — harness_context_file, harness_auto_enforcement, harness_garbage_collection [EXTRACTED 1.00]

## Communities

### Community 0 - "Harness Engineering"
Cohesion: 0.29
Nodes (8): Auto Enforcement System (Linter + Tests), Context Corruption Problem (Long Sessions), Context File (agent.md / claude.md), Harness Engineering, Garbage Collection (Periodic Code Cleanup), Pre-commit Hook, Rationale: Structural prevention over prompting, Schema Layer (CLAUDE.md / AGENTS.md)

### Community 1 - "LLM Wiki Pattern"
Cohesion: 0.29
Nodes (7): Lint Operation, LLM Wiki Pattern, Vannevar Bush Memex (1945), Obsidian (Wiki IDE), Obsidian Web Clipper, RAG (Retrieval-Augmented Generation) Pattern, Rationale: LLMs eliminate wiki maintenance burden

### Community 2 - "Token Optimization Techniques"
Cohesion: 0.29
Nodes (7): CLAUDE.md Optimization (200-line limit), .claudeignore File (File Exclusion), Claude Code 토큰 절감 3가지 기법 (헤이제임스), qmd Codebase Pre-indexing (92% token reduction), Session Management (/compact, /clear), 5 Token Saving Rules for CLAUDE.md, qmd (Markdown Search Tool)

### Community 3 - "3-Agent Team Structure"
Cohesion: 0.4
Nodes (5): 3-Agent Team Structure (Architect / Builder / Reviewer), Architect Agent Role, Builder Agent Role, Reviewer Agent Role, three-man-team GitHub Repository (russelleNVy)

### Community 4 - "Wiki Query & Navigation"
Cohesion: 0.67
Nodes (3): index.md (Wiki Content Catalog), Query Operation, Wiki Layer (LLM-maintained)

### Community 5 - "Source Ingestion Pipeline"
Cohesion: 0.67
Nodes (3): Ingest Operation, log.md (Append-only Operation Log), Raw Sources Layer

### Community 6 - "Persistent Knowledge vs RAG"
Cohesion: 1.0
Nodes (2): Persistent Compounding Wiki Artifact, Rationale: LLM Wiki vs RAG — incremental compilation

## Knowledge Gaps
- **13 isolated node(s):** `Lint Operation`, `Obsidian (Wiki IDE)`, `Obsidian Web Clipper`, `RAG (Retrieval-Augmented Generation) Pattern`, `Vannevar Bush Memex (1945)` (+8 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **Thin community `Persistent Knowledge vs RAG`** (2 nodes): `Persistent Compounding Wiki Artifact`, `Rationale: LLM Wiki vs RAG — incremental compilation`
  Too small to be a meaningful cluster - may be noise or needs more connections extracted.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `LLM Wiki Pattern` connect `LLM Wiki Pattern` to `Harness Engineering`, `Token Optimization Techniques`, `Wiki Query & Navigation`, `Source Ingestion Pipeline`, `Persistent Knowledge vs RAG`?**
  _High betweenness centrality (0.528) - this node is a cross-community bridge._
- **Why does `Claude Code 토큰 절감 3가지 기법 (헤이제임스)` connect `Token Optimization Techniques` to `3-Agent Team Structure`?**
  _High betweenness centrality (0.317) - this node is a cross-community bridge._
- **Why does `Harness Engineering` connect `Harness Engineering` to `3-Agent Team Structure`?**
  _High betweenness centrality (0.296) - this node is a cross-community bridge._
- **What connects `Lint Operation`, `Obsidian (Wiki IDE)`, `Obsidian Web Clipper` to the rest of the system?**
  _13 weakly-connected nodes found - possible documentation gaps or missing edges._