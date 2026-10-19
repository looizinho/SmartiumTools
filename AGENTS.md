# Repository Guidelines

## Project Structure & Module Organization

- `SmartiumTools.xcworkspace` currently groups only `Quick-Note` (the Quick Note app). `Side-Search` (Side Search) and `Safari` (Customize Search Engine) have their own `.xcodeproj` files and are opened directly.
- The folders are not Git repositories anymore: the app repos were removed to unify the projects into one. `Alarms` (Alare) and `Dynamic-Island` (Deflector) are built separately and are not part of the workspace.
- `Bridge/` is a Swift package (`swift-sidebridge`) exposing the `SideBridge` library used by Side-Search.
- App-specific extensions live inside each app: `WidgetExtension`, `AppIntents`, `CaptureExtension` (Quick-Note), `SafariExtension`/`ContentBlocker` (Safari), and `SharedIntents` (Side-Search). Shared sources use `Shared/`; screenshots belong in `ScreenShots/`.

## Build, Test, and Development Commands

- `open "SmartiumTools.xcworkspace"` — develop in Xcode (currently Quick-Note only).
- `xcodebuild -workspace SmartiumTools.xcworkspace -scheme "Quick Note" build` — build Quick Note. Workspace schemes: `Quick Note`, `Quick Note Watch App`, `CaptureExtension`, `Quick Note Widget Extension`.
- `xcodebuild -project "Side-Search/Side Search.xcodeproj" -target "Side Search" build` — build Side Search (its shared `Side Search` scheme is currently missing).
- `xcodebuild -project "Safari/Customize Search Engine.xcodeproj" -scheme CSE build` — build CSE.
- `(cd Bridge && swift build)` — build the Swift package.
- Resolve SwiftPM dependencies as configured in each project; do not bump versions ad hoc.

## Coding Style & Naming Conventions

- Swift 6 (Bridge declares `swiftLanguageModes: [.v6]`); keep code concurrency-safe.
- Follow standard Swift naming: `PascalCase` types, `camelCase` members. App and scheme names keep spaces (e.g., `Side Search`).
- No formatter or linter is configured; match the style of surrounding code and keep diffs minimal.

## Testing Guidelines

- There are no unit-test targets yet; verify changes by building and exercising the app or extension in the simulator/device.
- If adding tests, create a `*Tests` target per app and name files `FeatureTests.swift`.

## Commit & Pull Request Guidelines

- History uses short, imperative subjects without prefixes: `Update project`, `Add Invert Icon`, `Remove RequestType`.
- Development happens on a `dev` branch and reaches `main` through pull requests (e.g., `Merge pull request #113 from Cizzuk/dev`).
- PRs should describe the change, note affected apps/targets, and include simulator screenshots for UI changes.

## Git Topology

- There is no Git repository in this folder tree. Don't assume history or branches exist; if a repository is created for the unified project, it lives at the project root.
