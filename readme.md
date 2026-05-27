# Focus Pomodoro Timer

A single-screen, lightweight Pomodoro timer designed for productivity. This application features daily session history, ambient background support, audible cues, and a fully responsive design.

## Features

*   **Timer Phases:** Dedicated modes for Focus, Short Break, and Long Break.
*   **Customizable:** Configurable durations for all timer phases.
*   **Audio Feedback:** Web Audio API integration for session-end alerts and countdown ticks.
*   **Session Tracking:** Automated daily session history that persists across reloads and resets daily.
*   **Gamification:** Integrated day-streak tracking using localStorage.
*   **Keyboard Shortcuts:**
    *   **Space**: Play/Pause
    *   **R**: Reset timer
    *   **S**: Skip current phase
    *   **+/-**: Adjust time by 1 minute
*   **Responsive Design:** Fully optimized for screens down to 360px wide.
*   **Accessibility:** Built with ARIA roles, proper labeling, and full keyboard-accessible controls.

## How to Run

### Option 1 — Open Directly
Simply locate the `index.html` file in your file manager and double-click it to open it in your default web browser. No build step or dependencies required.

### Option 2 — Serve Locally (Recommended)
For consistent audio behavior, it is recommended to serve the file through a local web server:

**Using Python 3:**
```bash
python3 -m http.server 8080
Then, navigate to http://localhost:8080 in your browser.

Using Node.js:

Bash
npx serve .
Then, open the URL provided in your terminal.

Requirements
Browser: Modern web browser (Chrome 90+, Firefox 88+, Safari 15+, Edge 90+ or higher).

Environment: No npm install or complex build toolchain required.

Project Structure
Plaintext
.
├── index.html      # All-in-one file (HTML, CSS, and JavaScript)
├── readme.md       # Project documentation
└── answers.md      # Project answers/notes
