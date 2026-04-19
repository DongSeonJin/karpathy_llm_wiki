# Graph Report - ./raw  (2026-04-18)

## Corpus Check
- Corpus is ~2,350 words - fits in a single context window. You may not need a graph.

## Summary
- 29 nodes · 35 edges · 5 communities detected
- Extraction: 86% EXTRACTED · 14% INFERRED · 0% AMBIGUOUS · INFERRED: 5 edges (avg confidence: 0.84)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_LLM Wiki & Obsidian Layer|LLM Wiki & Obsidian Layer]]
- [[_COMMUNITY_AI Agent & Harness Engineering|AI Agent & Harness Engineering]]
- [[_COMMUNITY_Source Ingestion Pipeline|Source Ingestion Pipeline]]
- [[_COMMUNITY_Persistent Knowledge vs RAG|Persistent Knowledge vs RAG]]
- [[_COMMUNITY_Automated Quality Gates|Automated Quality Gates]]

## God Nodes (most connected - your core abstractions)
1. `LLM Wiki` - 12 edges
2. `하네스 엔지니어링 (Harness Engineering)` - 8 edges
3. `Ingest Operation` - 4 edges
4. `Raw Sources Directory` - 3 edges
5. `Persistent Wiki` - 3 edges
6. `Raw Sources Layer` - 3 edges
7. `Wiki Layer` - 3 edges
8. `Query Operation` - 3 edges
9. `자동 강제 시스템 (Auto Enforcement System)` - 3 edges
10. `Graphify Workflow` - 2 edges

## Surprising Connections (you probably didn't know these)
- `Raw Sources Directory` --semantically_similar_to--> `Raw Sources Layer`  [INFERRED] [semantically similar]
  raw/README.md → raw/articles/2026-04-15-karpathy-llm-wiki.md
- `Graphify Workflow` --semantically_similar_to--> `Ingest Operation`  [INFERRED] [semantically similar]
  raw/README.md → raw/articles/2026-04-15-karpathy-llm-wiki.md

## Hyperedges (group relationships)
- **Three-Layer LLM Wiki Architecture** — llmwiki_raw_sources, llmwiki_wiki_layer, llmwiki_schema_layer [EXTRACTED 0.95]
- **Core Wiki Operations** — llmwiki_ingest_operation, llmwiki_query_operation, llmwiki_lint_operation [EXTRACTED 0.95]
- **하네스의 3대 구성 요소** — 하네스엔지니어링_context_file, 하네스엔지니어링_auto_enforcement_system, 하네스엔지니어링_garbage_collection [EXTRACTED 1.00]
- **하네스 실무 적용 3단계** — 하네스엔지니어링_claude_md, 하네스엔지니어링_precommit_hook, 하네스엔지니어링_linter_test [EXTRACTED 1.00]

## Communities

### Community 0 - "LLM Wiki & Obsidian Layer"
Cohesion: 0.28
Nodes (9): index.md (wiki index), Lint Operation, LLM Wiki, log.md (chronological log), Vannevar Bush Memex, Obsidian (PKM tool), Query Operation, Schema Layer (CLAUDE.md / AGENTS.md) (+1 more)

### Community 1 - "AI Agent & Harness Engineering"
Cohesion: 0.28
Nodes (9): AI 에이전트 (AI Agent), claude.md / agent.md (온보딩 지침 파일), 컨텍스트 부패 (Context Corruption), 컨텍스트 파일 (Context File), 개발자 역할 변화 (Developer Role Change), 가비지 컬렉션 (Garbage Collection), 하네스 엔지니어링 (Harness Engineering), 프롬프트 엔지니어링 (Prompt Engineering) (+1 more)

### Community 2 - "Source Ingestion Pipeline"
Cohesion: 0.5
Nodes (5): Ingest Operation, Raw Sources Layer, Graphify Workflow, Naming Convention, Raw Sources Directory

### Community 3 - "Persistent Knowledge vs RAG"
Cohesion: 0.67
Nodes (3): Compounding Knowledge, Persistent Wiki, RAG (Retrieval-Augmented Generation)

### Community 4 - "Automated Quality Gates"
Cohesion: 0.67
Nodes (3): 자동 강제 시스템 (Auto Enforcement System), 린터 / 테스트 자동화 (Linter & Test Automation), 프리커밋 훅 (Pre-commit Hook)

## Knowledge Gaps
- **12 isolated node(s):** `Naming Convention`, `Schema Layer (CLAUDE.md / AGENTS.md)`, `Lint Operation`, `log.md (chronological log)`, `Obsidian (PKM tool)` (+7 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `LLM Wiki` connect `LLM Wiki & Obsidian Layer` to `Source Ingestion Pipeline`, `Persistent Knowledge vs RAG`?**
  _High betweenness centrality (0.264) - this node is a cross-community bridge._
- **Why does `하네스 엔지니어링 (Harness Engineering)` connect `AI Agent & Harness Engineering` to `Automated Quality Gates`?**
  _High betweenness centrality (0.128) - this node is a cross-community bridge._
- **Why does `Raw Sources Layer` connect `Source Ingestion Pipeline` to `LLM Wiki & Obsidian Layer`?**
  _High betweenness centrality (0.064) - this node is a cross-community bridge._
- **What connects `Naming Convention`, `Schema Layer (CLAUDE.md / AGENTS.md)`, `Lint Operation` to the rest of the system?**
  _12 weakly-connected nodes found - possible documentation gaps or missing edges._