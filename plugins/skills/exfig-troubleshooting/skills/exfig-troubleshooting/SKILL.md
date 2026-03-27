---
name: exfig-troubleshooting
description: Diagnose and fix ExFig errors — error catalog, environment checks, common fixes. Use when the user encounters ExFig errors, export failures, configuration problems, or asks "why is exfig not working", "exfig error", "export failed", "figma api error", "pkl error".
---

# ExFig Troubleshooting

Systematic approach to diagnosing ExFig issues.

## Step 1: Gather Information

### Environment
```bash
exfig --version
pkl --version
swift --version 2>/dev/null
echo ${FIGMA_PERSONAL_TOKEN:+"Token is set (${#FIGMA_PERSONAL_TOKEN} chars)"} || echo "Token NOT set"
echo ${PENPOT_ACCESS_TOKEN:+"Penpot token set"} || echo "Penpot token NOT set"
```

### Config
```bash
ls exfig.pkl 2>/dev/null && echo "Config found" || echo "No config"
```

If MCP available, run `exfig_validate` to get config status.

### Recent error
Ask the user for the exact error message or output.

## Step 2: Diagnose

Match the error against the catalog. Load `error-catalog.md` for the full reference.

### Quick Diagnosis Table

| Symptom | Likely Cause | Quick Fix |
|---------|-------------|-----------|
| `command not found: exfig` | Not installed or not in PATH | `brew install DesignPipe/tap/exfig` |
| `FIGMA_PERSONAL_TOKEN not set` | Missing env variable | `export FIGMA_PERSONAL_TOKEN=figd_xxx` |
| `401 Unauthorized` | Invalid or expired token | Regenerate at figma.com/developers |
| `404 Not Found` | Wrong file ID | Check Figma URL, extract correct ID |
| `429 Too Many Requests` | Rate limited | Wait, reduce `--rate-limit` value |
| `No exfig.pkl found` | Missing config | Run `exfig init` |
| `PKL evaluation error` | Syntax error in config | Run `pkl eval exfig.pkl` for details |
| `Empty export` | Frame names don't match | Use `exfig_inspect` to check actual names |
| `PENPOT_ACCESS_TOKEN not set` | Missing env variable | `export PENPOT_ACCESS_TOKEN=xxx` |
| Penpot `401` | Invalid Penpot token | Regenerate in Penpot Settings → Access Tokens |
| Penpot empty export | Assets not in library | Add to shared Library in Penpot |
| `signal code 5` | False positive from xcsift | Run `exfig` directly, not through pipe |
| Dark icons empty | Missing `variablesFileId` for library vars | Set `variablesFileId` to library file ID |
| Dark icons empty (single-file) | Wrong collection/mode names | Check names in Figma Variables panel (case-sensitive) |
| `No dark color found` warnings | Variable alias chain unresolved | Check `--verbose` logs; verify `variablesFileId` or `primitivesModeName` |
| Build fails | Stale cache | `swift package clean && swift build` |

## Step 3: Fix

Apply the fix from the catalog. After fixing:

1. **Re-validate** config: `exfig validate exfig.pkl`
2. **Re-run** the failed command
3. **Verify** output files were created

## Step 4: Prevent

Based on the issue, suggest preventive measures:
- CI validation step before export
- Token rotation reminders
- Config review after Figma file restructuring
- Version pinning in CI

## Escalation

If the issue isn't in the catalog:
1. Check ExFig GitHub Issues: https://github.com/DesignPipe/exfig/issues
2. Run with `--verbose` flag for detailed logs
3. Collect: ExFig version, PKL version, OS, error output, config (redact tokens)
