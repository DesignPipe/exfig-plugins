# exfig-mcp

MCP integration plugin for ExFig. Connects Claude Code to the ExFig MCP server, enabling config validation, Figma resource inspection, and design token export.

## Prerequisites

- `exfig` binary in PATH
- `FIGMA_PERSONAL_TOKEN` environment variable (for Figma API tools)

## Tools Provided

| Tool | Token Required | Purpose |
|------|---------------|---------|
| `exfig_validate` | No | Validate PKL configuration |
| `exfig_tokens_info` | No | Inspect .tokens.json files |
| `exfig_inspect` | Yes | List Figma resources |
| `exfig_export` | Yes | Export to platform code |
| `exfig_download` | Yes | Export as W3C design tokens |

## Installation

```bash
claude /plugin install https://github.com/DesignPipe/exfig-plugins/plugins/mcp/exfig-mcp
```
