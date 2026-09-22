---
tags: [atlas, meta, vault]
---

# How this vault works

Skillbox is both:

1. **A Cursor skills repo** — installable packages under `skills/<name>/SKILL.md`
2. **An Obsidian knowledge vault** — tree under `10-Skills/`, `20-Concepts/`, `Atlas/`, with graph + embeddings

## Tree

```
Home.md
Atlas/                 # maps of content (MOCs)
10-Skills/             # human-oriented skill portals + wikilinks
20-Concepts/           # shared concepts
80-Inbox/              # capture
90-Meta/               # templates, attachments
skills/                # Cursor Agent Skill packages (source of truth for agents)
.obsidian/             # Obsidian config + community plugins
.smart-env/            # Smart Connections local embeddings (generated)
```

## Vectorization

Plugin **Smart Connections** indexes markdown locally into `.smart-env/` (Transformers.js embeddings). No API key required for core retrieval.

1. Open this folder as a vault in Obsidian
2. Ensure Community plugins → Smart Connections + Dataview are enabled
3. Open **Connections** / **Lookup** on a note — semantic neighbors appear after indexing

## MCP (Cursor)

Server `obsidian` in `~/.cursor/mcp.json` points at this vault via `obsidian-mcp` (filesystem, works even if Obsidian is closed).

## Sync note

Exclude `.smart-env/` from Syncthing/rsync mirrors — rebuild embeddings per machine.
