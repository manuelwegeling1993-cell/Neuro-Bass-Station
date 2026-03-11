# NEURO BASS STATION

A browser-based neurofunk & DnB bass reference tool. Zero install — open the HTML file and play.

![screenshot placeholder](screenshot.png)

---

## What it does

**Neuro Bass Station** is a quick-inspiration tool for when you have writer's block or a spark of an idea on the go.  
It gives you 100 bass melodies × 80 rhythms × 17 breakbeat patterns, a synth engine, and a combiner — all in a single HTML file.

- **Audition** any melody or rhythm pattern with one click
- **Combine** a melody + rhythm + break into a full 2-bar neurofunk groove and preview it live
- **Randomise** individual columns or everything at once with the Neural Dice buttons
- **Export** your combination as a MIDI file or copy the pattern text to clipboard

---

## Controls

### Global bar (top)
| Control | What it does |
|---|---|
| **BPM** | 100–200 BPM, step 1 |
| **KEY** | Transpose root note chromatically (F to E) |
| **FILTER** | Global lowpass cutoff on the bass synth |
| **VOL** | Master volume |
| **SUB** | Sub-bass oscillator level |
| **DRIVE** | Toggles soft-clip waveshaper (adds harmonic distortion) |
| **SUB NUDGE** | Shifts rhythm hits ±ms relative to grid (lock sub to kick or push ahead) |
| **GROOVE NUDGE** | Shifts melody / mid-bass hits ±ms for feel (rush = aggression, late = funk) |
| **LOOP** | Toggle loop on/off |
| **▶ PLAY / ⏹ STOP** | Play the last-played pattern, or stop |

### Waveforms
`DUAL SAW` · `SAW` · `SQUARE` · `SINE` — sets the oscillator character of the synth voice.

### Keyboard shortcuts
| Key | Action |
|---|---|
| `Space` | Play / Stop |
| `←` / `→` | BPM −1 / +1 |
| `1` / `2` / `3` / `4` | Select waveform (Dual / Saw / Square / Sine) |
| `L` | Toggle Loop |

---

## Tabs

### MELODIES
100 bass patterns across 5 volumes:
- Vol 1 — Core neuro/funk phrases
- Vol 2 — Extended vocabulary
- Vol 3 — Modal (Dorian, Phrygian, Locrian)
- Vol 4 — Two-bass counterpoint ideas
- Vol 5 — Techstep & dark neuro

Click any card to audition. Search bar filters by title or description.

### RHYTHMS
80 patterns across 4 volumes:
- Vol 1 — Core grooves
- Vol 2 — Intermediate / syncopated
- Vol 3 — Polyrhythmic & Euclidean
- Vol 4 — Techstep mechanical patterns

### BREAKS
17 classic breakbeat reference patterns (Amen, Think, Apache, Funky Drummer…) shown at your current BPM with kick, snare, and hat rows. Click to audition the resynth.

### COMBINE
Pick one melody + one rhythm + one break. The engine maps pitch notes across rhythm hit positions to build a combined sequence. Preview live, export as MIDI or copy text.

- **⚄ RANDOMISE ALL** — random melody + rhythm (break excluded intentionally)
- Per-column **⚄** buttons randomise that column only
- Column headers and search bars are **sticky** — always visible while scrolling

---

## Audio engine

- Web Audio API lookahead scheduler (25ms window, 100ms ahead)
- 6-voice pool with smart voice stealing (prefers silent voices)
- Per-voice chain: `Oscillator → GainEnv → WaveShaper → Filter → MasterGain`
- Sub oscillator: separate sine at half frequency → `SubGain → MasterGain`
- Rhythm click: two-oscillator percussive transient (sine sub + square ring)
- Drive: sigmoid soft-clip curve via `WaveShaperNode` (toggled, not always on)

---

## Browser support

Requires **Web Audio API** — any modern browser works (Chrome, Firefox, Edge, Safari 14+).  
No server needed. Open `Neuro bass station.html` directly.

---

## Roadmap / ideas

- [ ] Upload your own MIDI for pitch or rhythm
- [ ] Per-voice oscillator GUI (tune, shape, detune)
- [ ] Save / share presets via URL hash
- [ ] PWA / offline support

---

## License

MIT — do whatever you want with it.
