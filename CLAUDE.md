# CLAUDE.md

Runlab is an iOS app — personal running coach, performance analyst, and training planner. Syncs from Strava and Apple HealthKit, authenticates via Supabase, uses an AI coach (Claude API proxied through a backend) for personalized insights.

Full product vision: `blueprint/vision.md`. Detailed rules split into `@.cursor/rules/`.

---

## Build & Run

- **Xcode:** `open runlab.xcodeproj`
- **CLI build:** `xcodebuild -project runlab.xcodeproj -scheme runlab -sdk iphonesimulator build`
- **Tests:** No test target yet. When added: `xcodebuild -project runlab.xcodeproj -scheme runlab test -sdk iphonesimulator -destination 'platform=iOS Simulator,name=iPhone 16'`

---

## Target & Requirements

- iOS 17.0+, iPhone and iPad. Primary test device: iOS 26.3
- Swift 5.9+ with `@Observable` macro (Observation framework — **not** `ObservableObject`)
- `SWIFT_DEFAULT_ACTOR_ISOLATION = MainActor`, `SWIFT_APPROACHABLE_CONCURRENCY = YES`
- Bundle ID: `runlab.runlab`, Team: `7C6P95YDJR` (automatic signing)

---

## Key Architectural Rules

- **Single target** — no Swift packages or CocoaPods. File system synchronized groups (objectVersion 77).
- **Observation** — `@Observable` only (not `ObservableObject`). Inject via `.environment()`, not `@EnvironmentObject`.
- **SwiftData** — `@Model` for persistence. `ModelContainer` in `runlabApp.swift`. Views use `@Query` with `SortDescriptor` and `#Predicate`.
- **Async/Await** — all network/IO. No Combine.
- **No force-unwraps** in user-facing code paths.

---

## Coding Conventions

- Use `@Observable` — never `ObservableObject` or `@Published`.
- Use `async let` for parallel async calls.
- All `@Query` macros use `SortDescriptor` — not deprecated `\.keyPath` sort.
- All interactive elements: `.accessibilityLabel` and `.accessibilityHint`.
- Haptics: `UIImpactFeedbackGenerator(.medium)` on plan commit, workout complete, badge earned, message sent.
- Errors: global `AppErrorHandler` (`@Observable`) with dismissible banner toast (top slide-in, alertRed left border).
- Charts: Swift Charts (native). White solid line primary; `textSecondary` dashed secondary. No gradient fills unless specified.
- Maps: `Map` from MapKit (iOS 17+). Dark style via `.mapStyle(.standard(...))` with dark colorScheme.
- No narrating comments — only explain non-obvious intent or formulas.

---

## Detailed Rules (split files)

@.cursor/rules/architecture.md
@.cursor/rules/design-system.md
@.cursor/rules/training-zones.md
@.cursor/rules/training-plans.md
@.cursor/rules/backend-services.md
@.cursor/rules/onboarding-navigation.md
@.cursor/rules/monetization-roadmap.md
