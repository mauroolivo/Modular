# Modular

Production-style reference app showing modular SwiftUI architecture for iOS.

`Modular` demonstrates how to structure a growing app with clear ownership, explicit dependency direction, and practical trade-offs.

## What is included

- 3 feature modules: `CatalogFeature`, `FavoritesFeature`, `SearchFeature`
- Shared foundations: `DesignSystem`, `ItemDomain`
- Infrastructure: `Networking`, `AnalyticsAPI`, `AnalyticsLive`
- One framework-target comparison module: `CatalogWidgets`

## Architecture snapshot

```text
App target (composition root)
├── CatalogFeature      ─┬── DesignSystem
│                        ├── ItemDomain
│                        └── AnalyticsAPI
├── FavoritesFeature    ─┬── DesignSystem
│                        ├── ItemDomain
│                        └── AnalyticsAPI
├── SearchFeature       ─┬── DesignSystem
│                        ├── ItemDomain
│                        └── AnalyticsAPI
├── CatalogWidgets (framework target) ─┬── DesignSystem
│                                       └── ItemDomain
├── Networking
└── AnalyticsLive ── AnalyticsAPI + SDK adapter
```

## Core decisions

- Features do not import sibling features for app-level flows.
- Features receive lightweight inputs; app target assembles live infrastructure.
- Third-party SDKs stay contained behind API + adapter boundaries.
- Shared high fan-in modules stay narrow and stable.
- Preview/test scope is module-local where possible.

## Project layout

```text
Modular/
├── Modular/                 # app target source
├── Packages/                # local Swift packages
├── CatalogWidgets/          # Xcode framework target
├── ARTICLE_UIMATTERS.md     # full architecture article
├── _UI_MATTERS_VISION_MISSION.md
├── Modular.xcodeproj
└── Modular.xcworkspace
```

## Build and run

```bash
cd /Users/mauro/files/code/Swift/Modular
open Modular.xcworkspace
```

Optional CLI build:

```bash
cd /Users/mauro/files/code/Swift/Modular
xcodebuild -workspace Modular.xcworkspace -scheme Modular build
```

## Testing

Run full test suite:

```bash
cd /Users/mauro/files/code/Swift/Modular
xcodebuild -workspace Modular.xcworkspace -scheme Modular test
```

Run package tests directly (example):

```bash
cd /Users/mauro/files/code/Swift/Modular/Packages/CatalogFeature
swift test
```

## Notes

This repository is a reference implementation, not a drop-in template. Use the boundary principles, then adapt them to your product constraints.
