# Architecture & Project Structure

## Project Tree

```
runlab/
├── runlabApp.swift            # @main entry, ModelContainer, service init
├── ContentView.swift          # Root TabBar
├── DesignSystem.swift         # All design tokens, shared components
│
├── Fonts/                     # Bundled fonts (registered in Info.plist UIAppFonts)
│   ├── Cooper-BT-Medium.otf   # Cooper BT Medium (500) — Section Header
│   ├── Manrope-Regular.ttf    # Manrope Regular (400) — Body
│   └── ReadexPro-Light.ttf    # Readex Pro Light (300) — Chart Labels
│
├── Models/                    # SwiftData @Model classes
│   ├── RunActivity.swift
│   ├── TrainingPlan.swift
│   ├── PlannedWorkout.swift
│   ├── AthleteProfile.swift
│   ├── HealthSnapshot.swift
│   └── CoachMessage.swift
│
├── Services/                  # @Observable singletons
│   ├── StravaService.swift
│   ├── HealthKitService.swift
│   ├── BackendService.swift
│   ├── AICoachService.swift
│   ├── SubscriptionService.swift
│   └── KeychainHelper.swift
│
├── Onboarding/
├── Profile/
├── Home/
│   ├── HomeView.swift, TrainingStatusWidget.swift
│   ├── TodaysWorkoutCard.swift, CoachNudgeCard.swift
│   ├── RecentActivityCard.swift, HealthSnapshotRow.swift
├── Training/
│   ├── TrainingView.swift, PlanLibraryView.swift
│   ├── PlanDetailView.swift, PlanCommitmentView.swift
│   ├── ActivePlanView.swift, PlanCalendarView.swift
│   └── PlanCatalog.swift
├── Analytics/
│   ├── ActivityFeedView.swift, ActivityRowView.swift
│   ├── ActivityDetailView.swift, ActivityTimelineChart.swift
│   ├── ActivityMapCard.swift, PaceZoneChart.swift
│   ├── IntervalDetector.swift, IntervalsTableView.swift
│   └── TrainingLoad.swift
├── Coach/
│   └── CoachView.swift
├── Tools/
│   ├── ToolsView.swift, PaceCalculatorView.swift
│   ├── RacePredictorView.swift, SplitPlannerView.swift
└── Assets.xcassets/
```

## Data Models (`runlab/Models/`)

All SwiftData `@Model` classes:

| Model | Purpose |
|-------|---------|
| `RunActivity` | Strava activity (pace, HR, cadence, polyline, laps JSON, markdownSummary, sampled streams) |
| `TrainingPlan` | Committed plan with `@Relationship` to workouts, race location, race date |
| `PlannedWorkout` | Scheduled session (concrete date, workout type, target distance, pace zone) |
| `AthleteProfile` | Physical profile, training zones (HR Z1–Z5 bpm, pace Z1–Z6 sec/unit), HRmax, LTHR, T-pace, goals, preferences, zone history |
| `HealthSnapshot` | Daily HRV, RHR, sleep, VO2 max, readiness score from HealthKit |
| `CoachMessage` | Chat history (role: "user" or "assistant") |

`ModelContainer` initialized in `runlabApp.swift` with `.inMemory(false)`.

## Services (`runlab/Services/`)

All `@Observable` singletons (`.shared` or `.environment()`):

| Service | Responsibility |
|---------|---------------|
| `StravaService` | OAuth 2.0, token refresh, `syncActivities(since:)` |
| `HealthKitService` | Authorization, `fetchHealthSnapshots(days:)`, `HKObserverQuery` |
| `BackendService` | REST to backend — markdown generation, athlete profile, subscription validation, account deletion |
| `AICoachService` | Prompt assembly (system + profile + markdowns + history), streams via `AsyncThrowingStream` |
| `SubscriptionService` | StoreKit 2 — `isProUser`, `purchase(_:)`, `checkEntitlements()` |
| `KeychainHelper` | Strava tokens + Supabase JWT in Keychain via Security framework |

## Training Load (`runlab/Analytics/TrainingLoad.swift`)

- **rTSS** ≈ `(duration_seconds / 3600) × IF² × 100`, where `IF = NGP / threshold_pace`
- **CTL (Fitness)** — 42-day EWA: decay = `exp(-1/42)`
- **ATL (Fatigue)** — 7-day EWA: decay = `exp(-1/7)`
- **TSB (Form)** = previous day CTL − previous day ATL

Status labels: Peak (TSB > 5, CTL > 30), Productive (TSB -10…5), Maintaining (TSB > 5, CTL < 30), Recovery (TSB -10…-20), Overreaching (TSB < -20).

## Performance Management Chart (Home Dashboard)

Three overlapping Swift Charts lines (default 90d, selectable 30/90/180/365):
- Fitness (CTL): `signalTeal` solid
- Fatigue (ATL): `cautionYellow` solid
- Form (TSB): `textSecondary` dashed, zero baseline rule

Status badge pinned above. CTL/ATL/TSB as `MetricDisplay` tiles below. Tap for tooltip. AI one-liner below.
