---
description: Export images from Figma using ExFig
allowed-tools: ["Bash", "Read"]
user-invocable: true
---

# Export Images

Export raster images from Figma in platform-specific formats.

## Steps

1. Check that `exfig.pkl` exists:
```bash
ls exfig.pkl
```

2. If ExFig MCP is available, validate config first:
- Use `exfig_validate` tool

3. Run the export:
```bash
exfig images -i exfig.pkl
```

With cache:
```bash
exfig images -i exfig.pkl --cache
```

4. Show generated files:
```bash
git status --short
```

5. Image exports can be slow due to Figma render API. If it times out:
```bash
# Reduce rate limit for more reliable downloads
exfig images -i exfig.pkl --rate-limit 5
```

6. On error, suggest `exfig-troubleshooting` skill.
