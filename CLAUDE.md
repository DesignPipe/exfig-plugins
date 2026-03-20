# CLAUDE.md — exfig-plugins

Plugin marketplace for ExFig CLI tool. Each plugin is a self-contained Claude Code plugin
in `plugins/` following the indrive-claude-plugins structure.

## Structure

| Directory | Type | Contents |
|-----------|------|----------|
| `plugins/mcp/` | MCP servers | `.mcp.json` + optional usage skills |
| `plugins/skills/` | Skills | `skills/<name>/SKILL.md` + support files |
| `plugins/commands/` | Slash commands | `commands/<name>.md` |
| `plugins/rules/` | Rules | `rules/<name>.md` (auto-loaded) |

## Plugin Conventions

- Each plugin has `.claude-plugin/plugin.json` with name, description, version, author
- Skills use progressive disclosure: `SKILL.md` < 500 lines, heavy references in separate `.md` files
- MCP plugins have `.mcp.json` at plugin root
- Commands use YAML frontmatter with `allowed-tools`, `description`, `user-invocable: true`

## ExFig Context

ExFig exports colors, typography, icons, and images from Figma to iOS/Android/Flutter/Web.
Config format: PKL (`exfig.pkl`). Binary: `exfig` (expected in PATH, fallback `.build/debug/exfig`).
MCP server: `exfig mcp` (stdio transport, JSON-RPC).

### MCP Tools Available

| Tool | Requires Token | Purpose |
|------|---------------|---------|
| `exfig_validate` | No | Validate PKL config |
| `exfig_tokens_info` | No | Inspect .tokens.json |
| `exfig_inspect` | Yes | List Figma resources without export |
| `exfig_export` | Yes | Run platform code export |
| `exfig_download` | Yes | Export design data as W3C tokens JSON |

### Supported Platforms

iOS (xcassets, Swift), Android (XML, Compose, VectorDrawable), Flutter (Dart, SVG/PNG), Web (CSS, JSX)
