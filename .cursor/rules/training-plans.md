# Training Plans

## Plan Library

MVP ships 12 plans — 4 distances × 3 levels. Hybrid: fixed weekly workout-type template scheduled dynamically backwards from race date. Mileage seeds from current weekly mileage, builds ~10%/week until peak (3 weeks pre-race), then tapers.

| Plan | Distance | Level | Duration | Runs/Week |
|------|----------|-------|----------|-----------|
| 5K — Beginner | 5K | Beginner | 8 weeks | 3 |
| 5K — Intermediate | 5K | Intermediate | 8 weeks | 4 |
| 5K — Advanced | 5K | Advanced | 8 weeks | 5 |
| 10K — Beginner | 10K | Beginner | 10 weeks | 3 |
| 10K — Intermediate | 10K | Intermediate | 10 weeks | 4 |
| 10K — Advanced | 10K | Advanced | 10 weeks | 5 |
| Half Marathon — Beginner | HM | Beginner | 12 weeks | 3 |
| Half Marathon — Intermediate | HM | Intermediate | 12 weeks | 4 |
| Half Marathon — Advanced | HM | Advanced | 12 weeks | 5 |
| Marathon — Beginner | Marathon | Beginner | 16 weeks | 4 |
| Marathon — Intermediate | Marathon | Intermediate | 16 weeks | 5 |
| Marathon — Advanced | Marathon | Advanced | 16 weeks | 6 |

**Catalog:** Static data in `PlanCatalog.swift` (Swift structs, no network call).

**Methodologies:** Beginner = Hal Higdon base building. Intermediate = 80/20 polarized. Advanced = Daniels Running Formula periodization.

## Distance Allocation Per Session Type

| Session Type | % of Weekly Mileage | Pace Target |
|-------------|---------------------|-------------|
| Easy Run | 22–25% | Z1–Z2 |
| Tempo Run | 18–20% | Z3 |
| Intervals | 12–15% (incl. warm-up/cool-down) | Z4–Z5 |
| Medium Long Run | 25–28% | Z2 |
| Long Run | 30–35% | Z1–Z2 |

## Schedule Generation (at commit time)

1. Anchor race day to user's goal race date (always Sunday in template)
2. Count backwards in full weeks for plan duration
3. If today is mid-week, current partial week becomes Week 1
4. Weekly mileage: seed → +10%/week until peak (N−3), taper: −20% (N−2), −40% (N−1), Race Week = shakeout + race
5. Preferred training days and long run day respected
6. Sessions written as `PlannedWorkout` records in SwiftData

## Commitment Flow

Required inputs: **Race location** (display only), **Race date**, **Goal time**.

Validation:
- Race date ≥ full plan duration away
- If farther, Week 1 starts exactly `plan_duration` before race day
- Preferred training days ≥ plan's runs/week
- Long run day must be a preferred training day
- One workout per day in MVP

Preview shown before confirming with "Start Plan".

## Active Plan Management

- Reschedule future workouts (long-press drag or "Edit")
- Edit future workout details (distance, type, pace target)
- Manually mark complete (treadmill/watch-only)
- Completed past workouts locked
- Abandon plan with confirmation (irreversible)
- One active plan at a time

## Auto-Completion Matching

Synced run satisfies planned workout when: (1) same calendar day, (2) distance ±10% of planned, (3) dominant pace zone matches type (Easy = Z1–Z2, Tempo = Z3, Intervals = Z4–Z5, Long Run = Z1–Z2). Day+type match but missing distance = **Partial**.

## Plan Completion Badge

All workouts completed (or race date passes with plan satisfied) → badge on profile, specific to distance + level.
