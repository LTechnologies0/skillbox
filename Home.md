---
tags: [home, moc, skillbox]
aliases: [Skillbox Home, Vault Home]
---

# Skillbox Vault

Knowledge vault for **shareable Cursor Agent Skills** — structured as an Obsidian tree with local **vector embeddings** (Smart Connections).

## Start here

- [[Atlas/MOC Skills|MOC — Skills]]
- [[Atlas/MOC Concepts|MOC — Concepts]]
- [[Atlas/How this vault works|How this vault works]]
- [[80-Inbox/README|Inbox]]

## Skills (Cursor-compatible)

Canonical agent skill packages live under `skills/<name>/` (keep `SKILL.md`). Vault notes in `10-Skills/` are the human/graph layer with wikilinks.

| Skill | Vault entry | Cursor path |
|-------|-------------|-------------|
| Ultra-Light GUI | [[10-Skills/Ultra Light GUI/Index\|Ultra Light GUI]] | `skills/ultra-light-gui/` |
| Unslop Codebase | [[10-Skills/Unslop Codebase/Index\|Unslop Codebase]] | `skills/unslop-codebase/` |
| Safe Codebase | [[10-Skills/Safe Codebase/Index\|Safe Codebase]] | `skills/safe-codebase/` |
| Resource Safety | [[10-Skills/Resource Safety/Index\|Resource Safety]] | `skills/resource-safety/` (+13) |

## Quick Dataview

```dataview
TABLE file.folder AS Folder, tags
FROM "10-Skills" OR "20-Concepts"
SORT file.name ASC
```

## Graph tips

1. Open **Graph view** — skills cluster under `10-Skills/`.
2. Open **Smart Connections** on any note for semantic neighbors (local embeddings in `.smart-env/`).
3. Use MCP `obsidian` from Cursor to read/search/write this vault.
