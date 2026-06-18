# Changelog

All notable changes to the Song ↔ Exercise Sequence Planner are documented here.

---

## [1.1.0] — 2026-06-18

### Overview
Adds named class slots, drag-to-reorder variations, and a redesigned mix UX that works from song lengths rather than overlap seconds. Also fixes a CSS regression that made variation name inputs invisible.

---

### New Features

#### Class Slots
- Two independent plan slots — **45-Minute Core** and **60-Minute Strength** — appear as pill tabs in the top bar
- Switching tabs auto-saves the current sequence into the departing class and loads the selected one
- Each class maintains its own completely independent song and posture sequence, persisted to `localStorage`
- The trash icon confirm prompt names the active class so it's clear which plan you're clearing

#### Drag-to-Reorder Variations
- Each variation row in the posture editor now has a `⠿` drag handle on the left
- Drag rows up or down to reorder; the target row highlights in blue on hover
- Variation color tints update immediately to reflect the new order after drop

#### Redesigned Mix UX — Mixed Length per Song
- Instead of entering an overlap duration, each song in a mixed pair now shows a **Mixed length** field
- **Song A (outgoing):** edit its mixed length down from its full duration — the delta is how much of Song A is cut by the crossfade
- **Song B (incoming):** edit its own mixed length independently — the delta is how much of Song B is trimmed at the front
- Savings badge (`−0:30`) on each input updates immediately; the connector bar between cards shows the combined total saved
- Both mixed lengths default to the song's full duration when mix is first enabled, so you only enter what you know
- Old `mix.amount` data from v1.0 is automatically migrated on first load

---

### Fixes

#### Variation Name Inputs Invisible (CSS Specificity)
- `.field input` (specificity 0,1,1) was overriding `.var-editor-name` (0,1,0), forcing `width: 100%` onto the duration input which has `flex-shrink: 0` — it grabbed the entire row width and squeezed the name input to zero
- Fixed by scoping both rules to `.var-editor-row .var-editor-name` / `.var-editor-row .var-editor-dur`, raising their specificity above the `.field input` rule

#### Mix Toggle Hard to See
- The toggle used `var(--text3)` (`#5a5f78`) text on a `var(--bg3)` (`#22263a`) background — near-invisible contrast on the dark theme
- Fixed: text color raised to `var(--text2)`, border raised to `var(--text3)`, active state gains a glow ring, and a `⇄` icon is always present via CSS `::before`

---

### Technical

- `state.classes` array and `state.activeClassId` added to the v2 schema; `save()` keeps the active class slot in sync on every write
- Mix data model changed from `mix: { enabled, amount }` to `mix: { enabled, outMixedLength, inMixedLength }`; automatic migration from `amount` on load
- Deployed to Vercel at `mix-ai.vercel.app` via GitHub integration on the `main` branch

---

## [1.0.0] — 2026-06-18

### Overview
First production release. A single-file HTML/CSS/JS app for planning exercise class sequences against a song playlist. No build step, no dependencies — open `planner.html` directly in any browser or VS Code.

---

### Features

#### Posture Library
- Create, edit, and delete **postures** — named exercise positions with a total duration and a color
- Assign a color from a 15-color palette per posture for visual identification throughout the app
- Postures persist to `localStorage` and survive page reloads

#### Variation Hierarchy
- Each posture can be broken into **variations** (e.g. Hold, Pulse, Large ROM, or any custom name)
- Variations are defined with individual durations; the sum of variation durations is enforced to never exceed the posture's total duration
- A live progress bar in the posture editor shows used vs. free time as you build out variations
- A **global variation type library** (`state.variationTypes`) stores every named variation type you've used, seeded with Hold, Pulse, Large ROM, Tempo, Flow, and Isometric — new names are added automatically as you create them

#### Song History
- Add, edit, and delete songs (title, artist, duration) to a persistent history
- Songs can be dragged from the history panel directly into the sequence, or added via the search modal

#### Sequence Builder
- Drag-and-drop song cards to reorder the class sequence
- Drag postures from the library panel onto song cards
- Per-song overflow detection: if the total posture time exceeds a song's duration, a red warning banner appears with a quick "**+ Song**" button to add a continuation song. Dropping a posture onto an already-full song is blocked with a toast.
- Remove postures or songs from the sequence individually or clear the entire plan

#### Mix System
- Each song (except the last) has a **"Mix with next"** toggle to indicate a crossfade with the following song
- Setting an overlap duration (e.g. `0:30`) shortens the combined effective time of the pair by half the overlap — both songs display their adjusted effective duration in amber
- Both songs in a mixed pair show active, clickable toggle badges — either song can remove the mix
- A visual **overlap bar connector** between mixed cards shows the overlap proportion and exact time saved
- The last song in the sequence cannot be set as outgoing mix

#### Timeline
- A color-coded proportional timeline bar at the bottom of the app shows the full class at a glance
- Hatched overlay on the mix region of outgoing songs
- Time markers at each song boundary
- Posture legend showing all posture types used in the current plan
- Header stats: total time, effective time (after mix savings), and song count

#### Variations Display on Song Cards
- Each posture assigned to a song card shows a **proportional color bar** (using graduated tints of the posture's base color per variation) plus a **named legend row** below it — variation names and durations are always readable regardless of segment width
- Unassigned time within a posture shows as a "free" entry in the legend

---

### Technical

- Single self-contained `planner.html` — no framework, no bundler, no network requests
- All state stored in `localStorage` under key `planner_v2`
- Automatic migration from the earlier `planner_state` (v1 blocks schema) to the v2 postures schema on first load
- `vercel.json` included for zero-config static deployment to Vercel

---

### Known Limitations

- No multi-device sync — data lives in the browser's `localStorage` only
- No export/import of plans
- No undo/redo
