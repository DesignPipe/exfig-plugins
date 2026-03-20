# exfig-plugins

Claude Code plugins for [ExFig](https://github.com/DesignPipe/exfig) — CLI tool that exports design resources from Figma to iOS, Android, Flutter, and Web.

## Installation

```bash
# Install all plugins from marketplace
claude /plugin marketplace add https://github.com/DesignPipe/exfig-plugins

# Or install individual plugins
claude /plugin install https://github.com/DesignPipe/exfig-plugins/plugins/mcp/exfig-mcp
claude /plugin install https://github.com/DesignPipe/exfig-plugins/plugins/skills/exfig-setup
```

## Plugins

| Plugin | Type | What it does |
|--------|------|-------------|
| **exfig-mcp** | MCP | Connects Claude to ExFig MCP server (validate, inspect, export) |
| **exfig-setup** | Skill | Interactive wizard: install → token → config → first export → CI |
| **exfig-config-review** | Skill | Reviews exfig.pkl for errors and optimization opportunities |
| **exfig-troubleshooting** | Skill | Error catalog with diagnostic steps |
| **exfig-migration** | Skill | Migration guide between major ExFig versions |
| **exfig-export** | Commands | `/export-colors`, `/export-icons`, `/export-images`, `/export-all` |
| **exfig-rules** | Rules | Naming and structure conventions for ExFig projects |

## Prerequisites

- [ExFig CLI](https://github.com/DesignPipe/exfig) installed (`exfig --version`)
- `FIGMA_PERSONAL_TOKEN` environment variable (for Figma API access)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI

## Quick Start

1. Install the setup plugin and say: *"Set up ExFig for my iOS project"*
2. The wizard will check your environment, help configure PKL, and run your first export
3. Install exfig-export for convenient slash commands

## License

MIT
