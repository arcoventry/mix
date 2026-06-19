# Changelog

All notable changes to the Song ↔ Exercise Sequence Planner are documented here.

---

## [2.2.0] — 2026-06-19

### Overview
Adds **PDF export** of a finished class schedule. Build your plan, click **Export PDF** in the top-right, fill in the instructor/class/date, and generate a clean printable handout straight from the browser — no libraries, no upload, fully offline.

---

### New: PDF Class Schedule Export

- **Export PDF button** in the top-right of the app, fixed to the corner so it's always reachable regardless of how many classes or stats fill the top bar
- **Export dialog** captures the **Instructor Name**, **Class Name** (prefilled from the active class), and **Class Date**; the instructor name is remembered for next time
- The generated document leads with a **full class timeline** — song segments, posture bars flowing across song boundaries, crossfade-mixed regions, and a time axis — followed by a **posture color legend**
- **Per-song breakdown** maps the final posture layout against each song: time range, a per-song bar, and each posture with its proportional variation divisions
- **Crossfade mixes are called out** where applicable, and postures that carry across a song boundary are noted
- Renders through the browser's native print path (**Save as PDF**), reusing the same timeline/color engine as the app so the export can never disagree with what's on screen
- Contains no third-party or corporate branding — just your class

---



### Overview
A full visual refresh. The app moves from a dark navy/blue developer theme to a clean light "Daylight" palette designed as one matched set, and the variation color system was reworked so its divisions stay crisp on any palette. Existing plans load unchanged.

---

### Changes

#### New "Daylight" Light Theme
- Replaced the dark navy-black chrome with a warm light/paper theme — off-white background, white panels, dark warm text
- Refit every dark-theme-only treatment for light backgrounds: timeline segment fills, song-number labels, mix hatches, the variation-chip badges, drag-over highlights, and the color-picker selection ring

#### Palette-Matched Accent
- The accent is now the **steel-blue** (`#7e95c4`) pulled directly from the posture color set, so the app chrome belongs to the same family as the posture bars instead of clashing with them
- (Previously a generic indigo that read as a default/template color)

#### Harmonized, Hex-Ordered Posture Colors
- The 15 posture swatches are a cohesive soft-pastel set, and are now ordered by hex value so the color picker reads as a smooth gradient rather than a random scatter

---

### Fixes

#### Variation Divisions Invisible on the Light Palette
- After the palette change, the proportional variation bars stopped showing their internal divisions — adjacent variation tints were washing into one another
- Root cause: variation tints were derived by *only lightening the base color toward white*. That stepped cleanly on dark saturated colors, but on light pastels every tint collapsed toward near-white
- Reworked the tint generator to step through evenly-spaced **HSL lightness bands** on the base hue, so divisions stay distinct for any base color — light or dark. Added a hairline divider between segments on the card bars, and fixed a library variation-chip tint that was emitting invalid CSS
- Now correct everywhere variations are drawn: posture cards, legend dots, per-card song-time bars, the bottom timeline, the modal editor, and the timeline legend

#### Timeline Boundary Lines Floated as Partial-Height Streaks
- Song-boundary tick lines stopped short of the bottom of the timeline track, leaving dark vertical streaks hovering in the upper portion — especially visible on the light theme. They now run full-height as single clean, subtle dividers

---

## [2.0.1] — 2026-06-18

### Overview
Bug-fix release. Fixes posture scrambling when songs are reordered. The cross-song flow model introduced in v2.0 now recalculates cleanly every time the playlist order changes.

---

### Fixes

#### Postures Scrambled When Reordering Songs
- Reordering songs could chaotically concentrate postures into the wrong songs, with the result differing each time the same reorder was repeated
- Root cause: `redistributePostures()` used a `Math.max(si, fromSong)` guard intended to keep a posture from being pulled into an earlier song. But it **permanently mutated** each song's `postures` array on every render. After a reorder, postures were stranded in the old songs' arrays while the guard evaluated them against the *new* song positions — so each pass scrambled the layout further
- Fixed with a **flow & repack** model: all postures across all songs are flattened into a single ordered routine, then re-flowed into song buckets purely by effective time windows. Posture **order** is now the single source of truth; which song a posture starts in is always *derived* from that order against the current song layout, never stored as ground truth between renders
- Result: reordering songs re-flows the whole routine cleanly from the start, every time. The operation is idempotent (running it twice never produces a second change) and conserves every posture (none lost or duplicated) — both verified across 20,000 randomized fuzz scenarios

---

## [2.0.0] — 2026-06-18

### Overview
**Postures now flow across your whole class.** This release redefines the core model: songs are no longer hard containers that each posture must fit inside. Instead, the playlist is a continuous backdrop, and postures flow freely across song boundaries — exactly like a real class, where a single position can carry over from one track into the next. Existing plans load unchanged; nothing in your saved data needs to migrate.

---

### The New Model — Postures Flow Across Songs

#### Cross-Song Posture Flow
- A posture is no longer locked to one song. If a 5-minute posture is placed in a 4-minute song, the remaining minute now flows into the next song automatically
- The continuation is called out visually rather than blocked — songs are a backdrop, not a cage
- Carry-over math accounts for mixed song lengths: when a song is shortened by a crossfade, the posture flow uses the *effective* (mixed) duration, not the raw track length
- A warning only appears when overflow has nowhere to go — i.e. the **last** song in the plan is exceeded — alongside a quick **+ Song** button to extend the class

#### Auto-Reorder — No More Dumping Everything Into Song 1
- Drop a posture into an already-full song and it automatically bumps down to the next song that has room — the card physically moves to where the posture actually starts playing
- A posture keeps its home song as long as it *starts* there, so legitimate single-posture overflow across a boundary still works
- The move is one-directional: postures are only ever bumped **down**, never pulled up into an earlier song, so an intentional drag into a later song is always respected
- The drop toast reports where the posture landed, e.g. *"🔁 Standing Climb bumped to 'Track 2' — song 1 was full"*

---

### New Features

#### Drag Postures Between and Within Songs
- Every posture row now has a `⠿` grab handle
- Drag a row up or down to reorder it within the same song — a blue insert line shows where it will land
- Drag a row onto another song to move the posture there — drop it before a specific row, or onto the card body to append to the end
- The target song highlights green to confirm a valid drop

#### Per-Card Song Time Bar
- Each song card now shows a proportional **class-time bar**, mirroring the bottom timeline
- Hatched segment at the left edge = a posture carrying in from the previous song
- Solid segments = postures starting within this song, tinted by their variation colors
- A glowing right edge = a posture that continues into the next song
- Hover any segment for the posture name, its clipped duration, and carry-in / overflow flags

---

### Changes

#### Two-Layer Timeline Redesign
- The bottom timeline was rebuilt as two layers: a dim song-background layer and a layer of absolutely-positioned posture bars that span song boundaries freely
- Posture bars are placed at their true global class-time positions, so a posture crossing from one song into the next is drawn as a single continuous bar
- Each bar is sub-divided into graduated variation tints; song boundary tick-lines and a per-variation color legend round it out

#### Version Badge
- A small `v2.0` badge now sits in the top bar next to the app title

---

### Technical
- New `redistributePostures()` normalization pass runs at the top of `renderSequence()` and persists whenever a posture's home song changes, keeping the data model in sync with the visual flow
- `computePostureEvents()` extracted as a single shared computation powering both the card time bars and the timeline, so the two can never disagree
- `computeCarryOver()` reworked to propagate overflow between songs using effective mixed durations

---

## [1.2.0] — 2026-06-18

### Overview
Adds CSV playlist import from Exportify and removes all seed data so new users start with a clean slate.

---

### New Features

#### CSV Playlist Import ("Add Playlist")
- A new **+ Add Playlist** button sits alongside **+ Add Song** in the Songs panel
- Opens a modal where you can drag-and-drop or click to select an Exportify `.csv` file
- Parses Exportify columns automatically: Track Name, Artist Name(s), Duration (ms), Tempo, Energy
- Shows a preview list of all tracks before committing — each row displays title, artist, duration, and a `⇄ mix` badge for any song detected as a continuous/DJ mix
- Mix detection: flags tracks containing "mixed" or "continuous mix" in the title, but correctly excludes tracks that are just remixes (contains "remix")
- Summary line reports track count, how many were detected as mixed, and whether BPM/Energy data is present in the CSV
- **Replace library** checkbox (checked by default) clears existing songs before import; uncheck to append
- Duplicate detection: songs already in the library (same title + artist) are skipped, not doubled

---

### Changes

#### Clean First-Run Slate
- Removed all seven default seed songs from the song library
- Removed all seven default seed postures (Warm-Up, Seated Climb, Standing Climb, etc.)
- New users now open the app to an empty library and build from scratch

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
