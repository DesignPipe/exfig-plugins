---
name: exfig-migration
description: Migrate ExFig between major versions — handles breaking changes, config format updates, deprecated features. Use when the user asks about "upgrade exfig", "migrate exfig", "breaking changes", "update exfig config", "exfig v2 to v3", or encounters errors after upgrading ExFig.
---

# ExFig Migration Guide

Help users migrate between major ExFig versions safely.

## Step 1: Identify Versions

```bash
# Current installed version
exfig --version

# Config package URI version
grep -o 'v[0-9]*\.[0-9]*\.[0-9]*' exfig.pkl | head -1
```

Compare installed version with config URI version. If they differ, migration may be needed.

## Step 2: Determine Migration Path

### v1.x → v2.x (YAML → PKL)

**Breaking changes:**
1. Config format changed from YAML (`exfig.yml`) to PKL (`exfig.pkl`)
2. CLI flags renamed (e.g., `--config` → `--input`/`-i`)
3. Platform entries changed from single objects to arrays (`Listing`)
4. Color export uses Variables API instead of Styles API

**Migration steps:**

1. **Generate new PKL config:**
```bash
exfig init --platform {your_platform}
```

2. **Transfer settings from YAML:**
Map YAML fields to PKL equivalents:

| YAML (v1) | PKL (v2) |
|-----------|----------|
| `figma.file_id` | `figma.lightFileId` |
| `figma.dark_file_id` | `figma.darkFileId` |
| `ios.colors.swift_type` | `ios.colors[0].codeGeneration` |
| `ios.icons.frame_name` | `ios.icons[0].figmaFrameName` |
| `output_dir` | `ios.icons[0].assetsFolder` |

3. **Update CI scripts:**
```bash
# Old
exfig export --config exfig.yml --type colors

# New
exfig colors -i exfig.pkl
# Or batch all:
exfig batch exfig.pkl
```

4. **Remove old config:**
```bash
# After verifying new config works
rm exfig.yml
```

### v2.x Minor Updates

For minor version updates within v2.x:

1. **Update package URI in config:**
```bash
# Find current version in config
grep 'package://' exfig.pkl

# Update to new version
# Replace v2.0.0 with v2.1.0 in the URI
```

2. **Check changelog** for new features or deprecations:
```bash
# View release notes
gh release view v2.1.0 --repo DesignPipe/exfig
```

3. **Regenerate config** if new fields were added:
```bash
# Validate with new version
exfig validate exfig.pkl
```

### Future: v2.x → v3.x (template)

When v3.x is released, this section will be populated with specific migration steps. The general approach:

1. Read release notes for breaking changes
2. Back up current config: `cp exfig.pkl exfig.pkl.backup`
3. Update ExFig binary
4. Run `exfig migrate` (if available) or manually update config
5. Validate and test export
6. Update CI scripts

## Step 3: Validate Migration

After migration:

```bash
# 1. Validate config syntax
exfig validate exfig.pkl

# 2. Run a dry export (inspect only)
# Via MCP: exfig_inspect resource_type=all
# Or: exfig inspect -i exfig.pkl --resource-type all

# 3. Full export
exfig batch exfig.pkl

# 4. Verify output
git diff  # Check generated files are equivalent to pre-migration
```

## Step 4: Update CI

If using CI, update:
1. ExFig installation step (version pinning)
2. Export commands (if CLI syntax changed)
3. Cache keys (if cache format changed)

## Rollback

If migration fails:
```bash
# Restore backup config
cp exfig.pkl.backup exfig.pkl

# Downgrade ExFig
brew install DesignPipe/tap/exfig@2.0.0
# Or: mise use exfig@2.0.0
```
