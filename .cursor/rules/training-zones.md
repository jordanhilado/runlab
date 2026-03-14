# Zone Auto-Calculation

Runs silently after onboarding Step 2. Values stored in `AthleteProfile`, recalculated on profile updates. Manual override from Profile page.

## Max HR (Tanaka)

`HRmax = 208 − (0.7 × age)`

## Resting HR

Apple Health 7-day avg of `HKQuantityTypeIdentifierRestingHeartRate`, or default **60 bpm**.

## Lactate Threshold HR (LTHR)

| Fitness Level | LTHR (% of HRmax) |
|---------------|-------------------|
| Beginner | 82% |
| Recreational | 84% |
| Intermediate | 86% |
| Advanced | 88% |
| Elite | 90% |

`LTHR = round(HRmax × LTHR_pct)`

## Threshold Pace (T-pace)

**With goal time** — VDOT lookup:

| Distance | T-pace multiplier from race pace |
|----------|--------------------------------|
| 5K | race pace × 1.13 |
| 10K | race pace × 1.06 |
| Half Marathon | race pace × 1.02 |
| Marathon | race pace × 0.94 |
| Ultra | Falls back to fitness level default |

**Without goal time** — estimated from fitness level (midpoint used):

| Fitness Level | Default T-pace (min/mi) | Default T-pace (min/km) |
|---------------|------------------------|------------------------|
| Beginner | 11:00–13:00 | 6:50–8:05 |
| Recreational | 9:00–11:00 | 5:35–6:50 |
| Intermediate | 7:30–9:00 | 4:40–5:35 |
| Advanced | 6:15–7:30 | 3:53–4:40 |
| Elite | < 6:15 | < 3:53 |

## HR Zones (5 Zones, anchored to HRmax)

| Zone | Label | % of HRmax |
|------|-------|-----------|
| Z1 | Recovery | < 68% |
| Z2 | Aerobic Base | 68–83% |
| Z3 | Tempo | 84–93% |
| Z4 | Threshold | 94–98% |
| Z5 | VO2 Max | ≥ 99% |

## Pace Zones (6 Zones, anchored to T-pace)

| Zone | Label | T-pace multiplier |
|------|-------|------------------|
| Z1 | Easy / Recovery | ≥ 1.29× |
| Z2 | Aerobic / Marathon | 1.15–1.29× |
| Z3 | Threshold / Tempo | 1.00–1.15× |
| Z4 | Interval (VO2 Max) | 0.93–1.00× |
| Z5 | Speed / Repetition | 0.88–0.93× |
| Z6 | Sprint / Anaerobic | < 0.88× |

## Zone Recalculation Triggers

Recalculate (with user confirmation) when:
- Age changes (birthday)
- Apple Health provides new observed HRmax
- User updates goal time or fitness level
- User logs a race or time trial
- Synced Strava activity contains new all-time max HR

Historical zone distributions are **not retroactively recalculated**. Change date and old/new values recorded in zone history log.
