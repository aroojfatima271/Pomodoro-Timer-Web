# ANSWERS.md

## 1. How to Run

No installation needed. The app is a single `index.html` file with zero dependencies.

**Quickest way:**
```bash
open index.html
```

Or serve it locally:
```bash
npx serve .
# open http://localhost:3000
```
Deployment URL:
https://clever-jalebi-f8455e.netlify.app/
There is no build step. No `npm install`. Works in any modern browser.

---

## 2. Stack & Design Choices

**Stack:** Vanilla HTML, CSS, and JavaScript — no framework, no bundler.

I chose this because a Pomodoro timer is fundamentally a single-screen, stateful UI. Adding React or Vue would introduce build complexity for no real gain. A well-structured single HTML file is easier to audit, faster to load, and simpler to deploy (just `open index.html`).

**Design Decision 1 — Circular ring progress instead of a progress bar**

A radial ring fills the entire visual center of the screen and makes the timer *feel* alive. The ring's stroke uses `stroke-dashoffset` animated via CSS `transition: stroke-dashoffset 1s linear`, which gives a smooth per-second tick without JavaScript-driven repaints. I sized it at `min(62vw, 320px)` so it takes up roughly 60% of the viewport width on mobile — large enough to read at a glance, not so large it crowds the controls below. The ring color changes from amber (focus) to blue (break) so the current mode is legible without reading any text.

**Design Decision 2 — Session-done overlay instead of a browser alert**

When a cycle completes, I show a full-screen overlay card with a satisfying heading ("🎯 Focus Complete!"), a short subtitle, and a single "Continue" button. I deliberately chose this over `window.alert()` because alerts block the thread and look ugly, and over a small toast because the cycle boundary is a *major moment* that deserves full visual weight. The overlay also gives users a moment to mentally transition between modes. The overlay is CSS-only animation (`opacity` + `scale` transition), so no animation library is needed.

---

## 3. Responsive & Accessibility

**Responsive behaviour:**

At 360px (narrow phone), the ring shrinks to `62vw` (~223px), the font scales via `clamp()`, and the controls stack naturally in a single row — nothing overflows. The settings inputs are 56px wide and finger-friendly. At 1440px (wide desktop), the ring caps at 320px and the whole layout is centred with comfortable breathing room. I tested both by resizing the browser window and with Chrome DevTools device emulation.

**Accessibility consideration I handled:**

All interactive controls are native `<button>` and `<input type="number">` elements, so they are keyboard-navigable and screen-reader announced by default. The timer label (`FOCUS`, `BREAK`, `PAUSED`) is always a visible text element — not only conveyed by color — so color-blind users are not disadvantaged. Button text updates to reflect state (`Start` → `Pause` → `Resume`) which means screen readers announce the current action correctly.

**Accessibility consideration I knowingly skipped:**

I did not add an ARIA live region (`aria-live="polite"`) for the countdown itself. Announcing every second to a screen reader would be extremely disruptive (imagine hearing "24:59", "24:58", "24:57"…). The right fix would be to announce only on meaningful boundaries — e.g. at 5 minutes remaining, at 1 minute remaining, and on cycle completion. I skipped this because implementing it well (avoiding double-announcements, handling tab-away correctly) would have taken a meaningful chunk of time, and the cycle-complete overlay does get announced natively since it becomes the focused element.

---

## 4. AI Usage

**Tool used:** Claude (Anthropic)

**What I asked:**
I used Claude to generate the initial full implementation of this app, including the ring SVG animation, the localStorage history logic with date-rollover, and the Web Audio API chime.

**What I changed and why:**

1. **Ring sizing** — The AI initially used a fixed `300px` ring. I changed it to `min(62vw, 320px)` using a CSS variable so it scales fluidly on narrow phones without overflowing. On a 360px screen the fixed 300px ring was cutting off at the edges.

2. **Audio chime** — The AI generated a single-tone beep. I changed it to a rising 4-note arpeggio (C5 → E5 → G5 → C6, each 180ms apart) using the Web Audio API. A single tone feels like an error; a rising chord feels like an achievement. Each note uses a `linearRampToValueAtTime` envelope so it has a quick attack and gentle tail rather than a harsh click.

3. **History entry format** — The AI initially showed duration as "25 min focus". I changed it to "✓ 25:00 focus" with a coloured dot (amber for focus, blue for break) to match the ring's visual language, and a right-aligned time stamp. The checkmark adds a small sense of accomplishment per entry.

---

## 5. Honest Gap

**What isn't polished enough:** The settings inputs (focus minutes / break minutes) update the timer correctly, but there is no visual feedback or validation when you type an out-of-range value (e.g. 0 or 200). The input just silently clamps or ignores it. A user who types "0" will get a 0:00 timer that immediately completes, which is confusing.

**What I'd do with another day:** Add proper inline validation — a red border + short error message ("Min 1 minute") that appears on blur, and disable the Start button until the input is valid. I'd also persist the user's custom durations to `localStorage` so they don't have to re-enter them on every page load. Finally, I'd add a keyboard shortcut (`Space` to start/pause, `R` to reset) which power users would appreciate for staying in flow.
