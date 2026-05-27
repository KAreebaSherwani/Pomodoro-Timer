Focus — Pomodoro Timer
A single-screen Pomodoro timer with daily session history, ambient background, audible cues, and responsive design.

How to Run
Option 1 — Open directly (no install needed)
Open index.html
Just double-click index.html in your file manager, or drag it into any browser. No build step, no dependencies, no server required.

Option 2 — Serve locally (recommended for consistent audio behaviour)
Python 3 (usually pre-installed on macOS / Linux)
python3 -m http.server 8080
then open http://localhost:8080

Node.js
npx serve .
then open the printed URL

Requirements
A modern browser (Chrome 90+, Firefox 88+, Safari 15+, Edge 90+)

No npm install, no build toolchain

Project Structure
Plaintext
.
├── index.html      # Entire app — HTML, CSS, and JS in one file
├── README.md
└── ANSWERS.md
Features
Pomodoro timer with Focus / Short Break / Long Break phases

Configurable durations (focus, break, long break)

Web Audio API chimes on session end and final countdown ticks

Daily session history (persists across reloads, resets on new day)

Day-streak tracking via localStorage

Keyboard shortcuts: Space play/pause, R reset, S skip, +/- 1 min

Responsive down to 360 px

Accessibility: ARIA roles, labels, and keyboard-accessible controls