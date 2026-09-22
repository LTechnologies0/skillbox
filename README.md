# Skillbox

Vault Obsidian + dépôt de **Cursor Agent Skills** partageables.

- Ouvrir le dossier dans Obsidian : `flatpak run md.obsidian.Obsidian /home/user/fakehome/skillbox`
- Entrée vault : [[Home]]
- Skills agent : `skills/<nom>/SKILL.md`
- Embeddings locaux : plugin **Smart Connections** → `.smart-env/`
- MCP Cursor : serveur `obsidian` (`obsidian-mcp@2`)

## Arborescence

```
Home.md
Atlas/           # MOCs
10-Skills/       # portails humains + wikilinks
20-Concepts/
80-Inbox/
90-Meta/
skills/          # packages Cursor (source of truth agents)
.obsidian/       # config + Dataview + Smart Connections
```

## Skills

| Skill | Description |
|-------|-------------|
| [ultra-light-gui](skills/ultra-light-gui/) | 100 principes GUI + garde-fous + profiling |
| [unslop-codebase](skills/unslop-codebase/) | Unslop graph/tree + inventaire types + docs riches |
| [safe-codebase](skills/safe-codebase/) | 40 classes de safety (memory → payments) |
| [resource-safety](skills/resource-safety/) | Resource safety umbrella (CWE-400/770) |
| `resource-*` (×13) | Domaines: memory, cpu, fd, concurrency, connections, disk, queues, timeouts, rate-limit, amplification, request-bounds, lifecycle, downstream |

## Installer un skill Cursor

```bash
ln -s "$(pwd)/skills/nom-du-skill" ~/.cursor/skills/nom-du-skill
```

## MCP

Configuré dans `~/.cursor/mcp.json` :

```json
"obsidian": {
  "command": "npx",
  "args": ["-y", "obsidian-mcp@2", "serve", "--vault", "skillbox=/home/user/fakehome/skillbox"]
}
```

Recharge les MCP dans Cursor après modification.
