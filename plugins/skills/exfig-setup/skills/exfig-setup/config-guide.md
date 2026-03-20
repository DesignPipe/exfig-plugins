# ExFig PKL Configuration Guide

## Config Structure

ExFig uses PKL (Programmable, Scalable, Safe) for configuration. A typical `exfig.pkl`:

```pkl
amends "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/ExFig.pkl"

import "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/iOS.pkl"
import "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/Common.pkl"

figma {
  lightFileId = "YOUR_FIGMA_FILE_ID"
}

common {
  variablesColors {
    tokensFileId = "YOUR_FIGMA_FILE_ID"
    tokensCollectionName = "Colors"
  }
}

ios {
  colors = new Listing {
    new iOS.ColorsEntry {
      codeGeneration = "swiftui"
    }
  }
  icons = new Listing {
    new iOS.IconsEntry {
      figmaFrameName = "Icons"
      assetsFolder = "Icons"
    }
  }
}
```

## Key Sections

### figma
Figma file references:
- `lightFileId` — main Figma file ID (required for most exports)
- `darkFileId` — separate dark mode file (optional, if dark colors are in a different file)

### common
Shared settings:
- `variablesColors` — Figma Variables for color tokens
  - `tokensFileId` — file containing variables
  - `tokensCollectionName` — variable collection name (e.g., "Colors", "Tokens")
- `tokensFile` — local `.tokens.json` path (alternative to Figma Variables)

### Platform sections (ios, android, flutter, web)
Each platform has:
- `colors` — color export entries
- `icons` — icon export entries (SVG components from Figma)
- `images` — image export entries (raster images)
- `typography` — text style export

## DRY Patterns

Use `local` variables to avoid duplication:

```pkl
local iconFrames: Mapping<String, String> = new {
  ["Navigation"] = "navigation"
  ["Actions"] = "actions"
  ["Status"] = "status"
}

ios {
  icons = new Listing {
    for (frameName, folder in iconFrames) {
      new iOS.IconsEntry {
        figmaFrameName = frameName
        assetsFolder = folder
        renderMode = "template"
      }
    }
  }
}
```

`local` properties don't appear in output — they're compile-time only.

## Multi-Platform Config

Export to multiple platforms from one config:

```pkl
ios {
  colors = new Listing { new iOS.ColorsEntry { codeGeneration = "swiftui" } }
}

android {
  colors = new Listing { new Android.ColorsEntry { codeGeneration = "compose" } }
}

flutter {
  colors = new Listing { new Flutter.ColorsEntry {} }
}
```

## Validation

Validate config without running exports:
```bash
pkl eval --format json exfig.pkl    # Check PKL syntax
exfig validate exfig.pkl            # Check ExFig-specific rules
```
