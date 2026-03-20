---
description: Export all design resources from Figma using ExFig batch mode
allowed-tools: ["Bash", "Read"]
user-invocable: true
---

# Export All

Run a batch export of all configured resources (colors, icons, images, typography) from Figma.

## Steps

1. Check that `exfig.pkl` exists:
```bash
ls exfig.pkl
```

2. If ExFig MCP is available, validate config first:
- Use `exfig_validate` tool

3. Run batch export:
```bash
exfig batch exfig.pkl
```

With cache (skip unchanged resources):
```bash
exfig batch exfig.pkl --cache
```

Force re-export everything (ignore cache):
```bash
exfig batch exfig.pkl --force
```

4. Show generated files:
```bash
git status --short
```

5. If some resources fail but others succeed, batch mode continues and reports errors at the end. Check the output for specific failures.

6. On error, suggest `exfig-troubleshooting` skill.
