# Second Brain

A personal, LLM-maintained knowledge wiki focused on **software development** — architecture, patterns, tools, languages, and frameworks. Modeled on Andrej Karpathy's LLM Wiki pattern.

You drop raw source documents into `raw/`. Claude reads them and builds a structured, interlinked wiki of markdown pages under `wiki/`. The wiki is browsable in any markdown viewer (VS Code, Obsidian, GitHub) and queryable by Claude.

## Quick Start

1. Open this repo in Claude Code (or any LLM agent that reads `CLAUDE.md`).
2. Drop a source document — article, transcript, PDF, note — into `raw/`.
3. Ask Claude: `Ingest raw/<filename>`.
4. Claude creates a source summary, extracts entities and concepts, cross-links them, and updates `wiki/index.md` and `wiki/log.md`.
5. Later, ask: `What do we know about <topic>?` Claude searches the wiki and synthesizes an answer.

## Folder Structure

```
/
├── CLAUDE.md              # Agent rules — read this first
├── README.md              # You are here
├── raw/                   # Drop source documents here (agent never modifies)
│   └── assets/            # Images, PDFs, binary attachments
├── wiki/                  # Agent-maintained wiki
│   ├── index.md           # Master catalog
│   ├── log.md             # Operation log
│   ├── sources/           # One page per ingested source
│   ├── entities/          # People, orgs, tools, languages, frameworks
│   ├── concepts/          # Ideas, patterns, techniques
│   └── synthesis/         # Cross-cutting analysis
└── output/                # Generated artifacts (reports, exports)
```

## Usage Examples

**Ingest a source**
> Ingest `raw/kleppmann-designing-data-intensive-applications-ch1.md`.

**Ask a question**
> What do we know about event-driven architecture? Cite sources.

**Cross-reference**
> Compare Kafka and RabbitMQ based on what we've ingested.

**Housekeeping**
> Lint the wiki — find orphan pages, broken links, and index mismatches.

## Tips

- Keep source filenames descriptive: `author-title-chN.md` or `conf-year-talk-title.md`.
- Use `raw/assets/` for images and PDFs; reference them from the source summary.
- Let Claude do the linking — don't hand-maintain wikilinks.
- Review `wiki/log.md` to see what Claude did in each session.
- Start small: ingest 3–5 sources on one topic, then query.

## Conventions

See `CLAUDE.md` for the exact rules Claude follows: page types, frontmatter schemas, cross-linking, naming conventions, and hard constraints.
