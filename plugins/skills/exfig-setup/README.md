# exfig-setup

Interactive setup wizard for ExFig. Guides you through installation, Figma token configuration, PKL config generation, first export, and CI setup.

## Trigger Phrases

- "Set up ExFig for my project"
- "Configure ExFig"
- "Install ExFig"
- "Start using ExFig"
- "Export Figma to iOS/Android/Flutter/Web"

## Phases

1. **Environment Check** — ExFig binary + PKL
2. **Figma Token** — verify FIGMA_PERSONAL_TOKEN
3. **Project Analysis** — detect platform, check existing config
4. **Config Generation** — interactive `exfig init` or MCP-assisted discovery
5. **First Export** — validate and run
6. **CI/CD** — optional GitHub Actions template

## Progressive Disclosure Files

| File | Content |
|------|---------|
| `install-guide.md` | Installation options (Homebrew, mise, binary, source) |
| `config-guide.md` | PKL config structure and patterns |
| `platform-defaults.md` | Platform-specific config templates |
| `ci-integration.md` | CI/CD templates and caching |
