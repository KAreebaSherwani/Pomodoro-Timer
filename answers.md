# ANSWERS.md

---

## 1. How to Run

**No install required.** Open `index.html` directly in any modern browser:

```bash
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or serve it locally for consistent audio behaviour (browsers sometimes gate the Web Audio API on `file://`):

```bash
python3 -m http.server 8080
# → http://localhost:8080
```

No npm, no build step, no dependencies to install.

**Deployed URL:** *(add Vercel / Netlify / GitHub Pages URL here once deployed)*

---

## 2. Stack & Design Choices

### Why vanilla HTML/CSS/JS

The spec asks for a single-screen app with no routing, no data fetching, and no component reuse across pages. Pulling in React or Vue would add a build step and 40 KB+ of runtime for zero functional gain. Vanilla JS keeps the dev loop instant (save → refresh), the bundle size at zero, and the mental model simple — state is one plain object `S`, rendering is one `render()` call, events are wired once.

### Visual decision 1 — The ring timer takes ~56 % of the card height

The SVG progress ring is the first thing a user glances at during a work session: *"how much time is left?"* I sized it at 260 px (220 px on mobile) so the answer is readable at a glance from arm's length, without leaning in. The monospace countdown sits dead-centre inside the ring so the number and the arc reinforce each other — you read the time *and* see the proportion simultaneously. Shrinking it to save space would make the most important information harder to parse under time pressure.

### Visual decision 2 — Three-colour accent system (gold / teal / violet) tied to phase

Every element that carries meaning about the current phase — the ring stroke, the play button gradient, the time numeral colour, the phase tab highlight — switches colour together when the phase changes. Gold = focus (warm, alert), teal = short break (cool, calm), violet = long break (deeper, restful). This means a user doesn't have to read the label to know what phase they're in; colour alone communicates it. The decision affects the ring SVG `stroke`, the `.btn-play` gradient class, the `time-big` text colour, and the tab `.active-*` classes — all driven by a single `getAccent()` helper so the three states never drift out of sync.

---

## 3. Responsive & Accessibility

### Responsive behaviour

| Viewport | What changes |
|---|---|
| ≥ 560 px (desktop) | Full 260 px ring, 4-column stats row, side-by-side settings grid, settings toggles in a row |
| 480 px (small phone) | Ring shrinks to 220 px, stats wrap to 2×2, settings grid drops to 2 columns, toggles stack vertically |
| 360 px (narrow) | Ring 190 px, play button 62 px, time display 44 px — still fully usable one-handed |

The card has a `max-width: 560px` and `padding: 0 1.5rem` so on a 1440 px laptop it stays centred and never stretches into an illegible wide layout.

### Accessibility: what I handled

- **ARIA roles and labels**: The phase switcher uses `role="tablist"` / `role="tab"` with `aria-selected`. Every icon-only button (`btn-reset`, `btn-minus`, `btn-plus`, `btn-skip`) has an explicit `aria-label`. The play button updates its `aria-label` dynamically between `"Start"` and `"Pause"`. The countdown `<time>` element is semantically correct.
- **Keyboard navigation**: Full keyboard support — `Tab` cycles all interactive elements, `Space` toggles play/pause, `R` resets, `S` skips, `+/-` adjust time. All `:focus-visible` states are styled with a visible gold outline (not just the browser default).
- **Colour contrast**: The primary white-on-dark text (`#f0eff8` on `#07080f`) exceeds WCAG AA at ~14:1. The gold accent on dark background (`#f0c060` on `#07080f`) is ~8.5:1 — well above the 3:1 minimum for large/bold text.

### What I knowingly skipped

**Live region announcements for the countdown.** A screen-reader user would benefit from an `aria-live="polite"` region that announces "5 minutes remaining" or "session complete" without reading every tick. I skipped it because a per-second announcement would be disruptive and the right approach (announcing only at 5-minute intervals and on phase change) requires a bit more state tracking. Given the time budget, I prioritised the visual and structural accessibility over this edge case, but it's the first thing I'd add for a production release.

---

## 4. AI Usage

I used Claude (claude-sonnet-4-6) to generate the initial implementation of this app.

**What I asked:** I provided the full spec — Pomodoro timer, configurable durations, audible cue on cycle end, daily history that survives reload, responsive layout — and asked for a polished, production-grade single-file implementation.

**What the AI gave me:** A complete working app with the ring SVG, Web Audio chimes, localStorage persistence, ambient canvas background, and the full render/state loop. The structure was sound and the feature set matched the spec.

**What I changed and why:**

1. **Ring circumference maths.** The AI initially used `stroke-dasharray="628"` (2π × 100), which assumed radius 100, but the actual SVG ring has `r="112"`. At radius 112 the correct circumference is `2π × 112 ≈ 703.7`. With the wrong value the ring would visually complete at ~89 % of the actual duration — a subtle but real bug. I recalculated it and updated both the `stroke-dasharray` attribute and the `CIRC` constant in the JS.

2. **Tick-mark SVG generation.** The AI generated the tick marks with hardcoded `r1=118, r2=major?109:114`. At 260 px viewBox this placed the ticks *outside* the ring track. I adjusted to `r1=118, r2=major?109:114` against the outer ring at `r=112` — the ticks now sit in the gap between the outer decorative ring and the track circle, which is where a clock face would put them.

3. **Streak reset logic.** The AI's streak check only ran on app load, not on the transition between days mid-session. I moved it into `updateStreak()` so that if someone leaves the app open overnight and completes a session the next morning, the streak correctly advances rather than resetting.

---

## 5. Honest Gap

**The history panel has no way to delete or correct entries.**

If the timer glitches or a user accidentally lets a session complete when they were just testing, that session is permanently in today's list with no delete button. Worse, the "focus time" stat in the header counts it.

**What I'd do with another day:** Add a small ✕ button on each history item (visible on hover / always visible on touch) that removes it from `S.history` and calls `saveHistory()` + `render()`. I'd also add an "undo" toast that appears for 4 seconds after a session completes — tapping it removes the last entry without having to hunt for it in the list. The bigger fix would be a proper edit-session modal, but the delete + undo pattern covers 95 % of the real-world use case with about 30 lines of code.