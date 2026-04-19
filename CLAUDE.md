# CLAUDE.md — Second Brain Agent Rules

This file governs how Claude maintains this repository. The entire repo is a personal, LLM-maintained knowledge wiki modeled on Andrej Karpathy's LLM Wiki pattern. Follow these rules exactly.

## 1. Purpose

This is a personal knowledge wiki focused on **software development** — architecture, patterns, tools, languages, and frameworks. The user drops raw source documents into `raw/`. Claude reads them and maintains a structured, interlinked, browsable wiki under `wiki/`. The wiki is self-maintaining: every ingest updates the index and the log.

## 2. Folder Structure

```
/
├── CLAUDE.md              # This file (agent rules)
├── README.md              # Human-facing usage guide
├── raw/                   # User-owned source documents. AGENT NEVER MODIFIES.
│   └── assets/            # Images, PDFs, binary attachments
├── wiki/                  # Agent-maintained structured knowledge
│   ├── index.md           # Master catalog (tables per category)
│   ├── log.md             # Append-only operation log (reverse chronological)
│   ├── sources/           # One page per raw source — summaries + key takeaways
│   ├── entities/          # People, orgs, tools, languages, frameworks
│   ├── concepts/          # Ideas, patterns, techniques
│   └── synthesis/         # Cross-cutting analysis, comparisons
└── output/                # Generated artifacts (reports, exports)
```

**Ownership:**
- `raw/` — user-owned; agent reads only, never writes.
- `wiki/` — agent-owned; user may browse/edit but agent is the primary maintainer.
- `output/` — agent-generated artifacts on request.

## 3. Page Types and Frontmatter Schemas

Every page in `wiki/` (except `index.md` and `log.md`) MUST begin with YAML frontmatter matching its type.

### 3.1 Source Summary — `wiki/sources/<slug>.md`

```yaml
---
type: source
title: "Human-Readable Title"
source_file: "raw/<filename.ext>"
date_ingested: "YYYY-MM-DD"
tags: [tag1, tag2]
---
```

Body sections: `## Summary`, `## Key Takeaways`, `## Entities Referenced`, `## Concepts Referenced`.

### 3.2 Entity Page — `wiki/entities/<slug>.md`

```yaml
---
type: entity
title: "Entity Name"
entity_kind: person | org | tool | language | framework
date_created: "YYYY-MM-DD"
date_updated: "YYYY-MM-DD"
tags: [tag1, tag2]
aliases: [alt-name-1, alt-name-2]
---
```

Body sections: `## Overview`, `## Details`, `## Related`, `## Sources`.

### 3.3 Concept Page — `wiki/concepts/<slug>.md`

```yaml
---
type: concept
title: "Concept Name"
date_created: "YYYY-MM-DD"
date_updated: "YYYY-MM-DD"
tags: [tag1, tag2]
aliases: [alt-name-1]
---
```

Body sections: `## Overview`, `## Details`, `## Related`, `## Sources`.

### 3.4 Synthesis Page — `wiki/synthesis/<slug>.md`

```yaml
---
type: synthesis
title: "Synthesis Title"
date_created: "YYYY-MM-DD"
date_updated: "YYYY-MM-DD"
inputs: ["wiki/sources/foo.md", "wiki/concepts/bar.md"]
tags: [tag1, tag2]
---
```

Body sections: `## Question`, `## Analysis`, `## Conclusion`, `## Open Questions`.

## 4. File Naming Conventions

- Lowercase, hyphenated slugs: `event-driven-architecture.md`, not `Event Driven Architecture.md`.
- ASCII only; strip accents and special characters.
- Max 60 characters in the filename (excluding extension).
- One page per concept/entity/source. Do not duplicate.
- If a name collision occurs, disambiguate with a suffix: `kafka-apache.md` vs `kafka-franz.md`.

## 5. Cross-Linking Rules

- Use wikilink syntax: `[[category/page-name]]` (e.g., `[[concepts/event-driven-architecture]]`).
- Every new page MUST link to at least one existing page (index doesn't count).
- Bidirectional linking: if page A references page B, page B's `## Related` section must back-link to A.
- When creating a page, scan the index for related existing pages before writing — prefer linking to extending.

## 6. `wiki/index.md` Format

Four tables, one per category. Columns: `file | title | date | tags`. Each row links to the page.

```markdown
# Wiki Index

## Sources
| File | Title | Date Ingested | Tags |
|------|-------|---------------|------|

## Entities
| File | Title | Last Updated | Tags |
|------|-------|--------------|------|

## Concepts
| File | Title | Last Updated | Tags |
|------|-------|--------------|------|

## Synthesis
| File | Title | Last Updated | Tags |
|------|-------|--------------|------|
```

Keep tables sorted alphabetically by file. Update index on every page create, rename, or frontmatter edit.

## 7. `wiki/log.md` Format

Append-only, reverse-chronological (newest at top). One `##` entry per operation.

```markdown
## YYYY-MM-DD HH:MM — OPERATION

- **input:** <what was processed — a path, a question, or "all">
- **pages-created:** <list of new pages, or "none">
- **pages-updated:** <list of modified pages, or "none">
- **summary:** <one-sentence description of what happened>
```

`OPERATION` is one of `INGEST`, `QUERY`, `LINT`, `INIT`.

## 8. Core Operations

### 8.1 INGEST (user drops a source)

1. Read source file from `raw/`.
2. Create a source summary page in `wiki/sources/`.
3. Extract entities; for each: create a new entity page OR update the existing one (additive merge).
4. Extract concepts; for each: create a new concept page OR update the existing one (additive merge).
5. Add cross-links between source, entities, and concepts.
6. Update `wiki/index.md` with all created/modified pages.
7. Append an `INGEST` entry to `wiki/log.md`.

### 8.2 QUERY (user asks a question)

1. Search `wiki/index.md` for relevant pages.
2. Read those pages to build context.
3. Synthesize an answer, citing pages with wikilinks.
4. Note any gaps where the wiki lacks information.
5. Append a `QUERY` entry to `wiki/log.md`.

### 8.3 LINT (user requests integrity check)

1. Validate `wiki/index.md` matches files on disk (no missing, no extras).
2. Find orphan pages (no inbound links except from index).
3. Validate all wikilinks resolve to real files.
4. Check every page's frontmatter against its type schema.
5. Report findings to the user; fix on explicit approval.
6. Append a `LINT` entry to `wiki/log.md`.

## 9. Hard Rules

- **Never modify `raw/`.** It is user-owned input.
- **Always update `wiki/index.md`** when pages are added, renamed, or have frontmatter changes.
- **Always append to `wiki/log.md`** for every INGEST, QUERY, LINT, or INIT operation.
- **Never delete pages** without explicit user permission.
- **Preserve existing content on updates** — merge additively, don't overwrite. If content conflicts, note the conflict inline and ask the user.
- **Validate frontmatter on write** — exact schema match per page type.
- **No broken wikilinks.** If you write a link, the target must exist or be created in the same operation.
