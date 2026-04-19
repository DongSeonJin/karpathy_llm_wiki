---
type: community
cohesion: 0.50
members: 5
---

# Source Ingestion Pipeline

**Cohesion:** 0.50 - moderately connected
**Members:** 5 nodes

## Members
- [[Graphify Workflow]] - document - raw/README.md
- [[Ingest Operation]] - document - raw/articles/2026-04-15-karpathy-llm-wiki.md
- [[Naming Convention]] - document - raw/README.md
- [[Raw Sources Directory]] - document - raw/README.md
- [[Raw Sources Layer]] - document - raw/articles/2026-04-15-karpathy-llm-wiki.md

## Live Query (requires Dataview plugin)

```dataview
TABLE source_file, type FROM #community/Source_Ingestion_Pipeline
SORT file.name ASC
```

## Connections to other communities
- 3 edges to [[_COMMUNITY_LLM Wiki & Obsidian Layer]]

## Top bridge nodes
- [[Ingest Operation]] - degree 4, connects to 1 community
- [[Raw Sources Layer]] - degree 3, connects to 1 community