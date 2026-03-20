# CI/CD Integration

## GitHub Actions

### Basic: Manual + Scheduled Export

```yaml
name: ExFig Export
on:
  workflow_dispatch:
  schedule:
    - cron: '0 9 * * 1'  # Weekly Monday 9am UTC

jobs:
  export:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install ExFig
        run: brew install DesignPipe/tap/exfig

      - name: Export design resources
        env:
          FIGMA_PERSONAL_TOKEN: ${{ secrets.FIGMA_TOKEN }}
        run: exfig batch exfig.pkl --cache

      - name: Commit changes
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add -A
          git diff --staged --quiet || git commit -m "chore: update design resources"
          git push
```

### Advanced: PR-based Export

```yaml
name: ExFig Export PR
on:
  workflow_dispatch:
  schedule:
    - cron: '0 9 * * 1'

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
        run: exfig batch exfig.pkl --cache

      - name: Create PR if changed
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git checkout -b chore/design-export-$(date +%Y%m%d)
          git add -A
          if ! git diff --staged --quiet; then
            git commit -m "chore: update design resources"
            git push -u origin HEAD
            gh pr create --title "chore: update design resources" \
              --body "Automated export from Figma via ExFig"
          fi
```

## Caching

ExFig supports version tracking to skip unchanged exports:

```yaml
- name: Cache ExFig version tracking
  uses: actions/cache@v4
  with:
    path: .exfig-cache
    key: exfig-cache-${{ hashFiles('exfig.pkl') }}
    restore-keys: exfig-cache-

- name: Export with cache
  run: exfig batch exfig.pkl --cache
```

## Version Pinning

Pin ExFig version in CI to avoid breaking changes:

```yaml
- name: Install ExFig
  run: brew install DesignPipe/tap/exfig@2.0.0
```

Or with mise:
```yaml
- name: Install mise + ExFig
  run: |
    curl https://mise.run | sh
    mise use exfig@2.0.0
```

## Secrets Setup

1. Go to repository Settings → Secrets and variables → Actions
2. Add `FIGMA_TOKEN` with your Figma Personal Access Token
3. Use a dedicated Figma account for CI (not personal) for team projects
