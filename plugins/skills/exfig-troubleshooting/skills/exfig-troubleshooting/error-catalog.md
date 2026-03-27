# ExFig Error Catalog

## Authentication Errors

### FIGMA_PERSONAL_TOKEN not set
**Message:** `FIGMA_PERSONAL_TOKEN environment variable is not set`
**Cause:** Token not in shell environment
**Fix:**
```bash
export FIGMA_PERSONAL_TOKEN="figd_your_token_here"
# Add to ~/.zshrc for persistence
echo 'export FIGMA_PERSONAL_TOKEN="figd_xxx"' >> ~/.zshrc
```

### 401 Unauthorized
**Message:** `Figma API error: 401 Unauthorized`
**Cause:** Token expired, revoked, or invalid
**Fix:**
1. Go to https://www.figma.com/developers/api#access-tokens
2. Revoke old token, create new one
3. Ensure scopes: `files:read`, `file_variables:read`

## Figma API Errors

### 404 Not Found
**Message:** `Figma API error: 404 Not Found`
**Cause:** Wrong file ID or no access to file
**Fix:**
1. Open Figma file in browser, copy URL
2. Extract ID from URL: `figma.com/design/[FILE_ID]/...`
3. Verify you have access (not just a viewer link)

### 429 Too Many Requests
**Message:** `Figma API error: 429` or `Rate limit exceeded`
**Cause:** Too many API calls in short period
**Fix:**
```bash
# Reduce rate limit
exfig batch exfig.pkl --rate-limit 5

# Or wait and retry
sleep 60 && exfig batch exfig.pkl
```
Figma limits: Tier 1 (files, images): 10-20 req/min depending on plan.

### Empty component list
**Message:** Export succeeds but no files generated
**Cause:** Frame names in config don't match Figma
**Fix:**
```bash
# Check actual frame names in Figma
exfig inspect --resource-type icons -i exfig.pkl
# Or via MCP
# exfig_inspect resource_type=icons
```
Then update `figmaFrameName` in config to match.

## Penpot API Errors

### PENPOT_ACCESS_TOKEN not set
**Message:** `PENPOT_ACCESS_TOKEN environment variable is not set`
**Cause:** Token not in shell environment
**Fix:**
```bash
export PENPOT_ACCESS_TOKEN="your-token-here"
# Add to ~/.zshrc for persistence
echo 'export PENPOT_ACCESS_TOKEN="your-token-here"' >> ~/.zshrc
```

### Penpot 401 Unauthorized
**Message:** `Penpot API error: 401 Unauthorized`
**Cause:** Access token expired or invalid
**Fix:**
1. Go to Penpot → Settings → Access Tokens
2. Revoke old token, create a new one
3. Update `PENPOT_ACCESS_TOKEN` in shell profile

### Penpot 400 malformed-json
**Message:** `Penpot API error: 400 malformed-json`
**Cause:** API request format mismatch — ExFig version may be outdated
**Fix:** Update ExFig to the latest version:
```bash
brew upgrade DesignPipe/tap/exfig
```

### Penpot empty export
**Message:** Export succeeds but no files generated
**Cause:** Resources are on the canvas but not added to the shared library
**Fix:** In Penpot, select the asset and add it to the Library (left panel → Assets → click "+" or right-click → "Add to library"). Only library assets are visible to the API.

### Penpot SVG reconstruction failed
**Message:** Component 'X' — failed to reconstruct SVG, skipping
**Cause:** Component's shape tree could not be converted to SVG. May happen with complex effects (blur, shadow, image fills) or components without `mainInstanceId`.
**Fix:** Ensure the component has been opened in the Penpot editor at least once (this generates `mainInstanceId`). For complex components, simplify shapes or flatten to paths in Penpot.

## PKL Configuration Errors

### PKL evaluation error
**Message:** `PKL evaluation error: ...`
**Cause:** Syntax error or schema violation in PKL config
**Fix:**
```bash
# Get detailed error
pkl eval --format json exfig.pkl
```
Common PKL mistakes:
- Missing `new` before class name: `new iOS.ColorsEntry { ... }`
- Wrong import URI (version mismatch)
- Typo in field name (PKL is strict)

### Package not found
**Message:** `Cannot find package ...`
**Cause:** PKL package URI points to unreleased version
**Fix:**
1. Check latest release: `exfig --version`
2. Update URI in config to match

### Type mismatch
**Message:** `Expected type X, but got Y`
**Cause:** Wrong value type for a field
**Fix:** Check schema — use `pkl eval` to see expected types

## Build Errors (from source)

### Build fails
**Fix:**
```bash
swift package clean
swift build
```

### Missing dependencies on Linux
```bash
# Ubuntu/Debian
sudo apt-get install libpng-dev libwebp-dev
swift build
```

## Variable Dark Mode Errors

### Dark icons export produces empty result (cross-file)
**Message:** Warnings like `No dark color found for variable X` or zero dark icons in export summary
**Cause:** Variables reference an external Figma library, but `variablesFileId` is not set. Variable IDs are file-scoped — alias targets from the icons file can't be resolved in the library file without cross-file name matching.
**Fix:**
```pkl
variablesDarkMode = new Common.VariablesDarkMode {
  collectionName = "DesignTokens"
  lightModeName = "Light"
  darkModeName = "Dark"
  variablesFileId = "LIBRARY_FILE_ID"  // add library file ID
}
```
Find the library file ID from the Figma URL: `figma.com/design/[FILE_ID]/...`

### Dark icons export produces empty result (single-file)
**Message:** Zero dark icons in export summary, no warnings
**Cause:** Collection name or mode names don't match Figma exactly (case-sensitive)
**Fix:**
1. Open Figma → Variables panel → check exact collection name, light mode name, dark mode name
2. Update config to match exactly:
```pkl
variablesDarkMode = new Common.VariablesDarkMode {
  collectionName = "DesignTokens"  // must match exactly
  lightModeName = "Light"          // must match exactly
  darkModeName = "Dark"            // must match exactly
}
```
Run with `--verbose` to see which variables ExFig finds and why they don't match.

### Variable alias chain resolution fails
**Message:** `No dark color found for variable X` in verbose output
**Cause:** Variable alias targets can't be resolved — the alias chain points to variables in a collection whose mode can't be matched
**Fix:**
- If using a primitives collection with a different mode structure, set `primitivesModeName`:
```pkl
variablesDarkMode = new Common.VariablesDarkMode {
  collectionName = "DesignTokens"
  lightModeName = "Light"
  darkModeName = "Dark"
  primitivesModeName = "Value"  // mode name in the primitives collection
}
```
- If variables reference an external library, set `variablesFileId` (see above)

### variablesDarkMode conflicts with other dark mode approaches
**Message:** Unexpected dark icon output or duplicated dark variants
**Cause:** Multiple dark mode approaches active simultaneously
**Fix:** Use only ONE approach per entry:
- `figma.darkFileId` — separate Figma file (global)
- `common.icons.suffixDarkMode` — name suffix splitting (global)
- `variablesDarkMode` on `FrameSource` — variable bindings (per-entry)

Remove the unused approaches to avoid conflicts.

## Export Errors

### deleted variables in output
**Cause:** Figma has soft-deleted variables still referenced
**Fix:** ExFig filters these automatically. If they appear, update to latest version.

### Android pathData too long
**Cause:** Complex SVG paths exceed VectorDrawable limits
**Fix:** Simplify icons in Figma, or use `--strict-path-validation` flag

### Jinja template error
**Cause:** Custom template has syntax error
**Fix:** Check Jinja2 syntax in `.jinja` template files

## Environment Issues

### command not found: exfig
**Fix:** Install ExFig or add to PATH:
```bash
brew install DesignPipe/tap/exfig
# Or add build directory to PATH
export PATH="$PWD/.build/debug:$PATH"
```

### command not found: pkl
**Fix:** Install PKL 0.31+:
```bash
brew install pkl
```

### xcsift "signal code 5"
**Cause:** False positive when piping `swift test` through xcsift
**Fix:** Run `exfig` directly, not through pipe. This is a known non-issue.

## MCP Server Issues

### MCP server won't start
**Cause:** `exfig` not in PATH for MCP server process
**Fix:** Ensure full path in `.mcp.json`:
```json
{
  "mcpServers": {
    "exfig": {
      "command": "/usr/local/bin/exfig",
      "args": ["mcp"]
    }
  }
}
```

### MCP output garbled
**Cause:** Something is writing to stdout (MCP uses stdout for JSON-RPC)
**Fix:** ExFig MCP mode routes all output to stderr. If using custom logging, ensure it goes to stderr.
