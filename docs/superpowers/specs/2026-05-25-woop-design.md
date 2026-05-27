# WOOP — Design Spec

**Date:** 2026-05-25
**Status:** Draft, awaiting user review
**Owner:** Ana-Maria Brad

## 1. Purpose

A new coaching instrument added to Roti-Coaching: a self-guided WOOP exercise (Wish-Outcome-Obstacle-Plan, Gabriele Oettingen) that walks the user through a single round in 5–10 minutes, saves completed rounds to a local history, and exports each round as a PDF.

WOOP fills a gap not covered by the existing tools: it pairs a wish with the inner obstacle that blocks it and produces an if-then implementation intention. GROW gives a goal + first step; WOOP gives a goal + obstacle-aware response plan.

## 2. Scope

**In scope (v1):**
- Single self-contained HTML page (`woop.html`) inside the Roti-Coaching folder.
- Landing screen with intro + "Începe un WOOP nou" CTA + history list of past completed WOOPs.
- Wizard flow: Timeframe setup screen → Wish → Outcome → Obstacle → Plan → Result.
- Imagery timer (1 minute) on Outcome and Obstacle screens; required on first WOOP, skippable on subsequent ones via a remembered preference.
- Medium guidance on the Obstacle step: explainer + 5 example inner-obstacle chips. No validation.
- localStorage persistence: one in-progress WOOP (resume-mid-exercise) + a history list of completed WOOPs.
- PDF export per completed WOOP via html2canvas + jsPDF.
- New card on `index.html` linking to `woop.html`.
- Romanian, audience-agnostic copy.

**Out of scope (v1):**
- Adolescent-specific version (can come later if needed).
- Editing past WOOPs (history is read-only + delete).
- Reminders / notifications / daily prompts (not possible from a static page).
- Account, cloud sync, sharing — entirely local.
- Sound or vibration cues on timer completion.

## 3. Architecture

Single self-contained HTML file `woop.html` in `Roti-Coaching/`, matching the pattern of `modelul-grow.html` and the wheels. All HTML, CSS, and JavaScript inline; no build system, no framework.

**External CDN dependencies** (in `<head>`):
- Google Fonts: Fraunces + Inter (matches existing pages).
- `html2canvas` + `jsPDF` for PDF export.

**SortableJS is NOT used** — no drag/drop in this tool.

**Code organization inside `<script>`**, using the same labeled-section pattern Valori-App uses:

1. `DATA` — static dictionaries (`TIMEFRAMES`, `OBSTACLE_EXAMPLES`, copy strings).
2. `STATE` — module-level mutable state: `currentDraft` (the in-progress WOOP), `history` (array of completed WOOPs), `currentScreen`, `imageryPref` (timer skip preference).
3. `PERSISTENCE` — `saveDraft`, `loadDraft`, `saveHistory`, `loadHistory`, `savePref`, `loadPref`. Each writes to its own localStorage key.
4. `LANDING` — renders intro + CTA + history list, attaches view/PDF/delete handlers.
5. `BUILDERS` — `buildTimeframe`, `buildWish`, `buildOutcome`, `buildObstacle`, `buildPlan`, `buildResult` (one per screen). Each reads from `currentDraft`, renders the screen, attaches input handlers, and calls `saveDraft()` on every mutation.
6. `TIMER` — `startImageryTimer(duration, onComplete)`, used by Outcome and Obstacle screens.
7. `RESULT` — renders the completed WOOP card; "Finalizează" button on the Plan screen pushes the draft onto `history`, clears `currentDraft`, and navigates to Result.
8. `PDF` — `exportPDF(woop)`. Applies a `pdf-export` body class for print-friendly layout, rasterizes the result card with html2canvas, builds a single-page PDF via jsPDF.
9. `NAVIGATION` — `goTo(screen)` is the single entry point; updates the progress bar and calls the right builder.

CSS organized with `/* ── SECTION ── */` banners matching the screen flow.

## 4. Screen Flow

### 4.1 Landing

- Header: title "WOOP — *de la dorință la pas*", subtitle "Wish · Outcome · Obstacle · Plan", "← Roti Coaching" back link.
- Intro paragraph: short explanation that WOOP is a science-backed method by Gabriele Oettingen, helps turn a wish into a concrete step by contrasting the desired outcome with the inner obstacle. Takes 5–10 minutes.
- Primary CTA: **"Începe un WOOP nou"**.
- "WOOP-uri completate" heading + history list.
- If a `currentDraft` exists from a prior session, show a small banner at the top: *"Ai un WOOP în desfășurare — continuă sau începe din nou?"* with two buttons.
- History list rendering: each row shows wish text (Fraunces serif), date + timeframe meta, and action buttons (`Vezi` / `PDF` / `Șterge`). Empty state: italic dashed-border card with *"Aici vor apărea WOOP-urile tale completate."*

### 4.2 Timeframe (setup, no progress bar)

- Title: "Pentru ce orizont de timp?"
- Subtitle: "Înainte de a începe"
- Prompt: "Cât de departe te uiți? Orizontul de timp te ajută să formulezi o dorință concretă."
- 4 chip options (2×2 grid):
  - 24 de ore — *Astăzi / mâine*
  - O săptămână — *Săptămâna viitoare*
  - O lună — *Următoarele 4 săptămâni*
  - Un an — *Sau mai mult*
- Nav: "Anulează" (back to Landing) / "Continuă →" (disabled until selection).

### 4.3 Wish (W) — Step 1 of 4

- Progress bar: 4 segments, segment 1 active.
- Step letter: "W" (Fraunces italic, accent color).
- Title: "Wish — dorința ta"
- Subtitle: "Pasul 1 din 4 · orizont: *<chosen timeframe>*"
- Prompt: "Care e o dorință importantă pentru tine în <timeframe-phrasing>? Ceva ce contează, e provocator, dar realizabil. În 3–6 cuvinte."
  Timeframe phrasings used in the prompt: `day` → "în următoarele 24 de ore", `week` → "în săptămâna care vine", `month` → "în luna care vine", `year` → "în anul care vine".
- Single-line text input. Char hint "3–6 cuvinte" below right.
- Nav: "← Înapoi" / "Continuă →" (disabled if empty).

### 4.4 Outcome (O) — Step 2 of 4

- Step letter: "O".
- Title: "Outcome — cel mai bun rezultat"
- Prompt: "Care e cel mai bun rezultat al îndeplinirii acestei dorințe? Cum te-ai simți? Ce s-ar schimba?"
- **Imagery timer card** (above textarea):
  - Hint: "Închide ochii și imaginează-ți rezultatul."
  - "01:00" display (counts down from 60 seconds to 00:00).
  - "Începe" button → starts the countdown; on reaching 00:00, the textarea is unlocked and the input is auto-focused.
  - "Sari peste" link → applies the imagery-skip preference and unlocks the textarea immediately.
  - On first WOOP ever, "Sari peste" is hidden — timer is required once. After that, "Sari peste" is visible and remembers the user's choice across sessions.
- Textarea (initially read-only until timer completes or skipped). Placeholder: "Descrie aici cel mai bun rezultat..."
- Nav: "← Înapoi" / "Continuă →" (disabled if empty).

### 4.5 Obstacle (O) — Step 3 of 4

- Step letter: "O".
- Title: "Obstacle — obstacolul interior"
- Prompt: "Care e principalul obstacol *din tine* care te ține pe loc? O emoție, un obicei, o frică, o convingere — nu o circumstanță externă."
- **Guidance card** (above timer):
  - Bold "Atenție:" lead-in, explanation that WOOP works only with inner obstacles, examples of what NOT to write (traffic, time, boss).
  - 5 example chips (Fraunces italic, pill shape): *teama de respingere*, *perfecționism*, *amânare*, *îndoiala de sine*, *distragere*.
- **Imagery timer card**:
  - Hint: "Închide ochii și imaginează-ți obstacolul."
  - Same behavior as Outcome timer (with same skip preference).
- Textarea (read-only until timer completes or skipped).
- Nav: "← Înapoi" / "Continuă →" (disabled if empty).

### 4.6 Plan (P) — Step 4 of 4

- Step letter: "P".
- Title: "Plan — planul „dacă-atunci""
- Prompt: "Construiește un plan de implementare. Decizi acum cum vei reacționa când apare obstacolul — ca să nu trebuiască să decizi în clipa aia."
- If-then builder card:
  - "Dacă..." input (helper: "Când apare obstacolul tău interior")
  - "...atunci..." input (helper: "Acțiunea concretă pe care o vei face în loc")
- Nav: "← Înapoi" / "Finalizează WOOP →" (disabled if either input empty). On click: push to history, clear draft, navigate to Result.

### 4.7 Result

- Header: "WOOP-ul tău" + meta "<date> · orizont: <timeframe>"
- Single card with 4 blocks:
  - **Wish — dorința** (large, accent color, Fraunces serif)
  - **Outcome — cel mai bun rezultat**
  - **Obstacle — obstacolul interior**
  - **Plan — dacă-atunci** (formatted as: *Dacă* [if-text], *atunci* [then-text])
- Actions: "Descarcă PDF" / "Începe un WOOP nou" / "Înapoi la listă".

## 5. Data Model

Three independent localStorage keys, namespaced to avoid clashes with future tools:

```
woop_draft_v1       — the in-progress WOOP (or absent)
woop_history_v1     — array of completed WOOPs
woop_pref_v1        — user preferences (currently: imagery skip)
```

**Draft / History entry shape:**

```json
{
  "id": "1716663240000",                        // millisecond timestamp as string
  "createdAt": "2026-05-25T19:14:00.000Z",
  "completedAt": "2026-05-25T19:21:00.000Z",  // history only
  "timeframe": "week",                          // 'day' | 'week' | 'month' | 'year'
  "wish": "Termin propunerea pentru AISV",
  "outcome": "Liniște după luni de pregătire...",
  "obstacle": "Perfecționismul. Tendința de a reciti...",
  "planIf": "simt că vreau să mai rescriu un paragraf deja revizuit",
  "planThen": "îmi pun un timer de 10 minute, după care marchez secțiunea ca încheiată"
}
```

**Preferences shape:**

```json
{
  "imageryCompletedOnce": true,
  "skipImageryByDefault": false
}
```

`imageryCompletedOnce` flips to `true` the first time the user finishes (or explicitly waits through) an imagery timer. After that, "Sari peste" becomes available. `skipImageryByDefault` is set when the user clicks "Sari peste" and persists across sessions.

**Persistence rules:**
- `saveDraft()` runs on every mutation in the wizard flow.
- On Finalize, the draft is moved to history (`completedAt` stamped), and `woop_draft_v1` is cleared.
- History list newest-first.
- Bumping the `_v1` suffix on a key is the way to invalidate old data after a schema-incompatible change.

## 6. PDF Export

A "Descarcă PDF" action lives both on the Result screen and on each history row.

- Implementation: temporarily apply `pdf-export` body class (constrains widths and applies print-friendly typography), then `html2canvas` the result card, then build a single-page A4 PDF via jsPDF.
- Filename: `woop-<wish-slug>-<YYYY-MM-DD>.pdf`. The wish slug is generated by lowercasing the wish text, stripping Romanian diacritics (ă→a, â→a, î→i, ș→s, ț→t), replacing non-alphanumeric runs with `-`, and truncating to 40 characters.
- The PDF mirrors the Result screen layout exactly: header (title + date + timeframe), then the four labeled blocks (Wish, Outcome, Obstacle, Plan).
- All new result-screen styles must have a matching rule under the `PDF EXPORT MODE` CSS section (same convention as Valori-App).

## 7. Integration with Roti-Coaching index

`index.html` of Roti-Coaching gets a new tool card alongside GROW and the wheels. Card title "WOOP", subtitle "Wish · Outcome · Obstacle · Plan", short description: *"De la dorință la pas concret, cu un plan dacă-atunci."* Links to `woop.html`.

Card visual treatment matches the existing cards (same paper palette, Fraunces title, hover state, etc).

## 8. Open questions

None at time of writing. All major decisions locked through brainstorming.

## 9. Reference

- Oettingen, G. (2014). *Rethinking Positive Thinking: Inside the New Science of Motivation.*
- WOOP method site: https://woopmylife.org/
- Mental Contrasting with Implementation Intentions (MCII) — the academic framework underlying WOOP.
