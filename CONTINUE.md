# Mothers in Bloom · Roadmap Studio — Continuation Guide

A self-contained handoff so this work can be picked up on any machine (by a person or a fresh AI session). Last updated 2026-09-14.

---

## TL;DR — pick up on another machine

```bash
git clone https://github.com/chasmanning/mothers-in-bloom-roadmap.git
cd mothers-in-bloom-roadmap
open "Mothers in Bloom - Roadmap Studio.html"   # runs standalone — no build, no install
```

- **Live site:** https://chasmanning.github.io/mothers-in-bloom-roadmap/
- **Access code:** the word you chose (stored only as a djb2 hash in `GATE_HASH` near the bottom of the HTML — deliberately not written here so it stays out of the public repo).
- **To change the app:** edit the one HTML file → `git commit -am "..."` → `git push`. GitHub Pages redeploys in ~1 minute.

---

## What this is

A practitioner-led **participant success platform** for **Mothers in Bloom (MIB)**, a financial-empowerment program for mothers transitioning into CACF. Built for the **Mothers in Bloom program lead** to run a guided "Blooming Roadmap" session with each participant. It's a single, self-contained HTML file — vanilla JS, inline CSS, `localStorage`, no dependencies except pdf-lib (loaded from CDN) for the Summary PDF.

## Golden rule — tech layer, not content layer

The program lead's intake questions, coaching scripts, and option labels are reproduced **verbatim** from her source documents. Never paraphrase, re-case, condense, or invent program content. UI chrome we author (button labels, tool captions) is fine. When unsure of wording, quote the source.

Source docs (on the original machine, `~/Downloads/`):
- `Mothers in Bloom Participant Success Platform.docx` — the vision/blueprint (drives what to build next)
- `Mothers in Bloom Participant Update & Roadmap Intake.pdf` — the 84-question intake form
- `Mothers_in_Bloom_Blooming_Roadmap Guide.pdf` — the roadmap graphic (embedded in the app as base64)

---

## Architecture (navigating the one big file)

The whole app is `Mothers in Bloom - Roadmap Studio.html`. `index.html` is a tiny redirect to it (so the site root loads the app).

- **Data:** `DB.clients{}` keyed by id, persisted to `localStorage` under `mib_studio_v1`. Each client object (see `newClientObj()`) holds `answers{}` plus per-tool data: `budget`, `nww`, `goals`, `savings`, `debts`/`debtPlan`, `dti`, `credit`, `housing`, `budgetActuals`, `referrals`, `benefits`/`benefitsOther`, `checkins`, `actionPlan`, `planner`. Also `DB.budgets[]` for standalone budget worksheets.
- **Intake shape:** `STAGES[]` is now organised as **pre / during / post** (each stage carries a `phase` of `pre`, `during` or `post`). Stage 1 is the program lead's pre-meeting prep, verified live via `type:"verify"` checkboxes; stages 2–6 are the conversation; stage 7 is the post-meeting coding pass. Anything gated as staff-only keys off `s.phase==='post'`, **not** the stage key.
- **Views:** roster → profile (the hub) → workspace (intake) → tools → dashboard. `showOnly(view)` switches; browser back/forward via History API (`pushNav`/`popstate`).
- **Profile hub:** opening a participant shows tool cards grouped **Overview / Coaching & accountability / Roadmap / Financial planning / Connections**. Each card opens its tool in a work modal (`openTool(kind)` → `renderWorkModal()`, routed by the `R{}` map).
- **Find code fast** — search for these banner comments:
  `FINANCIAL PLANNING SYSTEM`, `RESOURCES, REFERRALS & BENEFITS`, `COACHING & ACCOUNTABILITY`, `PROGRESS SNAPSHOT`, `ACTION PLAN`, `TIME MANAGEMENT`, `CASELOAD DASHBOARD`, and the `generateSummary` PDF builder. The example seed is the `if(Object.keys(DB.clients).length===0 ...)` block.
- **Editable pick lists:** `PICK_DEFAULTS` / `DB.settings` hold the starter lists for "how the gift is spent" and the check-in barrier / opportunity pickers. The program lead edits them in-app (`pickEdit`) — do not hard-code her options.
- **Adding a tool** (the established pattern): add data in `newClientObj()` → write `renderX(c)` + handlers → add a `toolCard()` in `renderProfile()` → add it to the `titles{}` + `R{}` maps in `renderWorkModal()` → add CSS → optionally seed the example and add a section to `generateSummary`.

---

## Round 2 — built 2026-09-14 from the 2026-07-23 feedback session

Source: the build spec written up from that working session. It is deliberately **not** in this repo — it carries internal program detail (cohort filters, eligibility criteria, tagging rules) that goes beyond the code. It lives in the CACF working folder and the private working repo; ask Chas.

Done this round:
- **Intake restructured into pre / during / post**, with her cuts, merges and reorderings applied. Ten stages became seven. Per-block "verified with the participant" checkboxes on the pre-meeting section.
- **Check-in tool rebuilt** as a repeating subset of the intake: re-verification of her details with edit-in-place (writes straight back to the intake answers), where things stand, biggest barrier and greatest opportunity (pick list + narrative), wins, financial goals, her homework vs. your next steps, notes, "what didn't I ask that I should have", and an optional testimonial. The intake shows as check-in #1, derived rather than copied.
- **Initial-goal flag** on goals ("goal she joined with" + achieved yes/no), replacing intake Q6 and Q7.
- **Interim free-text tag field** per participant, shown on the roster card and searchable.
- **Cut intake prompts reused as in-module help text** in Needs·Wants·Wishes and Goals (`CUT_PROMPTS`).

Questions retired from the intake (data is untouched; existing answers just stop rendering):
Q6, Q7, Q9, Q16, Q17, Q18, Q50–Q54, Q63, Q65, Q67, Q74, Q81, Q82, Q83. Q71 and Q73 moved to the check-in.

Authored, not the program lead's words — these render with a **draft wording** badge and need her sign-off:
the IEP / autism-spectrum question, the leadership-interest question and its note field, and "What would make your experience better?".

## What's built (V1 — the full blueprint)

Intake (the program lead's verbatim sections), Budget Builder, Needs·Wants·Wishes, Goals (SMART + milestones), **Savings tracker, Debt payoff planner (snowball/avalanche), Debt-to-income, Credit tracking, Housing journey, Planned-vs-actual**, **Referrals + Government assistance**, **Check-in / session history**, **Progress snapshot**, **Action plan**, **Planner**, **Caseload dashboard + CSV report**, branded **Summary PDF** (with a what-to-include picker), an **access-code gate**, and **Download all / Restore all / Clear this device** backup tools. Seeded with 7 demo participants.

Everything is committed, pushed, and live.

---

## Gotchas (learned the hard way — don't re-break these)

- **`file://` blocks `fetch()`.** The app is usually opened by double-clicking (file://), where browsers block fetching local files. So the roadmap image is **base64-embedded** (`ROADMAP_DATA`), not fetched. Any new asset must be embedded the same way, never `fetch()`ed, or it will silently vanish when opened from disk.
- **PDF font can't encode non-Latin glyphs.** `generateSummary` runs all text through `safe()`; don't hand pdf-lib raw emoji / `✓` / `↳` or it throws and falls back to the print dialog.
- **Examples seed only when `DB.clients` is empty.** An existing browser won't gain newly-added examples — view them in a fresh/incognito window. Note: **"Clear this device" sets `mib_example_removed=1`, which suppresses reseeding** (so clearing does not bring examples back).
- **The gate is a speed-bump, not security.** The code lives (hashed) in the public source; real per-user auth is a V2 backend concern.
- **PII stays local.** No backend, no network calls with data — participant data lives only in each browser's `localStorage`. Back up via **Download all → SharePoint**. The repo contains only code, never participant data.

---

## Preview while editing

The app works straight from `file://` (roadmap is embedded). If you want `http://` (e.g. to sanity-check network behavior), serve the folder with any static server, e.g. `python3 -m http.server 8000`, then open `http://localhost:8000/`. Do **not** commit participant data or backup JSON — `.gitignore` covers `.DS_Store`; keep exports out of the repo.

---

## What's next (open items)

0. **Still blocked on the program lead** (from the 2026-07-23 spec): the example action-plan template from the United Way contact; the goal-idea card list; her own wording for the gift-spend, barrier and opportunity pick lists (starter lists ship editable in the meantime); the fields she wants reportable/filterable; where the new intake questions should sit and how they should be worded; the tag brainstorm and card-badge priorities; dashboard metric suggestions; the off-ramp / graduation plan she is designing internally.
1. **Still to build from that spec:** budget rebuild in the wants-versus-needs layout, goal "idea" cards, reporting/filtering + cohort export, and the printout redesign. Deferred by agreement: the full tag system, the United Way FIP module, the action-plan redesign, the off-ramp/graduation module, dashboard changes, and Outlook reminders.
2. **Example names** (pending decision): the 6 seeded example moms have invented, demographically-flavored names. Options: leave them, swap to neutral placeholders (Participant A/B/C), a random mix, or names the program lead chooses.
3. **V2 — the hosted portal** (needs a real backend/logins): participant-facing per-mom dashboards, a partner dashboard, scheduling + reminders/automation, and the Graduation / Long-Term Follow-Up workflow. Everything through V1 was intentionally client-side/single-file; V2 is where a backend enters.
4. **C-Suite CSV mapping:** confirm the exact column list with the org so the exports map cleanly.
5. **Deliver:** round 2 is ready for the program lead to walk through. The draft-wording questions and the starter pick lists are the first things to put in front of her.

## Blueprint integrations (for V2)

Route the hard parts to existing systems: **C-Suite** (records/demographics/reporting), **Planner** (tasks/reminders/accountability), **SharePoint** (secure documents — already the backup target).

---

*This doc is self-contained; on the original machine there's also a running project memory, but everything needed to continue is here.*
