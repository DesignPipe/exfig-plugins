---
name: exfig-setup
description: Interactive setup wizard for ExFig — guides through installation, Figma token configuration, PKL config generation, first export, and CI setup. Use when the user asks to "set up exfig", "configure exfig", "install exfig", "start using exfig", "integrate figma export", "export figma to iOS/Android/Flutter/Web", or mentions needing help with initial ExFig configuration.
---

# ExFig Setup Wizard

Guide the user through setting up ExFig step by step. Adapt to their environment — skip completed steps, focus on what's missing.

## Phase 1: Environment Check

### 1.1 Check ExFig installation
```bash
which exfig && exfig --version
```

If not found, check dev build:
```bash
ls .build/debug/exfig 2>/dev/null
```

**If ExFig is not installed**, load `install-guide.md` and help the user install it.

### 1.2 Check PKL
```bash
which pkl && pkl --version
```

PKL 0.31+ is required for ExFig schemas. If missing:
```bash
# macOS
brew install pkl
# Or via mise
mise use pkl@latest
```

## Phase 2: Figma Token

### 2.1 Check token
```bash
echo ${FIGMA_PERSONAL_TOKEN:+"Token is set"} || echo "Token not set"
```

**If token is not set**, guide the user:

1. Go to https://www.figma.com/developers/api#access-tokens
2. Click "Create a personal access token"
3. Scopes needed: `files:read`, `file_variables:read` (for design tokens)
4. Add to shell profile:
```bash
# Add to ~/.zshrc or ~/.bashrc
export FIGMA_PERSONAL_TOKEN="figd_xxxxx"
```

For CI environments, recommend using repository secrets:
```yaml
env:
  FIGMA_PERSONAL_TOKEN: ${{ secrets.FIGMA_TOKEN }}
```

## Phase 3: Project Analysis

### 3.1 Detect project type
Look for platform markers in the current directory:

| Marker | Platform |
|--------|----------|
| `*.xcodeproj` or `Package.swift` | iOS |
| `build.gradle` or `build.gradle.kts` | Android |
| `pubspec.yaml` | Flutter |
| `package.json` + React/Vue/Svelte | Web |

```bash
# Quick detection
ls *.xcodeproj 2>/dev/null && echo "iOS"
ls build.gradle* 2>/dev/null && echo "Android"
ls pubspec.yaml 2>/dev/null && echo "Flutter"
ls package.json 2>/dev/null && echo "Web"
```

### 3.2 Check existing config
```bash
ls exfig.pkl 2>/dev/null
```

If `exfig.pkl` exists, skip to Phase 5 (validation).

### 3.3 Ask for Figma file
Ask the user for their Figma file URL or ID. ExFig can extract the file ID from full URLs:
- Full URL: `https://www.figma.com/design/ABC123/MyDesign`
- Just ID: `ABC123`

## Phase 4: Config Generation

### 4.1 Try MCP discovery first
If exfig MCP is available, use `exfig_inspect` with `resource_type=all` to discover what's in the Figma file. This gives us:
- Color styles count and sample names
- Icon components count
- Image assets
- Typography styles

### 4.2 Generate config
Run the interactive init command:
```bash
exfig init --platform {detected_platform}
```

Or if MCP is available and we have discovery data, help the user create a tailored `exfig.pkl` manually. Load `config-guide.md` for PKL config patterns and `platform-defaults.md` for platform-specific defaults.

### 4.3 Customize config
Based on discovery results, suggest:
- Which Figma frames to export as icons
- Color variable collections to use
- Typography styles to include
- Output directories matching project structure

## Phase 5: First Export & Validation

### 5.1 Validate config
```bash
exfig validate exfig.pkl
# Or via MCP: exfig_validate
```

### 5.2 Run first export
```bash
# Single resource type
exfig colors -i exfig.pkl

# Or all at once
exfig batch exfig.pkl
```

### 5.3 Verify output
Check that generated files exist and look correct:
- iOS: `.xcassets` directories, Swift extensions
- Android: `res/values/colors.xml`, Compose files
- Flutter: `lib/` Dart files, `assets/` SVGs
- Web: CSS variables, JSX components

### 5.4 Handle errors
If export fails, check:
1. Is `FIGMA_PERSONAL_TOKEN` valid? (401 error)
2. Is the Figma file ID correct? (404 error)
3. Are frame names in config matching Figma? (empty export)

For persistent errors, suggest the troubleshooting skill.

## Phase 6: CI/CD (Optional)

Ask if the user wants CI integration. If yes, load `ci-integration.md` for templates.

Quick GitHub Actions setup:
```yaml
# .github/workflows/exfig-export.yml
name: ExFig Export
on:
  workflow_dispatch:
  schedule:
    - cron: '0 9 * * 1'  # Weekly Monday 9am

jobs:
  export:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install ExFig
        run: brew install DesignPipe/tap/exfig
      - name: Export
        env:
          FIGMA_PERSONAL_TOKEN: ${{ secrets.FIGMA_TOKEN }}
        run: exfig batch exfig.pkl
      - name: Commit changes
        run: |
          git add -A
          git diff --staged --quiet || git commit -m "chore: update design resources"
          git push
```

## Completion

After successful setup, summarize what was configured:
- ExFig version installed
- Platform(s) configured
- Resources being exported (colors, icons, images, typography)
- Output locations
- CI status (if configured)

Recommend:
- Install `exfig-export` plugin for `/export-colors`, `/export-icons` slash commands
- Install `exfig-config-review` plugin for ongoing config optimization
