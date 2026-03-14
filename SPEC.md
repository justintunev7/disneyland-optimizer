# Disneyland Day Optimizer — Spec

## Overview
A mobile-friendly single-page web app that acts as a live trip planner for Disneyland. Users update their status (what ride they just finished, where they are) and the app recommends what to do next based on live wait times and a pre-built plan.

## Tech
- **Single HTML file** with inline CSS/JS (no build step, deploy to GitHub Pages)
- **Mobile-first** responsive design — used on phones at the park
- **No backend** — everything client-side, fetches live data from queue-times.com API

## Data Source
- **Live wait times:** `https://queue-times.com/parks/16/queue_times.json` (free API, updates every 5 min)
- Attribution required: "Powered by Queue-Times.com" with link

## Core Features

### 1. Live Wait Time Dashboard
- Show all tracked rides with current wait times, auto-refreshing every 2 minutes
- Color-coded: green (≤15 min), yellow (16-30 min), orange (31-45 min), red (46+ min)
- Show if a ride is closed

### 2. Ride Tracking
- Checklist of all rides from the plan with 3 tiers (Must-Do, With LLMP, Fill-In)
- Tap to mark a ride as "Done" — it moves to completed section
- Tap to mark as "In Line" — starts tracking your current activity
- Tap to "Skip" — removes from recommendations

### 3. Smart "What's Next?" Recommendation Engine
When the user taps "What should we do next?", the app considers:
- **Current time of day** (maps to the plan phases)
- **Live wait times** (prefer shorter waits)
- **Ride priority tier** (Tier 1 > Tier 2 > Tier 3)
- **Which rides are done/skipped**
- **Rider Switch rides vs all-kids rides** (balance them)
- **Proximity** (suggest nearby rides to minimize walking)
- **LLMP status** (track if LLMP is available to book, suggest what to book)
- **Special time windows** (fireworks = ride now, afternoon = rest/shows)

The recommendation should show the top 3 options with reasoning.

### 4. LLMP Tracker
- Track which rides you've used LLMP on (1 per ride per day)
- Track when your next LLMP booking is available (after tap-in or 2-hour timer)
- Suggest which ride to book LLMP for next based on current waits and remaining must-dos

### 5. Dynamic Plan Adjustment
- If a ride goes down (closed), automatically adjust recommendations
- If wait times spike, suggest pivoting to lower-wait alternatives
- "Lunch break" and "Rest break" buttons that pause recommendations and suggest good times/places
- Manual override: user can say "we're going to X instead" and the plan adjusts

### 6. Time Tracker
- Show elapsed park time
- Estimated rides remaining based on pace
- "You're on track" / "You're ahead" / "You're behind" compared to plan

## Ride Data (baked in)

### Party Info
- 3 adults, 3 kids (ages 4, 4, 2)
- Height thresholds: 42" max for the 4-year-olds (configurable), 2-year-old needs Rider Switch on all height-req rides

### Ride Database
Each ride has:
- `id` — queue-times.com ride ID for live data matching
- `name` — display name
- `land` — which land it's in (for proximity)
- `heightReq` — minimum height in inches (0 = none)
- `hasLL` — whether it's on LLMP
- `tier` — 1 (must-do), 2 (with LLMP), 3 (fill-in)
- `riderSwitch` — whether the 2-year-old needs Rider Switch
- `avgDuration` — ride duration in minutes (for time planning)
- `notes` — tips (e.g., "do at night", "skip if >30 min wait")

### Proximity Map (simplified)
Group rides by land for proximity suggestions:
- Adventureland: Pirates, Indiana Jones (skip), Jungle Cruise, Tiki Room
- Frontierland: Big Thunder
- Critter Country / Bayou: Tiana's, Winnie the Pooh
- New Orleans Square: Haunted Mansion, Pirates
- Fantasyland: Peter Pan, Small World, Alice, Mr. Toad, Snow White, Dumbo, Carrousel, Storybook, Matterhorn
- Toontown: Gadget's, Roger Rabbit, Mickey & Minnie's Runaway Railway
- Tomorrowland: Space Mountain, Buzz, Star Tours, Astro Orbitor, Autopia
- Galaxy's Edge: Smugglers Run, Rise of Resistance (skip — $$ single pass)

## UI Design
- **Dark theme** (easier on eyes in bright sun with sunglasses)
- **Large touch targets** (using phone with one hand while holding a kid)
- **Sticky header** with: current time, rides completed count, next LLMP available time
- **Main section:** "What's Next?" recommendation cards
- **Tabs:** Dashboard (wait times) | Plan (checklist) | LLMP Tracker
- **Bottom action bar:** "Mark Done" | "In Line" | "Take a Break" | "Refresh Waits"

## Deployment
- Single `index.html` file
- Deploy to GitHub Pages at `justintunev7.github.io/disneyland-optimizer`
- Works offline after first load (service worker cache for the app, but live data needs connection)
