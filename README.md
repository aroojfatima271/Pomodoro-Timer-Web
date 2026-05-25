# Pomodoro Timer — Focus with Daily History

A clean, minimal Pomodoro timer built with vanilla HTML/CSS/JS. No dependencies, no build step — just open and use.

## How to Run

### Option 1 — Open directly (fastest)
```bash
open index.html
```
Or just double-click `index.html` in your file explorer.

### Option 2 — Local dev server (recommended)
```bash
npx serve .
# then open http://localhost:3000
```

### Option 3 — Python
```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

No `npm install` needed. Zero dependencies.

---

## Features

- ⏱ Configurable focus (default 25 min) and break (default 5 min) timers
- ▶️ Start, Pause, Resume, Reset, Skip controls
- 🔔 Audible chime when a cycle ends (Web Audio API — no files needed)
- 🔄 Auto-switches between focus → break → focus
- 📋 Daily session history persists across page reloads via `localStorage`
- 🗓 History resets automatically on a new calendar day
- 📱 Fully responsive from 360px phones to 1440px desktops

---

## Stack

Vanilla HTML · CSS · JavaScript — no frameworks, no build tooling.
