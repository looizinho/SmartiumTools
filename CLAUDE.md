# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Conventions live in `AGENTS.md`, imported here so they load with this file:

@AGENTS.md

## Repository boundaries

- There is no Git repository in this folder tree. The per-app repos were removed to unify the projects, so don't expect history, branches, or `git status` to work here.
- The root workspace is `SmartiumTools.xcworkspace` and currently contains only `Quick-Note/Quick Note.xcodeproj`. `Side-Search`, `Safari`, `Alarms`, and `Dynamic-Island` are outside it. Build them from their own `.xcodeproj` files.

## Commands

- Root workspace (`xcodebuild -list`, verified): `xcodebuild -workspace SmartiumTools.xcworkspace -scheme "Quick Note" build`. Its schemes are `Quick Note`, `Quick Note Watch App`, `CaptureExtension`, and `Quick Note Widget Extension`.
- Side Search: `Side-Search/Side Search.xcodeproj`. Its listed schemes are `Side Search Widget Extension` and `Textual`. The shared `Side Search` scheme file is no longer on disk, so build the app with `-target "Side Search"`.
- CSE: `Safari/Customize Search Engine.xcodeproj`, scheme `CSE`.
- Bridge package: `cd Bridge && swift build` (verified to pass).
- Alarms: `Alarms/Alare.xcodeproj`, scheme `Alare`.
- Deflector: `Dynamic-Island/Deflector/Deflector.xcodeproj`, schemes `Deflector` and `Deflector Watch`.
- There are no test targets in any project. Verify by building and running in the simulator. There is no single-test command to run.

## Architecture

- **App Group settings.** Each app keeps its settings in `UserDefaults(suiteName:)`, declared as `GroupUserDefaults` in its `Constants.swift`. Suites: `group.dev.smartium.sidesearch` (Side Search), `group.dev.smartium.tools.cbnote` (Quick Note), and `group.com.tsg0o0.cse` (CSE). Widgets, App Intents, and extensions read the same suite. A key used in one target must match what the other targets read.
- **Side Search assistants.** `AssistantType` selects an assistant. Each one pairs a model in `Models/AssistantModels` with a service in `Models/AssistantServices` (URL-based, Apple Foundation Models, Claude API, Gemini API, Side Bridge) and a description provider in `Models/AssistantDescriptionProvider`. `MainViewModel` and `AssistantViewModel` drive the UI. Side Search `SharedIntents` (`ActivateIntent`, `EndAssistantIntent`) are compiled into both the app and its widget extension.
- **Bridge is not what the app builds against.** The workspace resolves `swift-sidebridge` from GitHub (`Cizzuk/swift-sidebridge` 1.0.0), not from the local `Bridge/` folder. Edits in `Bridge/` do not reach the app until the package reference is switched to the local path. Check the package resolution before you assume a change is live.
- **Quick Note (`Quick-Note`).** Notes are files in the document directory and iCloud (`NoteService`, `DocumentDir`, `iCloudSupport`). Camera Control and Action Button entry points go through `CaptureExtension`, `CaptureIntent`, and `CaptureContext`. The Apple Watch companion talks to the phone through `WatchConnectivityManager` and `WatchConnectivitySupport`.
- **Safari (CSE).** The `CSE` app target and the `SafariExtension`/`ContentBlocker` extensions share code in `Safari/Shared` (`CSEDataManager`, `SafariSEs`, `SearchEnginePresets`, `CloudKitManager`). Search engine data syncs through CloudKit, and backup/restore runs through the App Intents in `CSE/AppIntents`.
