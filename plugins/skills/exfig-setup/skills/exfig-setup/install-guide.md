# ExFig Installation Guide

## Option 1: Homebrew (macOS, recommended)

```bash
brew install DesignPipe/tap/exfig
```

Verify:
```bash
exfig --version
```

## Option 2: mise (cross-platform)

```bash
mise use exfig@latest
```

Or pin a specific version:
```bash
mise use exfig@2.0.0
```

## Option 3: Binary download

Download the latest release from https://github.com/DesignPipe/exfig/releases

```bash
# macOS (Apple Silicon)
curl -L https://github.com/DesignPipe/exfig/releases/latest/download/exfig-macos-arm64.tar.gz | tar xz
sudo mv exfig /usr/local/bin/

# macOS (Intel)
curl -L https://github.com/DesignPipe/exfig/releases/latest/download/exfig-macos-x86_64.tar.gz | tar xz
sudo mv exfig /usr/local/bin/

# Linux (x86_64)
curl -L https://github.com/DesignPipe/exfig/releases/latest/download/exfig-linux-x86_64.tar.gz | tar xz
sudo mv exfig /usr/local/bin/
```

## Option 4: Build from source

```bash
git clone https://github.com/DesignPipe/exfig.git
cd exfig
swift build -c release
# Binary at .build/release/exfig
```

Requires Swift 6.2+ toolchain.

## PKL Installation

ExFig configs use PKL format. Install PKL 0.31+:

```bash
# macOS
brew install pkl

# Or via mise
mise use pkl@latest

# Or binary
curl -L https://github.com/apple/pkl/releases/latest/download/pkl-macos-aarch64 -o pkl
chmod +x pkl && sudo mv pkl /usr/local/bin/
```

## Shell Completions

After installing ExFig, generate shell completions:

```bash
# Bash
exfig --generate-completion-script bash > ~/.bash_completions/exfig
# Zsh
exfig --generate-completion-script zsh > ~/.zsh/completions/_exfig
# Fish
exfig --generate-completion-script fish > ~/.config/fish/completions/exfig.fish
```
