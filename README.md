# ClassPulse

ClassPulse collects anonymous classroom doubts and helps teachers see which concepts appear repeatedly in those submissions. It highlights shared patterns; it does not score students or establish what any individual understands.

## Run the app on Windows

### One-time setup

1. Install **Node.js 24 or later** from [nodejs.org](https://nodejs.org/en/download/). Choose the Windows installer and keep the option to add Node.js to `PATH` enabled.
2. Finish installation, then close and reopen PowerShell so it picks up the updated `PATH`.
3. Check that Node.js is available:

```powershell
node --version
```

The command should print `v24` or a higher major version. If PowerShell says `node` is not recognized, restart PowerShell; if that does not help, rerun the Node.js installer and ensure it adds Node to `PATH`.

### Download and start ClassPulse

1. On GitHub, open this repository, choose **Code → Download ZIP**, and extract the ZIP. (Alternatively, clone the repository with Git.)
2. In File Explorer, open the extracted `classpulse` project folder, select the address bar, type `powershell`, and press Enter. This opens PowerShell in the correct folder.
3. Confirm that the prompt is in the project folder, then start the server:

```powershell
node server.js
```

4. Keep the PowerShell window open. On the same computer, visit **http://localhost:4173/** in your browser.

If PowerShell reports `EADDRINUSE` or “address already in use,” a server is already using that port. Open its existing address in your browser instead of starting another copy. To stop a server, focus the PowerShell window where it is running and press `Ctrl+C`.

If you cannot find the existing server window, choose another port, for example 4175:

```powershell
$env:PORT = "4175"
node server.js
```

Then visit **http://localhost:4175/**. If that port is also in use, choose a different unused number and use that same number in the browser address. Stop the server with `Ctrl+C` in its PowerShell window. The first start creates `classpulse.sqlite` beside `server.js` and seeds the fictional demo classroom. The database file is ignored by Git.
## Demo walkthrough

1. On the landing page, choose **Open teacher dashboard**.
2. Select the fictional Electrical Engineering session, code **PULSE1**.
3. Select **Analyze classroom** to group its 15 sample doubts into patterns about current direction/KCL, voltage polarity/KVL, and multiple-loop equations.
4. Select a cluster to inspect its submission evidence, a clearly labeled hypothesis, and a practical teaching suggestion.
5. Choose **Join a classroom**, enter `PULSE1`, and submit a test doubt. The teacher dashboard polls every eight seconds and marks its previous analysis outdated.
6. Select **Reset demo** on the dashboard to restore the synthetic baseline.

All sample submissions are fictional. The repeatable mock analysis is labeled **DEMO ANALYSIS** and works without API credentials. Fewer than three doubts are treated as insufficient data. Its priority formula is `related submissions × 10 + instructional importance (1–3) × 5`; this is a transparent planning aid, not a validated measure.

## Features

- Landing page, session dashboard, session creation, and mobile-friendly student submission flow.
- Anonymous doubts without accounts, names, email addresses, or student IDs.
- SQLite storage for sessions, doubts, analyses, and recommendation completion.
- Short join codes, active/paused/ended session controls, duplicate-submit protection, and closed-session rejection.
- Ranked confusion clusters, representative doubt excerpts, suggested interventions, worked explanations, and practice questions.
- Submission counts, analysis freshness, and dashboard polling every eight seconds.
- Server-side request validation, 1,000-character doubt limit, bounded JSON request size, and basic in-memory rate limiting.

## Project structure

```text
server.js              Node HTTP API, SQLite setup, deterministic analysis
public/index.html      App entry point
public/app.js          Client-side pages and API integration
public/styles.css      Responsive visual design
test/app.test.js       Node built-in API tests
.env.example           Configuration placeholders
```

## Tests

Run the included tests with Node's built-in test runner:

```powershell
node --test test/app.test.js
```

The tests cover session creation and unique codes, anonymous submission, input limits, closed-session behavior, deterministic clustering/counts, outdated analysis state, recommendation persistence, and malformed JSON handling.

## API reference

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/api/config` | Runtime mode and demo code |
| `GET` | `/api/sessions` | List sessions with counts and latest analysis |
| `POST` | `/api/sessions` | Create a session |
| `GET` | `/api/sessions/:id` | Read a session |
| `GET` | `/api/sessions/:id/status` | Read counts and analysis freshness |
| `GET` | `/api/sessions/:id/doubts` | Read anonymous doubts for a teacher view |
| `POST` | `/api/sessions/:code/doubts` | Submit an anonymous doubt |
| `POST` | `/api/sessions/:id/analyze` | Save a deterministic demo analysis |
| `GET` | `/api/sessions/:id/analysis` | Read the latest analysis |
| `PATCH` | `/api/sessions/:id/status` | Activate, pause, or end a session |
| `PATCH` | `/api/recommendations/:id` | Save recommendation completion |
| `POST` | `/api/demo/reset` | Restore the fictional demo classroom |

## Technology and current limitations

This deliverable uses Node's built-in HTTP server and SQLite, plus plain browser JavaScript/CSS/HTML. The requested React, Vite, TypeScript, Tailwind, Express, Recharts, and Gemini SDK packages were unavailable in the provided runtime, and npm was not installed. There is no production bundler build step. `.env.example` contains Gemini placeholders only; this version does **not** call Gemini and always performs deterministic demo analysis. It must not be described as live AI.

The server has no teacher authentication, durable distributed rate limits, deployment hardening, or data retention controls. The in-memory limit resets with the server. Before using real classroom data, add teacher authorization, abuse prevention, privacy and retention controls, secure hosting, and testing under concurrent use. Students should not enter identifying details in doubts.
