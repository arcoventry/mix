# Roadmap

Planned features and enhancements for the Song ↔ Exercise Sequence Planner.

---

## P0 — Must Have

### PDF Export
- Print or save a class plan as a formatted PDF — song order, posture assignments, mix notes, and timeline
- Useful for bringing to the bike, sharing with a sub instructor, or keeping class records

---

## P1 — High Value

### BPM / Energy Stored on Songs
- BPM and Energy are already parsed from Exportify CSVs but not yet saved to the song schema
- Would unlock BPM display on song cards and energy-arc visualization across the class

### BPM Transition Warnings
- Flag adjacent songs where the tempo jump is large (e.g. >8 BPM)
- Helps sequence smoother DJ-style transitions between songs

### Export / Import Plans as JSON
- Back up and restore a full class plan (songs + postures + sequence) as a file
- Essential for multi-device use and sharing plans with other instructors

---

## P2 — Nice to Have

### Posture Import / Presets
- Load a posture library from a file or choose from built-in templates (e.g. Spin, HIIT, Yoga)
- Mirrors what "Add Playlist" does for songs

### Energy Arc Visualization
- Color-coded energy curve across the timeline so you can see warm-up → peak → cooldown at a glance

### Duplicate / Copy a Class
- Clone a class slot so you can iterate on a plan without starting from scratch

### Reorder and Rename Class Slots
- Drag the class tab pills to change their order
- Rename slots to something custom beyond "45-Minute Core" / "60-Minute Strength"

---

## P3 — Exploratory

### Multi-Device Sync
- Replace `localStorage` with a cloud backend (e.g. Supabase) so plans persist across devices

### Undo / Redo
- Ctrl+Z support for sequence edits

### Shareable Class Links
- Generate a read-only URL for a class plan to share with students or co-instructors
