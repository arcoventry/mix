# Changelog

All notable changes to the Song ↔ Exercise Sequence Planner are documented here.

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
