---
description: Export icons from Figma using ExFig
allowed-tools: ["Bash", "Read"]
user-invocable: true
---

# Export Icons

Export icon components from Figma as platform-specific vector assets.

## Steps

1. Check that `exfig.pkl` exists:
```bash
ls exfig.pkl
```

2. If ExFig MCP is available, validate config first:
- Use `exfig_validate` tool

3. Run the export:
```bash
exfig icons -i exfig.pkl
```

With cache:
```bash
exfig icons -i exfig.pkl --cache
```

4. Show generated files:
```bash
git status --short
```

5. If export produces no files, the frame names in config may not match Figma. Suggest:
```bash
# Check actual frame names
exfig inspect -i exfig.pkl --resource-type icons
```

6. On error, suggest `exfig-troubleshooting` skill.
