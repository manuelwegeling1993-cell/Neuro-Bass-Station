# Neuro Bass Station

**Single-file browser app** — everything lives in `Neuro bass station.html`.  
No build step, no framework, no server needed. Open the file directly in a browser.

---

## What It Is

A performance instrument for **neurofunk / techstep Drum & Bass** bass synthesis.  
It bridges a step sequencer and a standalone synthesizer — you pick a melodic motif and a rhythm, fuse them, and play or export the result.

---

## Architecture

### Audio Engine
- Web Audio API, no libraries
- Persistent voice pool (`POOL_SIZE = 6` oscillators)
- Lookahead scheduler (`LOOKAHEAD_MS = 25`, `SCHEDULE_AHEAD = 0.10s`)
- Waveform modes: `dual` (osc + sub), sawtooth, square, triangle
- Global controls: BPM slider, pitch/filter/volume/sub-volume, loop toggle (default ON)

### UI Structure
| Tab | ID | Content |
|---|---|---|
| Melody Vol 1–5 | `tab-pitch1` … `tab-pitch5` | `.pitch-grid` → `.card` items |
| Rhythm Vol 1–4 | `tab-rhythm1` … `tab-rhythm4` | `.rhythm-grid` → `.rcard` items |
| Breaks | `tab-breaks` | `.breaks-grid` → `.break-card` items |
| Combine | `tab-combine` | Three selector columns + result canvas |

### Card Structure (all tabs)
```html
<div class="card | rcard | break-card">
  <div class="play-indicator | rplay-indicator | break-play-ind"></div>
  <div class="progress-bar | rprogress-bar | break-prog"></div>
  <div class="card-header | rcard-header | break-hdr">…</div>
  <canvas class="waveform | rwave | break-canvas" width="600|640" height="150"></canvas>
  <div class="card-desc | rcard-desc | break-desc">…</div>
</div>
```
Canvas height is overridden to `82px` via CSS. Same markup is reused on the Combine page.

---

## Content Libraries

### Melodic Library — 100 motifs across 5 volumes
| Variable | Vol | Tag types |
|---|---|---|
| `PATTERNS1` | 1 | sub, stab, loop, atmo |
| `PATTERNS2` | 2 | sub, stab, loop, atmo |
| `PATTERNS3` | 3 | modal, chrom, seq |
| `PATTERNS4` | 4 | techstep, mech |
| `PATTERNS5` | 5 | techstep, mech, loop |

Each pattern: `{ title, tag, desc, notes[] }`  
Note helper: `n(interval, duration)` — intervals relative to root (e.g. `'1'`, `'b3'`, `'b5'`, `'b7'`, `'oct'`)  
Rest helper: `rest(duration)`

### Rhythmic Library — 80 patterns across 4 volumes
| Variable | Vol | Theme |
|---|---|---|
| `RHYTHMS1` | 1 – Core Grooves (01–20) | sub, break, stab, swing, fill |
| `RHYTHMS2` | 2 – Intermediate Bridge (21–40) | sub, break, stab, swing, fill, tri |
| `RHYTHMS3` | 3 – Advanced Poly & Euclidean (41–60) | poly, tri |
| `RHYTHMS4` | 4 – Techstep Rhythms (61–80) | mech |

Each rhythm: `{ title, tag, desc, steps[] }`  
Steps are velocity values: `1` = full hit, `0.5` = ghost note, `0` = rest.  
Standard patterns = **32 steps** (2 bars of 16th notes at 4/4).  
Vol 3 polyrhythmic patterns may be 40, 48, or 64 steps (intentional for tuplet cycles).

### Breaks Library — `BREAKS[]`
Each break: `{ name, orig (BPM), color, kick[], snare[], hat[] }`  
Used in the Breaks tab and as optional rhythmic layer in Combine.

---

## Combine Page

**Layout:** `.combine-layout` (flex column)
- `.combine-selectors` — 3 columns (flex, `justify-content: space-between`), each `width: 25%`
  - `.combine-col` × 3: SELECT MELODY / SELECT RHYTHM / SELECT BREAK
  - Each col: header + search input + scrollable `.combine-list`
  - `max-height: 540px` (~3 cards visible before scroll)
- `.combine-centre` — result area (centered, 50% width)
  - `#combine-canvas` (200px) — pitch curve of combined sequence
  - `#combine-rhythm-canvas` (60px) — rhythm grid of selected rhythm
  - Active selection labels, controls, export buttons

**Key JS functions:**
- `buildCombinePitchList(filter)` / `buildCombineRhythmList(filter)` / `buildCombineBreaksList(filter)`
- `buildCombinedSequence(pat, rhy)` — maps pitch notes onto rhythm hits (cycles pitch if shorter)
- `combinedEvents(pat, rhy)` — generates timed audio events
- `updateCombineCanvas()` — redraws result visuals
- `playCombined()` / `stopCombine()`

**Export:**
- `exportMidi()` — generates `.mid` file (pitch + rhythm combined); ppq = 480, ticksPerStep = 120
- `exportClip()` — renders audio to WAV via OfflineAudioContext

---

## Key CSS Variables
```css
--bg:      #060d14   /* page background */
--border:  #1a2530   /* dividers */
--dim:     #1e3040   /* subtle elements */
--accent:  #00c8ff   /* cyan highlight */
--accent2: #c07c00   /* gold/selected */
--accent3: #7060c0   /* purple */
```

**Button states:**
- `.btn.playing` — green `#40c040` (play active)
- `.btn.active-loop` — amber `#c8a000` (loop ON)

---

## Global State Variables
```js
let loopEnabled = true;     // loop is ON by default
let currentBpm = 172;
let activeCard = null;      // currently playing card { el, clockId, stopTo, progEl }
let lastPlayedFn = null;    // re-plays last card on global play button
let audioCtx, masterGain, subGain, filterNode;
let voicePool = [];         // 6 oscillator voices
```

---

## Known Design Decisions
- All card markup is identical between individual tabs and Combine page (same CSS classes, same canvas sizes)
- Breaks tab uses same 4-column grid as Melody/Rhythm tabs at `≥1200px`
- Ghost notes (vel = 0.5) use reduced gain and shorter envelope in `fireNote()`
- Vol 3 poly patterns with non-32 step counts are intentional (mathematical tuplet cycles)
