---
name: exfig-config-review
description: Review and optimize exfig.pkl configuration — detect common issues, suggest improvements, check for missing entries. Use when the user asks to "review my exfig config", "check exfig.pkl", "optimize my config", "is my exfig config correct", or when they're having export issues that might be config-related.
---

# ExFig Config Review

Analyze `exfig.pkl` for correctness, completeness, and optimization opportunities.

## Step 1: Find and Read Config

```bash
# Find config files
ls exfig.pkl *.pkl 2>/dev/null
```

Read the config file. If multiple PKL files exist, identify the main ExFig config (it `amends` the ExFig.pkl schema).

## Step 2: Validate Syntax

If exfig MCP is available:
- Use `exfig_validate` tool to check config validity

Otherwise:
```bash
exfig validate exfig.pkl
```

## Step 3: Check for Common Issues

Load `common-issues.md` for the full checklist. Key checks:

### Critical Issues
1. **Missing file IDs** — `figma.lightFileId` is required for all Figma-based exports
2. **Wrong collection name** — `variablesColors.tokensCollectionName` must match exactly (case-sensitive)
3. **Empty entries** — platform sections with empty `Listing` produce no output
4. **Package URI version mismatch** — ensure `amends` URI matches installed ExFig version

### Optimization Opportunities
1. **DRY violations** — repeated frame names or settings across entries → use `local` Mapping + `for`-generators
2. **Missing platforms** — project has Android files but no `android` section in config
3. **Unused sections** — config has sections for platforms not in the project
4. **Default values explicitly set** — remove values that match schema defaults (reduces noise)
5. **Missing cache flag** — recommend `--cache` for CI to skip unchanged exports

### Best Practices
1. **Use variables for colors** — `variablesColors` is preferred over color styles (supports themes/modes)
2. **Template renderMode for icons** — iOS icons should use `renderMode = "template"` for tinting
3. **Vector format for icons** — prefer PDF (iOS), VectorDrawable (Android), SVG (Flutter/Web)
4. **Separate light/dark files** — if designs use separate files for dark mode, set `darkFileId`

## Step 4: Report

Present findings as:

1. **Errors** — must fix before export works
2. **Warnings** — may cause unexpected results
3. **Suggestions** — improvements for better results or maintainability

For each finding, provide the specific line/section and a concrete fix.

## Step 5: Apply Fixes

If the user agrees, apply fixes directly to `exfig.pkl`. For DRY refactoring, show the before/after diff to confirm.

After fixes, re-validate:
```bash
exfig validate exfig.pkl
```
