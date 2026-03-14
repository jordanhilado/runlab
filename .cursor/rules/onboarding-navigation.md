# Onboarding, Navigation & Profile

## Onboarding Flow

5-step linear flow after account creation. Progress bar at top, back button (except Step 1), "Skip for now" on optional steps.

Completing final step → `@AppStorage("hasCompletedOnboarding") = true` → main tab bar.

### Step 1 — Physical Profile
Height (ft/in or cm), Weight (lbs/kg), Date of Birth (wheel picker), Gender (pill: Male / Female / Non-binary / Prefer not to say).

### Step 2 — Running Profile & Goals
Fitness level (Beginner–Elite), Primary goal, Goal race distance (5K–Ultra/None), Goal time (H:MM:SS, conditional), Weekly mileage (slider 0–100+), Preferred training days (multi-select), Long run day (single-select within preferred), Units (Imperial/Metric), Coach style (Concise/Balanced/Detailed).

Zone auto-calculation runs silently after Step 2.

### Step 3 — Connect Strava *(optional)*
OAuth via `ASWebAuthenticationSession`. Success → checkmark animation, background sync. Skip available.

### Step 4 — Connect Apple Health *(optional)*
HealthKit permissions. v1.0 **read-only**. Reads: HRV, resting HR, sleep, VO2 max, weight, active energy.

### Step 5 — You're All Set
Summary of connected integrations, goal distance/time, fitness level. CTA: **"Start Training"**.

## Navigation

Five-tab `TabBar` (bgDark, no blur):

| Tab | Root View | Icon |
|-----|-----------|------|
| Home | `HomeView` | `house` |
| Training | `TrainingView` | `figure.run` |
| Coach | `CoachView` | `message.fill` |
| Analytics | `ActivityFeedView` | `chart.bar.fill` |
| Tools | `ToolsView` | `wrench.and.screwdriver.fill` |

- Active: white icon + label. Inactive: `textSecondary`.
- `NavigationStack` per tab. Sub-screens: `.navigationBarTitleDisplayMode(.inline)`. No large titles.
- Profile avatar (circular) top-left, contextual pill badge top-right.

Root logic: `OnboardingView` if `@AppStorage("hasCompletedOnboarding")` false, else tab bar.

## Profile Page

Accessible from top-left avatar. Collapsible sections:

**Personal Info:** Name, photo, DOB, gender, height, weight (unit toggle).

**Training Zones:** HR Zones (HRmax, LTHR, Z1–Z5 — editable, source-labeled), Pace Zones (T-pace, Z1–Z6), Zone History log, "Recalculate from profile" button.

**Running Goals:** Primary goal, distance, time, fitness level, training days, long run day, weekly mileage.

**App Preferences:** Units, coach style, notifications.

**Integrations:** Strava (status, sync, disconnect), Apple Health (per-type status, Settings deep link).

**Account:** Email, change password, manage subscription, delete account (irreversible).

## Information Architecture

```
Profile (avatar top-left)
├── Personal Info
├── Training Zones (HR, Pace, History)
├── Running Goals
├── App Preferences
├── Integrations (Strava, Apple Health)
└── Account

TabBar
├── Home (PMC, today's workout, coach nudge, recent activity, health)
├── Training (active plan calendar, plan library, commitment flow)
├── Coach (chat, suggested prompts, insights)
├── Analytics (feed, detail timeline, pace/HR zones, map/elevation)
└── Tools (Race Predictor, Pace Calculator, Split Planner)
```
