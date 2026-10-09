# Modular

A production-ready reference implementation of modular SwiftUI architecture for iOS applications.

**Modular** demonstrates how to build scalable, maintainable iOS products through intentional module boundaries, dependency management, and clear architectural rules.

## Overview

This project showcases a fully-featured iOS app structured across multiple Swift Packages:

- **3 Vertical Features**: Catalog, Favorites, Search
- **2 Shared Foundations**: DesignSystem, ItemDomain
- **2 Infrastructure Layers**: Networking, Analytics
- **Clean Dependency Graph**: No cycles, feature-independent, preview-optimized

## Architecture at a Glance

```
App Target (Composition Root)
│
├── AppComposition.swift (dependency assembly)
├── ContentView.swift (feature orchestration)
└── Live implementations

Packages/
├── CatalogFeature (vertical feature)
├── FavoritesFeature (vertical feature)
├── SearchFeature (vertical feature)
├── DesignSystem (UI foundation)
├── ItemDomain (shared domain)
├── Networking (HTTP infrastructure)
├── AnalyticsAPI (analytics contract)
└── AnalyticsLive (third-party SDK bridge)
```

## Key Architectural Decisions

### 1. **Features Don't Import Each Other**
Cross-feature workflows are orchestrated at the app layer through lightweight action callbacks, not direct feature-to-feature imports.

### 2. **Features Request Inputs, Not Infrastructure**
Views receive lightweight `CatalogInput`, `FavoritesInput`, `SearchInput` structs containing only what they need. The app layer assembles actual infrastructure (HTTP clients, analytics trackers, repositories).

### 3. **Third-Party SDKs Are Contained**
Features depend only on `AnalyticsAPI` (protocol + no-op implementation). Real SDK integration happens in `AnalyticsLive`, isolated from feature code.

### 4. **Previews Are Isolated by Dependency Graph**
A feature preview compiles only what the feature depends on, not the full app graph. This is enforced through clean module boundaries, not by folder structure.

### 5. **Shared Models Are Stable and Narrow**
`ItemDomain` contains only the `Item` model and domain helpers. This high-fan-in module changes rarely and carefully.

### 6. **Public APIs Are Intentionally Minimal**
Each module exposes only what external consumers need. Implementation details remain internal or package-scoped.

## Project Structure

### App Target (`Modular/`)

```
Modular/
├── ModularApp.swift          # App entry point
├── ContentView.swift         # Tab composition + state
├── AppComposition.swift      # Dependency assembly root
├── LiveCatalogRepository.swift
├── Assets.xcassets/
└── ...
```

### Design System (`Packages/DesignSystem/`)

Reusable UI primitives:
- `AppButton`, `Card`, `Badge`
- Spacing tokens (`AppSpacing`)
- No feature logic, domain models, or infrastructure

### Domain (`Packages/ItemDomain/`)

Shared domain model:
- `Item` (catalog/favorite item)
- Domain helpers
- Used by multiple features

### Features

Each vertical feature (Catalog, Favorites, Search) owns:
- Feature UI (`CatalogView`, `FavoritesView`, `SearchView`)
- Feature state and local logic
- Input models and action callbacks
- Feature-scoped fixtures and previews
- Feature-scoped tests

Features depend on: `DesignSystem`, `ItemDomain`, and (if needed) `AnalyticsAPI`.

### Infrastructure

**Networking** (`Packages/Networking/`)
- `HTTPClient`, `HTTPRequest`, `HTTPResponse`
- Transport layer only, no business logic
- No feature-specific concerns

**Analytics** (`Packages/AnalyticsAPI/` + `AnalyticsLive/`)
- `AnalyticsAPI`: Protocol contract + no-op implementation
- `AnalyticsLive`: Concrete bridge to third-party SDK
- Features depend only on the API, not the implementation

## Building and Running

### Requirements
- Xcode 15.1+
- Swift 6.0+
- iOS 17+

### Build
```bash
cd Modular
xcodebuild -workspace Modular.xcworkspace -scheme Modular
```

### Open in Xcode
```bash
open Modular.xcworkspace
```

## Testing

Run feature-level tests:

```bash
# Run all tests
xcodebuild -workspace Modular.xcworkspace -scheme Modular test

# Run specific feature tests
xcodebuild -workspace Modular.xcworkspace -scheme CatalogFeatureTests test
xcodebuild -workspace Modular.xcworkspace -scheme FavoritesFeatureTests test
xcodebuild -workspace Modular.xcworkspace -scheme SearchFeatureTests test
xcodebuild -workspace Modular.xcworkspace -scheme NetworkingTests test
```

## Previews

Each feature includes isolated SwiftUI previews that compile only the feature's dependencies:

- `CatalogFeature` preview: compiles CatalogFeature + DesignSystem + ItemDomain
- `SearchFeature` preview: compiles SearchFeature + DesignSystem + ItemDomain
- `FavoritesFeature` preview: compiles FavoritesFeature + DesignSystem + ItemDomain
- `DesignSystem` previews: compile only DesignSystem

## Development Workflow

### Adding a New Feature

1. Create a new package in `Packages/NewFeature/`
2. Define feature input model and view
3. Add fixtures for deterministic previews
4. Implement actions for cross-feature communication (if needed)
5. Wire in `AppComposition.swift`
6. Compose in `ContentView.swift`

### Adding Infrastructure

1. Create or enhance infrastructure package (e.g., `Packages/Networking/`)
2. Keep it feature-agnostic
3. Create implementation wrapper in app layer (e.g., `LiveCatalogRepository.swift`)
4. Inject through feature inputs

### Modifying Shared Domain

Changes to `ItemDomain` ripple to all consumers (CatalogFeature, FavoritesFeature, SearchFeature). Keep changes careful and intentional.

## Philosophy

> **Modular architecture is about making intentional boundaries that improve ownership, reduce coupling, and enable independent development.**

This project demonstrates:
- ✅ Boundaries that reduce iteration friction
- ✅ Module independence enabling parallel development
- ✅ Preview performance as an architectural signal
- ✅ Testing at module scope, not just app scope
- ✅ Infrastructure contained, not scattered
- ✅ Scalability without over-modularization

## Related Documentation

- **ARCHITECTURE.md** — Detailed architectural rules and principles
- **MODULAR_ARCHITECTURE_RULES.md** — 13 rules extracted from production experience

## Learning Source

This project is based on **modularization-elements**, a comprehensive study of modular Swift architecture. See `ARCHITECTURE_JOURNAL.md` in the source project for detailed findings from each stage of the investigation.

## For More Information

Read the companion article on [uimatters.io](https://uimatters.io/) for deep dive into:
- Why these boundaries matter
- Trade-offs and costs
- Build performance implications
- Production considerations

---

**This is a reference implementation**, not a framework or template. Adapt the patterns to your product's specific constraints and evolution.
