# Graph Report - ./raw  (2026-04-15)

## Corpus Check
- Corpus is ~2,065 words - fits in a single context window. You may not need a graph.

## Summary
- 17 nodes · 22 edges · 4 communities detected
- Extraction: 86% EXTRACTED · 14% INFERRED · 0% AMBIGUOUS · INFERRED: 3 edges (avg confidence: 0.85)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Wiki Operations & Tools|Wiki Operations & Tools]]
- [[_COMMUNITY_Source Ingestion Pipeline|Source Ingestion Pipeline]]
- [[_COMMUNITY_Persistent Knowledge vs RAG|Persistent Knowledge vs RAG]]
- [[_COMMUNITY_Query & Wiki Layer|Query & Wiki Layer]]

## God Nodes (most connected - your core abstractions)
1. `LLM Wiki` - 12 edges
2. `Ingest Operation` - 4 edges
3. `Raw Sources Directory` - 3 edges
4. `Persistent Wiki` - 3 edges
5. `Raw Sources Layer` - 3 edges
6. `Wiki Layer` - 3 edges
7. `Query Operation` - 3 edges
8. `Graphify Workflow` - 2 edges
9. `RAG (Retrieval-Augmented Generation)` - 2 edges
10. `index.md (wiki index)` - 2 edges

## Surprising Connections (you probably didn't know these)
- `Raw Sources Directory` --semantically_similar_to--> `Raw Sources Layer`  [INFERRED] [semantically similar]
  raw/README.md → raw/articles/2026-04-15-karpathy-llm-wiki.md
- `Graphify Workflow` --semantically_similar_to--> `Ingest Operation`  [INFERRED] [semantically similar]
  raw/README.md → raw/articles/2026-04-15-karpathy-llm-wiki.md

## Hyperedges (group relationships)
- **Three-Layer LLM Wiki Architecture** — llmwiki_raw_sources, llmwiki_wiki_layer, llmwiki_schema_layer [EXTRACTED 0.95]
- **Core Wiki Operations** — llmwiki_ingest_operation, llmwiki_query_operation, llmwiki_lint_operation [EXTRACTED 0.95]

## Communities

### Community 0 - "Wiki Operations & Tools"
Cohesion: 0.33
Nodes (6): Lint Operation, LLM Wiki, log.md (chronological log), Vannevar Bush Memex, Obsidian (PKM tool), Schema Layer (CLAUDE.md / AGENTS.md)

### Community 1 - "Source Ingestion Pipeline"
Cohesion: 0.5
Nodes (5): Ingest Operation, Raw Sources Layer, Graphify Workflow, Naming Convention, Raw Sources Directory

### Community 2 - "Persistent Knowledge vs RAG"
Cohesion: 0.67
Nodes (3): Compounding Knowledge, Persistent Wiki, RAG (Retrieval-Augmented Generation)

### Community 3 - "Query & Wiki Layer"
Cohesion: 0.67
Nodes (3): index.md (wiki index), Query Operation, Wiki Layer

## Knowledge Gaps
- **7 isolated node(s):** `Naming Convention`, `Schema Layer (CLAUDE.md / AGENTS.md)`, `Lint Operation`, `log.md (chronological log)`, `Obsidian (PKM tool)` (+2 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `LLM Wiki` connect `Wiki Operations & Tools` to `Source Ingestion Pipeline`, `Persistent Knowledge vs RAG`, `Query & Wiki Layer`?**
  _High betweenness centrality (0.831) - this node is a cross-community bridge._
- **Why does `Raw Sources Layer` connect `Source Ingestion Pipeline` to `Wiki Operations & Tools`?**
  _High betweenness centrality (0.203) - this node is a cross-community bridge._
- **What connects `Naming Convention`, `Schema Layer (CLAUDE.md / AGENTS.md)`, `Lint Operation` to the rest of the system?**
  _7 weakly-connected nodes found - possible documentation gaps or missing edges._