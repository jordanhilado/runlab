# Monetization & Roadmap

## StoreKit 2 Subscription

| Product ID | Price |
|------------|-------|
| `runlab.pro.monthly` | $9.99/month |
| `runlab.pro.annual` | $59.99/year |

**Free tier:** 60 days activity history, 3 AI Coach messages/day, basic training status, 1 active plan, 3 tools.

**Runlab Pro:** Unlimited history, full AI Coach, all tools, unlimited plan history, advanced health correlations, export (CSV/JSON), priority support.

One active plan at a time for all users. Subscription gates: history depth, AI usage, tools, exports, advanced analytics.

7-day free trial. Annual plan prominently featured (~50% savings).

Pro gates: `SubscriptionService.isProUser` + `.proGate(feature:)` ViewModifier.

## Roadmap

### v1.0 — MVP

Auth, onboarding, profile, subscription. Strava sync, Apple Health read-only, SwiftData persistence. Home (PMC, today's workout, recent activity, health). Training (12 plans, commitment, calendar, manual/reschedule). Coach (chat, prompts, activity chips). Analytics (feed, detail timeline, pace/HR zones, map). Tools (Pace Calc, Race Predictor, Split Planner). Zone auto-calc. Dark-only UI. StoreKit 2.

**Not in v1.0:** Decoupling, weather, intervals table, comparison, heatmaps, custom charts, power rankings, grid management, proactive insights, advanced wellness. No Watch, WorkoutKit, Siri, Garmin/Wahoo, social, auto-adaptive plans.

### v1.1 — Analytical Depth

Intervals table, aerobic decoupling (Pa:HR + Seiler), cumulative HR, HR duration curve, NGP/IF/rTSS/VI/EF, 42-day curve, weather overlay, activity comparison, time period comparison, totals, fitness classification, expanded wellness, grid view, race countdown pill, PR detection, proactive insights, weekly/monthly reports, additional tools.

### v1.2 — Advanced Analytics & Coaching Intelligence

Heatmaps, histograms, EF trend, health correlation, GPS/HR correction, power zones, multi-season power curve, power rankings, custom charts, AI plan adaptation, injury risk, race strategy, coach history, Siri Shortcuts.

### v2.0 — Platform Expansion

Apple Watch, iPad layout, social, coach-athlete mode, Garmin Connect, Wahoo/TrainingPeaks import.
