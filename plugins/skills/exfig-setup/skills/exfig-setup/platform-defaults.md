# Platform-Specific Defaults

## iOS

```pkl
import "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/iOS.pkl"

ios {
  colors = new Listing {
    new iOS.ColorsEntry {
      codeGeneration = "swiftui"       // "swiftui" | "uikit" | "both"
      // output defaults to project root
    }
  }
  icons = new Listing {
    new iOS.IconsEntry {
      figmaFrameName = "Icons"         // Figma frame containing icon components
      assetsFolder = "Icons"           // .xcassets subfolder
      renderMode = "template"          // "template" | "original" | "automatic"
      // scales: @1x, @2x, @3x by default
    }
  }
  images = new Listing {
    new iOS.ImagesEntry {
      figmaFrameName = "Images"
      assetsFolder = "Images"
      format = "pdf"                   // "pdf" | "svg" | "png" | "webp"
    }
  }
  typography {
    codeGeneration = "swiftui"
  }
}
```

**Output structure:**
- Colors: `Colors.swift` (SwiftUI extensions), `Colors.xcassets`
- Icons: `Assets.xcassets/Icons/` (PDF/SVG vectors)
- Images: `Assets.xcassets/Images/`
- Typography: `Typography.swift`

## Android

```pkl
import "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/Android.pkl"

android {
  colors = new Listing {
    new Android.ColorsEntry {
      codeGeneration = "compose"       // "compose" | "xml" | "both"
      output = "app/src/main/res"
    }
  }
  icons = new Listing {
    new Android.IconsEntry {
      figmaFrameName = "Icons"
      output = "app/src/main/res"
      format = "vector_drawable"       // "vector_drawable" | "svg" | "png" | "webp"
    }
  }
  typography {
    codeGeneration = "compose"
    output = "app/src/main/java/com/example/ui/theme"
  }
}
```

**Output structure:**
- Colors: `res/values/colors.xml`, `Color.kt` (Compose)
- Icons: `res/drawable/` (VectorDrawable XML)
- Typography: `Type.kt` (Compose)

## Flutter

```pkl
import "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/Flutter.pkl"

flutter {
  colors = new Listing {
    new Flutter.ColorsEntry {
      output = "lib/theme"
    }
  }
  icons = new Listing {
    new Flutter.IconsEntry {
      figmaFrameName = "Icons"
      output = "assets/icons"
      format = "svg"
    }
  }
}
```

**Output structure:**
- Colors: `lib/theme/colors.dart`
- Icons: `assets/icons/*.svg` + `lib/theme/icons.dart`

## Web

```pkl
import "package://github.com/DesignPipe/exfig/releases/download/v2.0.0/exfig@2.0.0#/Web.pkl"

web {
  colors = new Listing {
    new Web.ColorsEntry {
      codeGeneration = "css"           // "css" | "scss" | "tailwind"
      outputDirectory = "src/styles"
    }
  }
  icons = new Listing {
    new Web.IconsEntry {
      figmaFrameName = "Icons"
      outputDirectory = "src/components/icons"
      format = "jsx"                   // "jsx" | "tsx" | "svg"
    }
  }
}
```

**Output structure:**
- Colors: `src/styles/colors.css` (CSS custom properties)
- Icons: `src/components/icons/*.jsx` (React components)
