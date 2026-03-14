# Backend & Services

## Authentication

Supabase Auth with three methods:
1. **Sign in with Apple** (required for App Store)
2. **Sign in with Google** (OAuth 2.0)
3. **Email & Password** (with email verification)

Supabase JWT as `Bearer` token for all backend calls. Backend validates server-side.

## Backend Stack

Node.js (TypeScript) + Express on Railway. All endpoints require Supabase JWT except Strava webhook.

### Core MVP Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/strava/webhook` | Strava webhook receiver (no auth) |
| `POST` | `/strava/exchange` | Exchange Strava auth code for tokens |
| `GET` | `/activities/sync` | Trigger manual Strava sync |
| `GET` | `/activities/:stravaId/markdown` | Fetch pre-rendered markdown |
| `POST` | `/coach/chat` | Stream AI Coach response via SSE |
| `GET` | `/athlete/profile` | Fetch athlete profile snapshot |
| `PUT` | `/athlete/profile` | Update athlete profile snapshot |
| `POST` | `/subscriptions/validate` | Validate StoreKit 2 receipt |
| `DELETE` | `/account` | Delete user data from Supabase |

### Backend Base URL

From `Info.plist` key `BACKEND_BASE_URL`, fallback `http://localhost:3000`.

## Supabase Schema

All tables use RLS — `auth.uid() = user_id`.

| Table | Key Columns | Description |
|-------|-------------|-------------|
| `strava_connections` | `user_id` (PK), `athlete_id`, `access_token_encrypted`, `refresh_token_encrypted`, `expires_at` | Strava tokens per user |
| `activity_markdowns` | `id` (PK), `user_id`, `strava_activity_id` (unique), `markdown`, `generated_at` | One markdown per activity |
| `athlete_profiles` | `id` (PK = `auth.uid()`), `profile_json`, `zones_json`, `rolling_summary_json`, `updated_at` | Profile, zones, coach context |

MVP server state minimal. Streams, health, plans, workouts, coach messages stay in SwiftData on-device.

## Strava OAuth

- Client ID / Secret from `Info.plist` (`STRAVA_CLIENT_ID` / `STRAVA_CLIENT_SECRET`)
- Redirect: `runlab://strava-callback`
- Scope: `activity:read_all,read`
- Tokens in Keychain; auto-refresh within 60s of expiry

## AI Coach

App **never calls Anthropic API directly**. All requests → `POST /coach/chat` → backend proxies to Claude, streams SSE back.

System prompt (in `AICoachService`):
> "You are Runlab Coach, a personal running coach with deep expertise in exercise physiology, training theory, and data analytics. You have access to the athlete's actual training data. Give specific, data-grounded answers. Be concise. Use metric or imperial based on the athlete's preference."

**Auto-injected context:** athlete profile, training zones, fitness metrics (CTL/ATL/TSB), activity totals, recent digests, latest health snapshot.

**Activity context:** Users attach via "+" button → pre-rendered markdown (not raw JSON) injected.

**Suggested prompts:** 6 tappable chips in horizontal scroll when history empty. Tap populates input (no auto-send).

## Activity Markdown System

Each Strava activity → ~400–700 token markdown document in `activity_markdowns`.

**Pipeline:** Webhook → fetch detail + streams → compute metrics → write markdown → upsert → discard raw streams.

**Template sections:** Overview, Performance Metrics (NGP/IF/rTSS/VI/EF), Aerobic Decoupling, Pace Zone Distribution, HR Zone Distribution, Auto-Detected Intervals, Environmental Conditions, Route, Coach Notes.

Full template in `blueprint/vision.md` (§ Activity Markdown System).

### Coach Notes Rule Logic

Rules evaluated in order; first 2–3 that trigger included:

| Priority | Condition | Summary |
|----------|-----------|---------|
| 1 | `pa_hr_decoupling > 5%` | Aerobic drift — suggest slowing |
| 2 | `pa_hr_decoupling ≤ 5%` and easy run | Well-coupled effort |
| 3 | Intervals within ±5% of target | Intervals executed cleanly |
| 4 | >50% reps faster than target | Intervals too hard |
| 5 | EF > 30d avg × 1.05 | Aerobic fitness improving |
| 6 | Avg HR > LTHR on easy run | Harder than intended |
| 7 | Max HR > stored HRmax | New max HR recorded |
| 8 | Default | Session load summary |

Markdown stored in `RunActivity.markdownSummary` for offline access.
