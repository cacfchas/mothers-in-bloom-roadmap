# Mothers in Bloom — Roadmap Studio

A practitioner-led participant success platform for **Mothers in Bloom**, a financial-empowerment
program for mothers transitioning into CACF. Built for Janasha "Jay" Bradford to run a guided
"Blooming Roadmap" session with each participant.

## What it is

A single self-contained HTML file — no build step, no server, no dependencies to install.
It runs entirely in the browser and is hosted as a static site on **GitHub Pages**.

- **Intake** — Jay's verbatim intake form (10 sections, Q1–Q84) in her exact order
- **Tools** — Budget builder, Needs · Wants · Wishes board, and Goals, opened from each participant's profile
- **Summary PDF** — a branded export with a "what to include" picker; the Blooming Roadmap graphic is the last page
- **Standalone Budget Builder** under the 🧰 Tools menu

## Access

The app opens behind a simple access-code screen. The current code is set in the source
(`GATE_HASH` near the bottom of the HTML, stored as a hash). This is a light deterrent for a
public link — not real security. True per-user logins come with the future hosted portal.

## Where the data lives

All participant data is stored **locally in the browser** (`localStorage`) on whatever device
entered it. Nothing is uploaded or shared. Two devices = two separate datasets. Use the in-app
**JSON backup** (and the CSV export) regularly, and keep backups in SharePoint after each session.

> Because data is local, this public site never exposes real participant information — a visitor
> sees an empty app, not Jay's people.

## Updating the live site

1. Edit `Mothers in Bloom - Roadmap Studio.html`
2. `git commit -am "your change"` and `git push`
3. GitHub Pages redeploys automatically in ~1 minute

`index.html` is a tiny redirect to the app so the site root loads it.

## Roadmap (next)

Deeper finance tools (savings/debt/DTI/credit), longitudinal session history, and the
participant-facing portal with real logins (V2). Integrations route the hard parts to existing
systems: C-Suite (records/reporting), Planner (tasks/reminders), SharePoint (secure docs).
