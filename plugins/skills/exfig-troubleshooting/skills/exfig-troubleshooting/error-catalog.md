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
