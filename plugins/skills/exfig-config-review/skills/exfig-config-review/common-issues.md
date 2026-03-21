# Common ExFig Config Issues

## Critical Errors

### Missing Figma file ID
```pkl
// BAD — no file ID
figma {}

// GOOD
figma {
  lightFileId = "ABC123DEF456"
}
```

### Wrong package URI version
```pkl
// BAD — version doesn't match installed ExFig
amends "package://github.com/DesignPipe/exfig/releases/download/v1.0.0/exfig@1.0.0#/ExFig.pkl"

// GOOD — matches installed version
amends "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/ExFig.pkl"
```
Check installed version: `exfig --version`

### Empty Listing
```pkl
// BAD — empty listing does nothing
ios {
  colors = new Listing {}
}

// GOOD — has at least one entry
ios {
  colors = new Listing {
    new iOS.ColorsEntry {}
  }
}
```

### Wrong collection name
```pkl
// BAD — case mismatch with Figma
variablesColors {
  tokensCollectionName = "colors"  // Figma has "Colors" (capital C)
}
```
Use `exfig_inspect` with `resource_type=colors` to see actual collection names.

## Warnings

### Duplicate frame names across entries
```pkl
// WARNING — same frame exported twice
icons = new Listing {
  new iOS.IconsEntry { figmaFrameName = "Icons"; assetsFolder = "nav" }
  new iOS.IconsEntry { figmaFrameName = "Icons"; assetsFolder = "actions" }
}
```
These will export the same icons to different folders. If intentional, add a comment.

### Missing output directory
```pkl
// WARNING — uses default output (project root), may clutter
android {
  colors = new Listing {
    new Android.ColorsEntry {}  // output defaults to "."
  }
}

// BETTER — explicit output
android {
  colors = new Listing {
    new Android.ColorsEntry {
      output = "app/src/main/res"
    }
  }
}
```

### PNG format for icons
```pkl
// WARNING — PNG icons don't scale well
icons = new Listing {
  new iOS.IconsEntry {
    format = "png"  // Prefer PDF or SVG for vectors
  }
}
```

## Penpot Config Issues

### Missing penpotSource.fileId
```pkl
// BAD — penpotSource without fileId
penpotSource = new Common.PenpotSource {}

// GOOD
penpotSource = new Common.PenpotSource {
  fileId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

### Both Figma and Penpot source set
```pkl
// WARNING — both sources configured on one entry
new iOS.ColorsEntry {
  figmaFileId = "ABC123"
  penpotSource = new Common.PenpotSource { fileId = "..." }
}
```
When both are set, `resolvedSourceKind` auto-detects Penpot (it takes priority). Remove the unused `figmaFileId` to avoid confusion.

### pathFilter too specific
```pkl
// BAD — overly specific, may match nothing
penpotSource = new Common.PenpotSource {
  fileId = "..."
  pathFilter = "Brand/Colors/Primary/Light"
}

// BETTER — broader filter, or remove entirely
penpotSource = new Common.PenpotSource {
  fileId = "..."
  pathFilter = "Brand"  // matches Brand/Colors, Brand/Primary, etc.
}
```
If export is empty, try removing `pathFilter` first to verify assets exist in the library.

## Optimization Suggestions

### DRY with local Mapping
```pkl
// BEFORE — repetitive
icons = new Listing {
  new iOS.IconsEntry { figmaFrameName = "Navigation"; assetsFolder = "nav"; renderMode = "template" }
  new iOS.IconsEntry { figmaFrameName = "Actions"; assetsFolder = "actions"; renderMode = "template" }
  new iOS.IconsEntry { figmaFrameName = "Status"; assetsFolder = "status"; renderMode = "template" }
}

// AFTER — DRY with for-generator
local iconFrames: Mapping<String, String> = new {
  ["Navigation"] = "nav"
  ["Actions"] = "actions"
  ["Status"] = "status"
}

icons = new Listing {
  for (frame, folder in iconFrames) {
    new iOS.IconsEntry {
      figmaFrameName = frame
      assetsFolder = folder
      renderMode = "template"
    }
  }
}
```

### Multi-platform color sharing
```pkl
// Share variablesColors config across platforms
common {
  variablesColors {
    tokensFileId = "ABC123"
    tokensCollectionName = "Colors"
  }
}
// Each platform's ColorsEntry inherits from common
```

### Version tracking for CI
```bash
# Add --cache to skip unchanged exports
exfig batch exfig.pkl --cache

# Force re-export when needed
exfig batch exfig.pkl --force
```
