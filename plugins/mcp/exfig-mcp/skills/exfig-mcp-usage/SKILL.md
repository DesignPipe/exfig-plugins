---
name: exfig-mcp-usage
description: Guide for using ExFig MCP tools — validate, inspect, export, download design tokens. Use when the user asks about ExFig MCP capabilities, wants to validate config, inspect Figma resources, or export design tokens via MCP.
---

# ExFig MCP Usage Guide

The ExFig MCP server provides 5 tools for working with Figma design resources directly from Claude Code.

## Available Tools

### exfig_validate (no Figma token needed)
Validate a PKL configuration file. Returns platform summary, entry counts, file IDs, and dark mode approaches configured.

```
Use: Check if exfig.pkl is valid before running exports
Input: config_path (optional, auto-detects exfig.pkl)
Output includes: dark_mode array (e.g., ["darkFileId", "variablesDarkMode"])
```

### exfig_tokens_info (no Figma token needed)
Inspect a W3C DTCG `.tokens.json` file. Returns token counts by type, groups, aliases.

```
Use: Understand structure of a design tokens file
Input: file_path (required)
```

### exfig_inspect (requires FIGMA_PERSONAL_TOKEN)
List Figma resources without exporting. Returns names, counts, metadata.

```
Use: Discover what's in a Figma file before configuring exports
Input: resource_type (colors|icons|images|typography|all), config_path (optional)
```

### exfig_export (requires FIGMA_PERSONAL_TOKEN)
Run platform code export via subprocess. Writes generated Swift/Kotlin/Dart/CSS files to disk and returns a structured JSON report.

```
Use: Export design resources to platform-specific code
Input:
  resource_type (required): colors|icons|images|typography|all
  config_path: auto-detects exfig.pkl if omitted
  filter: name pattern (e.g., "background/*")
  rate_limit: Figma API requests per minute (default: 10)
  max_retries: max retry attempts (default: 4)
  cache: enable version tracking (default: false)
  force: ignore cache (default: false)
  granular_cache: per-node cache (default: false)
```

### exfig_download (requires FIGMA_PERSONAL_TOKEN)
Export design data as W3C Design Tokens JSON. Returns JSON directly in the response — no file writes.

```
Use: Get raw design token data for analysis or custom processing
Input:
  resource_type (required): colors|typography|tokens
  config_path: auto-detects exfig.pkl if omitted
  format: w3c (default) or raw (RGBA values)
  filter: name pattern, only for colors
```

- `tokens` mode returns unified W3C DTCG JSON: colors + typography + dimensions + numbers merged
- `raw` format returns RGBA values instead of W3C token objects

## Common Workflows

### 1. Validate config before export
```
1. exfig_validate → check config is valid
2. exfig_inspect with resource_type=all → preview what will be exported
3. exfig_export with resource_type=all → run the export
```

### 2. Explore Figma file contents
```
1. exfig_inspect with resource_type=colors → see color styles
2. exfig_inspect with resource_type=icons → see icon components
3. exfig_inspect with resource_type=typography → see text styles
```

### 3. Download design tokens
```
1. exfig_download with resource_type=tokens → unified W3C tokens (colors + typography + dimensions)
2. exfig_download with resource_type=colors, format=raw → raw RGBA values
```

### 4. Export icons with dark mode via Variable Modes
```
1. exfig_validate → check dark_mode includes "variablesDarkMode"
2. exfig_export with resource_type=icons → exports light + dark SVGs
```
When `variablesDarkMode` is configured on icon entries, `exfig_export` automatically generates
dark SVG variants by resolving Figma Variable bindings. No separate dark file needed.

Three mutually exclusive dark mode approaches:
- `darkFileId` — separate Figma file for dark icons (global `figma` section)
- `suffixDarkMode` — name suffix splitting (global on `common.icons`)
- `variablesDarkMode` — Figma Variable Modes (per-entry on icon entries, recommended)

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| "FIGMA_PERSONAL_TOKEN not set" | Token missing from environment | Set `export FIGMA_PERSONAL_TOKEN=xxx` in shell |
| "No exfig.pkl found" | No config in current dir | Run `exfig init` or specify `config_path` |
| "Config file not found" | Wrong path | Check the `config_path` parameter |
| Rate limit (429) | Too many Figma API calls | Wait and retry, or reduce `rate_limit` param |
| Dark icons empty | Missing `variablesFileId` for library vars | Set `variablesFileId` in `variablesDarkMode` config |
| Dark icons empty (single-file) | Wrong collection/mode names (case-sensitive) | Check names in Figma Variables panel |
