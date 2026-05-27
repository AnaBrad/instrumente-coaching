# Credințe limitative — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `credinte-limitative.html`, an 8th coaching instrument that walks the user through Byron Katie's "The Work" (one limiting belief, 4 questions, 3 turnarounds × 3 examples) and produces a printable PDF.

**Architecture:** Single static HTML file at the repo root, sibling of `modelul-woop.html`. Paginated screens toggled via `hidden`, identical pattern to WOOP. State held in one in-memory JS object — no localStorage. Print/PDF via `window.print()` + `@media print` styles. Add a new card to `index.html` and soften the lead copy.

**Tech Stack:** Vanilla HTML/CSS/JS. Google Fonts (Fraunces + Inter). No build step, no dependencies, no test framework. Verification is manual browser walkthrough.

**Spec:** `docs/superpowers/specs/2026-05-27-credinte-limitative-design.md`. Read it before starting Task 1.

**Reference files (crib liberally):**
- `modelul-woop.html` — canonical paginated-screen pattern + `go()` / `goNext()` / `goBack()` navigation
- `roata-vietii.html` — `.session-info` (Nume + Data) CSS and DOM, PDF antet formatting
- `index.html` — card style, lead copy

**Commit policy:** Each task ends with a commit step showing the message. **The user must explicitly approve each commit** — do not run `git commit` without their go-ahead (per project-wide instruction). If they want to commit at task boundaries, run the suggested command; if not, defer.

---

## File Structure

- **Create:** `credinte-limitative.html` (the new tool)
- **Modify:** `index.html` (add card, soften lead copy, update meta description/OG/Twitter tags)
- **Create (if missing):** none — no test files, no auxiliary assets

---

## Task 1: Scaffold `credinte-limitative.html`

Create the file with head/meta, fonts, CSS variables, base layout, the `.screen` container pattern, and an empty `<main>` ready to hold screens. No interactive logic yet.

**Files:**
- Create: `credinte-limitative.html`

- [ ] **Step 1: Create the file with head + base CSS**

Use this as the starting content. The CSS variables are copied verbatim from `modelul-woop.html`. Comments mark what later tasks will add.

```html
<!DOCTYPE html>
<html lang="ro">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Credințe limitative — Instrument de coaching</title>
<meta name="description" content="Instrument de coaching după Byron Katie — cele 4 întrebări și 3 răsturnări pentru a lucra cu o credință limitativă. Creat de Ana-Maria Brad.">
<meta name="author" content="Ana-Maria Brad">

<!-- Open Graph -->
<meta property="og:type" content="website">
<meta property="og:title" content="Credințe limitative — Instrument de coaching">
<meta property="og:description" content="Cele 4 întrebări după Byron Katie. Iei o credință care te limitează, o cercetezi în liniște și o întorci pe toate părțile.">
<meta property="og:url" content="https://anabrad.github.io/instrumente-coaching/credinte-limitative.html">
<meta property="og:locale" content="ro_RO">

<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Credințe limitative — Instrument de coaching">
<meta name="twitter:description" content="Cele 4 întrebări după Byron Katie. Iei o credință care te limitează, o cercetezi în liniște și o întorci pe toate părțile.">

<link rel="canonical" href="https://anabrad.github.io/instrumente-coaching/credinte-limitative.html">

<!-- Favicon: italic "4?" in paper palette — see Notes at end of plan if it reads poorly at 16px -->
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='46' fill='%23f1e6d2' stroke='%23a05a25' stroke-width='2'/%3E%3Ctext x='50' y='66' font-family='serif' font-style='italic' font-size='50' fill='%23a05a25' text-anchor='middle'%3E4%3F%3C/text%3E%3C/svg%3E">

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,wght@0,300;0,400;0,500;1,300;1,400&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">

<style>
  :root {
    --paper: #f1e6d2;
    --paper-soft: #e6d6ba;
    --paper-deep: #d9c69e;
    --surface: #fbf4e3;
    --ink: #2a1d10;
    --ink-soft: #5a4530;
    --ink-mute: #5d4a35;
    --accent: #a05a25;
    --accent-deep: #6e3a14;
    --accent-2: #7a2336;
    --bordo: #7a2336;
    --radius: 6px;
    --radius-lg: 10px;
  }
  * { box-sizing: border-box; }
  :focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }
  body {
    margin: 0;
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
    background: var(--paper);
    color: var(--ink);
    line-height: 1.55;
    -webkit-font-smoothing: antialiased;
    font-size: 15px;
  }
  .page { max-width: 760px; margin: 0 auto; padding: 40px 28px 60px; }

  .screen {
    background: var(--surface);
    border: 0.5px solid var(--paper-deep);
    border-radius: var(--radius-lg);
    padding: 32px 32px 36px;
    margin-bottom: 8px;
    box-shadow: 0 1px 0 rgba(0,0,0,0.02);
  }

  /* Top header (title + back-link + session-info on Rezumat) */
  .back-row {
    margin-bottom: 8px;
  }
  .back-link {
    font-size: 12px;
    color: var(--ink-mute);
    text-decoration: none;
    text-transform: uppercase;
    letter-spacing: 0.1em;
  }
  .back-link:hover { color: var(--accent); }

  header.top {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    gap: 20px;
    margin-bottom: 22px;
    padding-bottom: 14px;
    border-bottom: 0.5px solid var(--bordo);
  }
  h1 {
    margin: 0;
    font-family: 'Fraunces', serif;
    font-weight: 300;
    font-size: 36px;
    letter-spacing: -0.01em;
    color: var(--ink);
  }
  h1 em { font-style: italic; color: var(--accent); font-weight: 400; }
  .subtitle {
    margin: 6px 0 0;
    font-size: 11px;
    color: var(--ink-mute);
    text-transform: uppercase;
    letter-spacing: 0.14em;
  }

  /* Belief echo (shown above Q1–Q4) */
  .belief-echo {
    font-family: 'Fraunces', serif;
    font-style: italic;
    font-size: 20px;
    color: var(--accent-deep);
    text-align: center;
    padding: 16px 12px;
    border-top: 0.5px dashed var(--paper-deep);
    border-bottom: 0.5px dashed var(--paper-deep);
    margin-bottom: 24px;
    line-height: 1.4;
  }

  /* Question prompt (Fraunces, larger) */
  .question {
    font-family: 'Fraunces', serif;
    font-weight: 400;
    font-size: 24px;
    line-height: 1.3;
    color: var(--ink);
    margin: 0 0 18px;
  }
  .question em { color: var(--accent); font-style: italic; }

  .helper {
    font-size: 13px;
    color: var(--ink-mute);
    margin: -8px 0 14px;
    line-height: 1.5;
  }

  /* Textareas */
  textarea {
    width: 100%;
    min-height: 90px;
    font-family: inherit;
    font-size: 15px;
    color: var(--ink);
    background: var(--paper);
    border: 0.5px solid var(--paper-deep);
    border-radius: var(--radius);
    padding: 12px 14px;
    resize: vertical;
    line-height: 1.5;
  }
  textarea:focus {
    outline: none;
    border-color: var(--accent);
    background: var(--surface);
  }
  textarea::placeholder { color: var(--ink-mute); opacity: 0.7; }

  /* Da/Nu choice buttons (for Q1, Q2) */
  .choice-row {
    display: flex;
    gap: 12px;
    margin-bottom: 18px;
  }
  .choice {
    flex: 1;
    font-family: 'Fraunces', serif;
    font-weight: 400;
    font-style: italic;
    font-size: 22px;
    padding: 18px 12px;
    background: var(--paper);
    border: 0.5px solid var(--paper-deep);
    border-radius: var(--radius);
    color: var(--ink-soft);
    cursor: pointer;
    transition: background 0.15s, border-color 0.15s, color 0.15s;
  }
  .choice:hover { border-color: var(--accent); color: var(--ink); }
  .choice.selected {
    background: var(--accent);
    border-color: var(--accent);
    color: var(--paper);
  }

  /* Turnaround helper / explainer */
  .ta-explainer {
    background: var(--paper);
    border-left: 2px solid var(--accent);
    padding: 12px 16px;
    border-radius: 0 var(--radius) var(--radius) 0;
    font-size: 13px;
    color: var(--ink-soft);
    line-height: 1.55;
    margin-bottom: 18px;
  }
  .ta-explainer em { font-style: italic; color: var(--accent-deep); }

  .field-group { margin-bottom: 14px; }
  .field-group label {
    display: block;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--accent);
    margin-bottom: 6px;
  }

  /* Action row (back + Continuă, or Salvează PDF + Începe din nou) */
  .actions {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
    margin-top: 24px;
    padding-top: 18px;
    border-top: 0.5px solid var(--paper-deep);
  }
  .btn {
    font-family: 'Inter', sans-serif;
    font-size: 13px;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    padding: 12px 22px;
    border-radius: var(--radius);
    border: 0.5px solid var(--accent);
    background: var(--accent);
    color: var(--paper);
    cursor: pointer;
    font-weight: 500;
    transition: background 0.15s;
  }
  .btn:hover { background: var(--accent-deep); }
  .btn:disabled {
    opacity: 0.45;
    cursor: not-allowed;
    background: var(--accent);
  }
  .btn.ghost {
    background: transparent;
    color: var(--accent);
  }
  .btn.ghost:hover { background: var(--paper); color: var(--accent-deep); }

  /* Session-info block (Nume + Data) — used on Rezumat */
  .session-info {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
  }
  .session-info input {
    padding: 8px 12px;
    font-size: 13px;
    border: 0.5px solid var(--ink-mute);
    border-radius: var(--radius);
    background: var(--surface);
    color: var(--ink);
    min-width: 170px;
    font-family: inherit;
  }
  .session-info input::placeholder { color: var(--ink-mute); }
  .session-info input:focus {
    outline: none;
    border-color: var(--accent);
    background: var(--paper);
  }
  .sr-only {
    position: absolute; width: 1px; height: 1px;
    overflow: hidden; clip: rect(0,0,0,0);
  }

  /* Rezumat blocks + turnaround render — added in Task 8 */
  /* Print styles — added in Task 9 */
</style>
</head>
<body>

<div class="page">
  <main id="app">
    <!-- Screens injected via subsequent tasks. -->
  </main>
</div>

<script>
(function() {
  'use strict';

  // FLOW + state + navigation — added in Task 2.

})();
</script>

</body>
</html>
```

- [ ] **Step 2: Verify the file loads in a browser**

Run: `open "credinte-limitative.html"` (macOS) or open the file path in any browser.
Expected: blank-ish paper-colored page with the soft `.page` container visible, no errors in DevTools console. Fonts load from Google.

- [ ] **Step 3: Commit (ask user first)**

Suggested message:

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: scaffold (head + base styles)"
```

---

## Task 2: State + navigation framework

Define the FLOW array, the `state` object, and the `go() / goNext() / goBack()` functions. No screens are mounted yet — just the machinery.

**Files:**
- Modify: `credinte-limitative.html` (inside the `<script>` IIFE)

- [ ] **Step 1: Replace the empty IIFE with state + navigation**

Replace the placeholder comment inside the script with:

```js
  // --- Flow definition -------------------------------------------------
  const FLOW = [
    'landing',
    'belief',
    'q1', 'q2', 'q3', 'q4',
    'ta1', 'ta2', 'ta3',
    'summary',
  ];

  // --- State (in-memory only — no localStorage) ------------------------
  const state = {
    screen: 'landing',
    belief: '',
    q1: { answer: null, note: '' },   // answer: 'da' | 'nu' | null
    q2: { answer: null, note: '' },
    q3: '',
    q4: '',
    ta1: { statement: '', examples: ['', '', ''] },
    ta2: { statement: '', examples: ['', '', ''] },
    ta3: { statement: '', examples: ['', '', ''] },
    name: '',
    date: new Date().toISOString().slice(0, 10),  // yyyy-mm-dd
  };

  function resetState() {
    state.screen = 'landing';
    state.belief = '';
    state.q1 = { answer: null, note: '' };
    state.q2 = { answer: null, note: '' };
    state.q3 = '';
    state.q4 = '';
    state.ta1 = { statement: '', examples: ['', '', ''] };
    state.ta2 = { statement: '', examples: ['', '', ''] };
    state.ta3 = { statement: '', examples: ['', '', ''] };
    state.name = '';
    state.date = new Date().toISOString().slice(0, 10);
  }

  // --- Navigation ------------------------------------------------------
  function go(screen) {
    state.screen = screen;
    FLOW.forEach(s => {
      const el = document.getElementById('screen-' + s);
      if (el) el.hidden = (s !== screen);
    });
    window.scrollTo({ top: 0, behavior: 'smooth' });
    if (typeof window['render_' + screen] === 'function') {
      window['render_' + screen]();
    }
  }

  function goNext(from) {
    const idx = FLOW.indexOf(from);
    if (idx >= 0 && idx < FLOW.length - 1) go(FLOW[idx + 1]);
  }

  function goBack(from) {
    const idx = FLOW.indexOf(from);
    if (idx > 0) go(FLOW[idx - 1]);
    else go('landing');
  }

  // Expose for inline onclick handlers in screens.
  window.go = go;
  window.goNext = goNext;
  window.goBack = goBack;

  // --- Boot ------------------------------------------------------------
  document.addEventListener('DOMContentLoaded', () => {
    go('landing');
  });
```

- [ ] **Step 2: Reload the file, check the console**

Run: open the file in a browser, open DevTools.
Expected: no errors. `state` is not visible (it's inside the IIFE), but `go`, `goNext`, `goBack` are on `window`. Test by typing `go('landing')` — nothing visible happens yet because no screen elements exist.

- [ ] **Step 3: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: state + navigation framework"
```

---

## Task 3: Landing screen

Add the first screen — title, intro, "Începe" button.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Add `#screen-landing` to `<main id="app">`**

Insert as the first child of `<main>`:

```html
<section class="screen" id="screen-landing">
  <header class="top">
    <div>
      <h1>Credințe <em>limitative</em></h1>
      <p class="subtitle">Cele 4 întrebări · după Byron Katie</p>
    </div>
    <a class="back-link" href="index.html">← Toate instrumentele</a>
  </header>

  <p style="font-size:15px;color:var(--ink-soft);margin:0 0 16px;">
    Iei o credință care te limitează, o pui sub patru întrebări simple și o întorci pe toate părțile.
    Inspirat de „The Work" — metoda lui Byron Katie.
  </p>
  <p style="font-size:14px;color:var(--ink-mute);margin:0 0 24px;">
    Nimic nu se trimite, nimic nu se salvează. Tot ce scrii rămâne în tabul acesta.
  </p>

  <div class="actions">
    <button class="btn" onclick="goNext('landing')">Începe</button>
  </div>
</section>
```

- [ ] **Step 2: Verify in browser**

Run: reload `credinte-limitative.html`.
Expected: landing screen visible with the title, intro paragraphs, and an "Începe" button. Clicking "Începe" should hide the landing screen (because `go('belief')` runs and only `#screen-belief` would stay visible — which doesn't exist yet, so all screens go hidden and the page shows the empty `.page` container). That's fine for now.

- [ ] **Step 3: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: landing screen"
```

---

## Task 4: Belief capture screen

The user types the limiting belief. This is the only screen besides Q1/Q2 with a hard-required field.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Add `#screen-belief` after `#screen-landing`**

```html
<section class="screen" id="screen-belief" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('belief')">← Înapoi</a></div>

  <h2 class="question">Scrie o credință care te <em>limitează</em>.</h2>
  <p class="helper">Fă-o scurtă, la persoana întâi sau a treia, exact așa cum sună în mintea ta.</p>

  <textarea id="input-belief" placeholder="Ex: „Nu sunt suficient de bun(ă)." aria-label="Credința"></textarea>

  <div class="actions">
    <button class="btn" id="btn-belief-next" disabled>Continuă</button>
  </div>
</section>
```

- [ ] **Step 2: Add the render function inside the IIFE, just above `document.addEventListener('DOMContentLoaded', …)`**

```js
  // --- Belief screen ---------------------------------------------------
  window.render_belief = function() {
    const input = document.getElementById('input-belief');
    const btn = document.getElementById('btn-belief-next');
    input.value = state.belief;
    btn.disabled = state.belief.trim().length === 0;

    input.oninput = () => {
      state.belief = input.value;
      btn.disabled = state.belief.trim().length === 0;
    };
    btn.onclick = () => goNext('belief');
    input.focus();
  };
```

- [ ] **Step 3: Verify**

Reload, click "Începe". The belief screen appears with a disabled Continuă button. Type into the textarea — the button enables. Click ← Înapoi — returns to landing. Click Continuă — goes to the next (empty) screen.

- [ ] **Step 4: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: belief capture screen"
```

---

## Task 5: Q1 and Q2 (Da/Nu + optional note)

Build the binary-choice + note component once, mount it for Q1 and Q2.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Add `#screen-q1` after `#screen-belief`**

```html
<section class="screen" id="screen-q1" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('q1')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-q1"></div>

  <h2 class="question">Întrebarea 1 — <em>E adevărat?</em></h2>

  <div class="choice-row" id="choices-q1">
    <button class="choice" data-value="da">Da</button>
    <button class="choice" data-value="nu">Nu</button>
  </div>

  <div class="field-group">
    <label for="note-q1">Ce ai observat? (opțional)</label>
    <textarea id="note-q1" placeholder="Ce ți-a venit în minte când ai răspuns?" aria-label="Notă pentru Întrebarea 1"></textarea>
  </div>

  <div class="actions">
    <button class="btn" id="btn-q1-next" disabled>Continuă</button>
  </div>
</section>
```

- [ ] **Step 2: Add `#screen-q2` immediately after, with identical structure but `q1` → `q2` and `Întrebarea 1` → `Întrebarea 2 — <em>Pot ști cu absolută certitudine că e adevărat?</em>`**

```html
<section class="screen" id="screen-q2" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('q2')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-q2"></div>

  <h2 class="question">Întrebarea 2 — <em>Pot ști cu absolută certitudine că e adevărat?</em></h2>

  <div class="choice-row" id="choices-q2">
    <button class="choice" data-value="da">Da</button>
    <button class="choice" data-value="nu">Nu</button>
  </div>

  <div class="field-group">
    <label for="note-q2">Ce ai observat? (opțional)</label>
    <textarea id="note-q2" placeholder="Ce ți-a venit în minte când ai răspuns?" aria-label="Notă pentru Întrebarea 2"></textarea>
  </div>

  <div class="actions">
    <button class="btn" id="btn-q2-next" disabled>Continuă</button>
  </div>
</section>
```

- [ ] **Step 3: Add the shared render function for binary-choice screens, inside the IIFE**

```js
  // --- Q1 / Q2 (binary choice + optional note) -------------------------
  function renderBinary(key) {
    const echo = document.getElementById('echo-' + key);
    const choices = document.getElementById('choices-' + key);
    const note = document.getElementById('note-' + key);
    const btn = document.getElementById('btn-' + key + '-next');

    echo.textContent = state.belief ? '„' + state.belief + '"' : '';

    Array.from(choices.children).forEach(b => {
      b.classList.toggle('selected', state[key].answer === b.dataset.value);
      b.onclick = () => {
        state[key].answer = b.dataset.value;
        Array.from(choices.children).forEach(x =>
          x.classList.toggle('selected', x.dataset.value === state[key].answer));
        btn.disabled = state[key].answer === null;
      };
    });

    note.value = state[key].note;
    note.oninput = () => { state[key].note = note.value; };

    btn.disabled = state[key].answer === null;
    btn.onclick = () => goNext(key);
  }

  window.render_q1 = function() { renderBinary('q1'); };
  window.render_q2 = function() { renderBinary('q2'); };
```

- [ ] **Step 4: Verify**

Reload. Type a belief, Continuă. Q1 appears with the belief echoed at top, two large Da/Nu buttons, an optional textarea. Continuă is disabled until you pick. Click Da — it highlights with accent background, Continuă enables. Click Nu — Da deselects, Nu selects. Go ← Înapoi to belief, return — your Da/Nu choice persists. Continuă to Q2 — same behavior, same belief echoed.

- [ ] **Step 5: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: Q1 and Q2 (Da/Nu + note)"
```

---

## Task 6: Q3 and Q4 (free-text)

Two free-text reflection screens. Not required — Continuă is always enabled.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Add `#screen-q3` and `#screen-q4`**

```html
<section class="screen" id="screen-q3" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('q3')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-q3"></div>

  <h2 class="question">Întrebarea 3 — <em>Cum reacționezi, ce se întâmplă, când crezi acel gând?</em></h2>
  <p class="helper">Ce emoții apar? Cum te porți cu tine? Cu ceilalți? Ce imagini, amintiri, ce viitor îți vin?</p>

  <textarea id="input-q3" aria-label="Răspuns la Întrebarea 3"></textarea>

  <div class="actions">
    <button class="btn" onclick="goNext('q3')">Continuă</button>
  </div>
</section>

<section class="screen" id="screen-q4" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('q4')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-q4"></div>

  <h2 class="question">Întrebarea 4 — <em>Cine ai fi fără acel gând?</em></h2>
  <p class="helper">Imaginează-te în aceeași situație, dar fără credința aceasta. Cine ești?</p>

  <textarea id="input-q4" aria-label="Răspuns la Întrebarea 4"></textarea>

  <div class="actions">
    <button class="btn" onclick="goNext('q4')">Continuă</button>
  </div>
</section>
```

- [ ] **Step 2: Add the shared render function**

```js
  // --- Q3 / Q4 (free-text) ---------------------------------------------
  function renderFreeText(key) {
    const echo = document.getElementById('echo-' + key);
    const input = document.getElementById('input-' + key);
    echo.textContent = state.belief ? '„' + state.belief + '"' : '';
    input.value = state[key];
    input.oninput = () => { state[key] = input.value; };
  }

  window.render_q3 = function() { renderFreeText('q3'); };
  window.render_q4 = function() { renderFreeText('q4'); };
```

- [ ] **Step 3: Verify**

Reload, walk through landing → belief → Q1 → Q2 → Q3 → Q4. The belief echoes at top of each Q. Q3/Q4 textareas accept text. State persists when you go back and forward. Continuă always advances (no gating).

- [ ] **Step 4: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: Q3 and Q4 (free-text)"
```

---

## Task 7: Turnarounds (Răsturnări 1, 2, 3)

Three turnaround screens, each with one statement field + three example fields. Same component, three mounts. Not required.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Add `#screen-ta1`**

```html
<section class="screen" id="screen-ta1" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('ta1')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-ta1"></div>

  <h2 class="question">Răsturnarea 1 — <em>la opus</em></h2>
  <div class="ta-explainer">
    Inversează credința. Ex: <em>„Nu sunt suficient de bun" → „Sunt suficient de bun."</em>
  </div>

  <div class="field-group">
    <label for="stmt-ta1">Răsturnarea</label>
    <textarea id="stmt-ta1" placeholder="Scrie credința inversată." aria-label="Răsturnarea 1"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta1-0">Exemplu 1 — unde e la fel de adevărat sau mai adevărat?</label>
    <textarea id="ex-ta1-0" aria-label="Exemplu 1 pentru Răsturnarea 1"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta1-1">Exemplu 2</label>
    <textarea id="ex-ta1-1" aria-label="Exemplu 2 pentru Răsturnarea 1"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta1-2">Exemplu 3</label>
    <textarea id="ex-ta1-2" aria-label="Exemplu 3 pentru Răsturnarea 1"></textarea>
  </div>

  <div class="actions">
    <button class="btn" onclick="goNext('ta1')">Continuă</button>
  </div>
</section>
```

- [ ] **Step 2: Add `#screen-ta2`**

Same shape as `#screen-ta1`, with these substitutions: all `ta1` → `ta2`, title to `Răsturnarea 2 — <em>la sine</em>`, explainer to:

```html
<div class="ta-explainer">
  Întoarce credința către tine. Ex: <em>„El nu mă respectă" → „Eu nu mă respect (în această situație)."</em>
</div>
```

(Repeat the full screen markup with the substitutions — do not abbreviate. The executor reads tasks linearly.)

```html
<section class="screen" id="screen-ta2" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('ta2')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-ta2"></div>

  <h2 class="question">Răsturnarea 2 — <em>la sine</em></h2>
  <div class="ta-explainer">
    Întoarce credința către tine. Ex: <em>„El nu mă respectă" → „Eu nu mă respect (în această situație)."</em>
  </div>

  <div class="field-group">
    <label for="stmt-ta2">Răsturnarea</label>
    <textarea id="stmt-ta2" placeholder="Scrie credința întoarsă către tine." aria-label="Răsturnarea 2"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta2-0">Exemplu 1 — unde e la fel de adevărat sau mai adevărat?</label>
    <textarea id="ex-ta2-0" aria-label="Exemplu 1 pentru Răsturnarea 2"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta2-1">Exemplu 2</label>
    <textarea id="ex-ta2-1" aria-label="Exemplu 2 pentru Răsturnarea 2"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta2-2">Exemplu 3</label>
    <textarea id="ex-ta2-2" aria-label="Exemplu 3 pentru Răsturnarea 2"></textarea>
  </div>

  <div class="actions">
    <button class="btn" onclick="goNext('ta2')">Continuă</button>
  </div>
</section>
```

- [ ] **Step 3: Add `#screen-ta3`**

```html
<section class="screen" id="screen-ta3" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('ta3')">← Înapoi</a></div>

  <div class="belief-echo" id="echo-ta3"></div>

  <h2 class="question">Răsturnarea 3 — <em>la celălalt</em></h2>
  <div class="ta-explainer">
    Întoarce credința către celălalt sau către situație. Ex: <em>„El nu mă respectă" → „Eu nu îl respect."</em>
    Dacă credința nu privește o altă persoană, întoarce-o către situație sau viață.
  </div>

  <div class="field-group">
    <label for="stmt-ta3">Răsturnarea</label>
    <textarea id="stmt-ta3" placeholder="Scrie credința întoarsă către celălalt sau către situație." aria-label="Răsturnarea 3"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta3-0">Exemplu 1 — unde e la fel de adevărat sau mai adevărat?</label>
    <textarea id="ex-ta3-0" aria-label="Exemplu 1 pentru Răsturnarea 3"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta3-1">Exemplu 2</label>
    <textarea id="ex-ta3-1" aria-label="Exemplu 2 pentru Răsturnarea 3"></textarea>
  </div>
  <div class="field-group">
    <label for="ex-ta3-2">Exemplu 3</label>
    <textarea id="ex-ta3-2" aria-label="Exemplu 3 pentru Răsturnarea 3"></textarea>
  </div>

  <div class="actions">
    <button class="btn" onclick="goNext('ta3')">Continuă</button>
  </div>
</section>
```

- [ ] **Step 4: Add the shared render function for turnarounds**

```js
  // --- Turnarounds -----------------------------------------------------
  function renderTurnaround(key) {
    const echo = document.getElementById('echo-' + key);
    const stmt = document.getElementById('stmt-' + key);
    echo.textContent = state.belief ? '„' + state.belief + '"' : '';

    stmt.value = state[key].statement;
    stmt.oninput = () => { state[key].statement = stmt.value; };

    for (let i = 0; i < 3; i++) {
      const ex = document.getElementById('ex-' + key + '-' + i);
      ex.value = state[key].examples[i];
      ex.oninput = () => { state[key].examples[i] = ex.value; };
    }
  }

  window.render_ta1 = function() { renderTurnaround('ta1'); };
  window.render_ta2 = function() { renderTurnaround('ta2'); };
  window.render_ta3 = function() { renderTurnaround('ta3'); };
```

- [ ] **Step 5: Verify**

Walk through landing → belief → Q1 → Q2 → Q3 → Q4 → Răsturnare 1 → Răsturnare 2 → Răsturnare 3. Belief echoes at top of each. Type into the statement and example fields — values persist when navigating back and forward.

- [ ] **Step 6: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: turnarounds (3 screens)"
```

---

## Task 8: Rezumat screen (summary + Nume/Data + render)

The final screen aggregates all state into a readable worksheet. Nume + Data inputs go in the header. Adds the Rezumat-specific CSS.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Add Rezumat-specific CSS to the `<style>` block (just before `/* Rezumat blocks + turnaround render — added in Task 8 */`)**

```css
  /* Rezumat */
  .summary-block { margin-bottom: 20px; }
  .summary-block .q-label {
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.12em;
    color: var(--accent);
    margin: 0 0 4px;
  }
  .summary-block .q-text {
    font-family: 'Fraunces', serif;
    font-size: 17px;
    color: var(--ink);
    margin: 0 0 8px;
    font-weight: 400;
  }
  .summary-block .q-text em { color: var(--accent); font-style: italic; }
  .answer-pill {
    display: inline-block;
    font-size: 12px;
    background: var(--paper);
    color: var(--accent-deep);
    padding: 3px 12px;
    border-radius: 999px;
    border: 0.5px solid var(--paper-deep);
    margin-right: 8px;
    font-weight: 500;
  }
  .summary-note {
    font-size: 14px;
    color: var(--ink-soft);
    line-height: 1.55;
    margin: 4px 0 0;
  }
  .summary-ta {
    margin-top: 22px;
    padding-top: 18px;
    border-top: 0.5px solid var(--paper-deep);
  }
  .summary-ta h3 {
    font-family: 'Fraunces', serif;
    font-weight: 300;
    font-size: 19px;
    margin: 0 0 8px;
  }
  .summary-ta h3 em { color: var(--accent); font-style: italic; }
  .summary-ta-stmt {
    font-family: 'Fraunces', serif;
    font-style: italic;
    color: var(--accent-deep);
    font-size: 16px;
    margin: 0 0 8px;
  }
  .summary-ta-ex {
    font-size: 14px;
    color: var(--ink-soft);
    padding-left: 14px;
    border-left: 1.5px solid var(--paper-deep);
    margin: 6px 0;
  }
```

- [ ] **Step 2: Add `#screen-summary` after `#screen-ta3`**

```html
<section class="screen" id="screen-summary" hidden>
  <div class="back-row"><a class="back-link" href="#" onclick="event.preventDefault();goBack('summary')">← Înapoi</a></div>

  <header class="top">
    <div>
      <h1>Credințe <em>limitative</em></h1>
      <p class="subtitle">Rezumat · după Byron Katie</p>
    </div>
    <div class="session-info">
      <label for="input-name" class="sr-only">Nume</label>
      <input type="text" id="input-name" placeholder="Nume" autocomplete="off" aria-label="Nume">
      <label for="input-date" class="sr-only">Data</label>
      <input type="date" id="input-date" aria-label="Data">
    </div>
  </header>

  <div id="summary-body"></div>

  <div class="actions">
    <button class="btn ghost" id="btn-restart">Începe din nou</button>
    <button class="btn" id="btn-print">Salvează PDF</button>
  </div>
</section>
```

- [ ] **Step 3: Add the render function inside the IIFE**

```js
  // --- Rezumat ---------------------------------------------------------
  function esc(s) {
    return String(s)
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;');
  }

  function answerPill(answer) {
    if (answer === 'da') return '<span class="answer-pill">Da</span>';
    if (answer === 'nu') return '<span class="answer-pill">Nu</span>';
    return '';
  }

  function noteHtml(note) {
    const n = note.trim();
    return n ? '<p class="summary-note">' + esc(n) + '</p>' : '';
  }

  function freeTextHtml(text) {
    const t = text.trim();
    return t ? '<p class="summary-note">' + esc(t) + '</p>' : '';
  }

  function turnaroundHtml(ta, title) {
    const stmt = ta.statement.trim();
    const exs = ta.examples.map(e => e.trim()).filter(Boolean);
    if (!stmt && exs.length === 0) return '';  // skip entirely if empty
    let html = '<div class="summary-ta"><h3>' + title + '</h3>';
    if (stmt) html += '<p class="summary-ta-stmt">„' + esc(stmt) + '"</p>';
    exs.forEach(ex => {
      html += '<p class="summary-ta-ex">' + esc(ex) + '</p>';
    });
    html += '</div>';
    return html;
  }

  window.render_summary = function() {
    // Wire inputs to state
    const nameInput = document.getElementById('input-name');
    const dateInput = document.getElementById('input-date');
    nameInput.value = state.name;
    dateInput.value = state.date;
    nameInput.oninput = () => { state.name = nameInput.value; };
    dateInput.onchange = () => { state.date = dateInput.value; };

    // Render body
    const body = document.getElementById('summary-body');
    let html = '';
    if (state.belief.trim()) {
      html += '<div class="belief-echo">„' + esc(state.belief.trim()) + '"</div>';
    }
    html += '<div class="summary-block">'
      + '<p class="q-label">Întrebarea 1</p>'
      + '<p class="q-text">E adevărat?</p>'
      + answerPill(state.q1.answer)
      + noteHtml(state.q1.note)
      + '</div>';
    html += '<div class="summary-block">'
      + '<p class="q-label">Întrebarea 2</p>'
      + '<p class="q-text">Pot ști cu absolută certitudine că e adevărat?</p>'
      + answerPill(state.q2.answer)
      + noteHtml(state.q2.note)
      + '</div>';
    html += '<div class="summary-block">'
      + '<p class="q-label">Întrebarea 3</p>'
      + '<p class="q-text">Cum reacționez când cred acest gând?</p>'
      + freeTextHtml(state.q3)
      + '</div>';
    html += '<div class="summary-block">'
      + '<p class="q-label">Întrebarea 4</p>'
      + '<p class="q-text">Cine aș fi fără acest gând?</p>'
      + freeTextHtml(state.q4)
      + '</div>';
    html += turnaroundHtml(state.ta1, 'Răsturnarea 1 — <em>la opus</em>');
    html += turnaroundHtml(state.ta2, 'Răsturnarea 2 — <em>la sine</em>');
    html += turnaroundHtml(state.ta3, 'Răsturnarea 3 — <em>la celălalt</em>');
    body.innerHTML = html;

    // Wire buttons
    document.getElementById('btn-print').onclick = () => window.print();
    document.getElementById('btn-restart').onclick = () => {
      resetState();
      go('landing');
    };
  };
```

- [ ] **Step 4: Verify**

Walk through the full flow with some filled and some empty fields. Reach Rezumat. Check:
- Belief echoed at top.
- Q1, Q2 show pill + note (or no note if empty).
- Q3, Q4 show paragraph (or nothing if empty).
- Each turnaround shown only if it has a statement or at least one example. Empty examples are omitted.
- Nume input is empty by default with placeholder "Nume"; Data input shows today's date.
- "Începe din nou" returns to landing with all fields cleared.

- [ ] **Step 5: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: Rezumat screen + Nume/Data + render"
```

---

## Task 9: Print / PDF styles

`@media print` hides nav and other screens, expands the Rezumat into A4-friendly worksheet, formats the date in Romanian, adds footer.

**Files:**
- Modify: `credinte-limitative.html`

- [ ] **Step 1: Replace the comment `/* Print styles — added in Task 9 */` with the print block**

```css
  /* Print / PDF antet for Rezumat */
  .pdf-header {
    display: none;
    margin: 0 0 18px;
    font-size: 13px;
    color: var(--ink-soft);
  }
  .pdf-header strong { color: var(--ink); font-weight: 500; }

  .pdf-footer {
    display: none;
    text-align: center;
    font-size: 10px;
    color: var(--ink-mute);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-top: 28px;
    padding-top: 14px;
    border-top: 0.5px solid var(--paper-deep);
  }

  @media print {
    @page { margin: 18mm 16mm; }
    body { background: #fff; font-size: 11pt; }
    .page { max-width: 100%; padding: 0; }
    .screen {
      background: #fff;
      border: none;
      box-shadow: none;
      padding: 0;
      margin: 0;
    }
    /* Hide everything except summary */
    #screen-landing, #screen-belief,
    #screen-q1, #screen-q2, #screen-q3, #screen-q4,
    #screen-ta1, #screen-ta2, #screen-ta3 { display: none !important; }
    #screen-summary { display: block !important; }
    #screen-summary[hidden] { display: block !important; }

    /* Hide back-link, session inputs (replaced by pdf-header), actions */
    #screen-summary .back-row,
    #screen-summary .session-info,
    #screen-summary .actions { display: none !important; }

    /* Show PDF header + footer */
    .pdf-header { display: block !important; }
    .pdf-footer { display: block !important; }

    /* Page-break tuning */
    .summary-block, .summary-ta { page-break-inside: avoid; }
    h1, h3 { page-break-after: avoid; }

    /* Colors print better with a light touch */
    .belief-echo { color: #6e3a14; border-color: #c9b894; }
    .answer-pill { border-color: #c9b894; background: #f6ecd5; }
    .summary-ta-ex { border-left-color: #c9b894; }
    h1 em, h3 em, .q-text em, .summary-ta-stmt { color: #6e3a14; }
  }
```

- [ ] **Step 2: Add `.pdf-header` and `.pdf-footer` to the Rezumat DOM**

In `#screen-summary`, insert just after the `<header class="top">…</header>`:

```html
<div class="pdf-header" id="pdf-header"></div>
```

And just before the closing `</section>` (after `.actions`):

```html
<div class="pdf-footer">Credințe limitative · după Byron Katie · anabrad.github.io/instrumente-coaching</div>
```

- [ ] **Step 3: Render the PDF header from state at print time**

Inside `window.render_summary`, after wiring the inputs but before computing `html`, add:

```js
    // PDF antet (only visible in print)
    const fmtDate = (iso) => {
      try {
        const [y, m, d] = iso.split('-').map(Number);
        return new Intl.DateTimeFormat('ro-RO', {
          day: 'numeric', month: 'long', year: 'numeric'
        }).format(new Date(y, m - 1, d));
      } catch (e) { return iso; }
    };
    const pdfHeader = document.getElementById('pdf-header');
    const name = state.name.trim();
    const dateStr = fmtDate(state.date);
    pdfHeader.innerHTML = name
      ? '<strong>' + esc(name) + '</strong> &nbsp;·&nbsp; ' + esc(dateStr)
      : esc(dateStr);
```

Also update `nameInput.oninput` and `dateInput.onchange` to re-render the PDF header live (so the user sees it correctly when they hit the print dialog):

Replace:
```js
    nameInput.oninput = () => { state.name = nameInput.value; };
    dateInput.onchange = () => { state.date = dateInput.value; };
```

With:
```js
    const updateHeader = () => {
      const n = state.name.trim();
      const d = fmtDate(state.date);
      pdfHeader.innerHTML = n
        ? '<strong>' + esc(n) + '</strong> &nbsp;·&nbsp; ' + esc(d)
        : esc(d);
    };
    nameInput.oninput = () => { state.name = nameInput.value; updateHeader(); };
    dateInput.onchange = () => { state.date = dateInput.value; updateHeader(); };
```

- [ ] **Step 4: Verify**

Walk through the full flow with realistic content in all fields. On Rezumat, click "Salvează PDF". The browser print dialog opens. Change destination to "Save as PDF" and preview:
- No nav/back-links/buttons.
- Title at top with "Credințe *limitative*" and "Rezumat · după Byron Katie".
- PDF header line: `Ana-Maria · 27 mai 2026` (or whatever the user entered/today).
- All four questions and three turnarounds laid out cleanly.
- Footer at bottom.
- Page breaks don't split blocks in half (test by filling lots of text and checking the second page).
- Also test with empty Nume — header should show just the date.

- [ ] **Step 5: Commit (ask user)**

```bash
git add credinte-limitative.html
git commit -m "credinte-limitative: print styles + PDF antet"
```

---

## Task 10: Wire it into `index.html`

Add the card, soften the lead copy, update meta description and OG/Twitter descriptions to match.

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Reframe the lead — drop duration and the privacy tagline**

Reason: the collection now includes a longer instrument (this one) and may include a future app that requires login. The current second sentence ("Câte 10 minute, fără cont, fără date trimise.") claims things that will become inaccurate. Replace it with an audience/use-case sentence that holds up.

In `index.html`, find the `<p class="lead">` body text:

```
"Instrumente vizuale pentru sesiuni de coaching. Câte 10 minute, fără cont, fără date trimise."
```

Replace with:

```
"Instrumente vizuale pentru sesiuni de coaching. Pentru sesiuni 1:1 sau pentru lucrul cu sine."
```

Then update the three meta description tags. Current `<meta name="description">` is:

```
"Instrumente vizuale pentru sesiuni de coaching — WOOP, GROW, Valorile personale și 4 Roți (Vieții, Vieții adolescenți, Încrederii, Emoțiilor). Câte 10 minute, fără cont, fără date trimise."
```

Replace with:

```
"Instrumente vizuale pentru sesiuni de coaching — WOOP, GROW, Valorile personale, 4 Roți (Vieții, Vieții adolescenți, Încrederii, Emoțiilor) și lucrul cu credințele limitative. Pentru sesiuni 1:1 sau pentru lucrul cu sine."
```

Current `<meta property="og:description">` and `<meta name="twitter:description">` are:

```
"Instrumente vizuale pentru sesiuni de coaching. Câte 10 minute, fără cont, fără date trimise."
```

Replace both with:

```
"Instrumente vizuale pentru sesiuni de coaching. Pentru sesiuni 1:1 sau pentru lucrul cu sine."
```

- [ ] **Step 2: Add the new card between Roata Încrederii and Roata Emoțiilor**

Find this block (around line 210 in `index.html`):

```html
<a class="card" href="roata-emotiilor.html">
  <div class="card-label">Adulți & adolescenți · emoții</div>
  <h2>Roata <em>Emoțiilor</em></h2>
```

Insert immediately before it:

```html
<a class="card" href="credinte-limitative.html">
  <div class="card-label">Adulți · credințe limitative</div>
  <h2>Credințe <em>limitative</em></h2>
  <p>Patru întrebări după Byron Katie. Iei o credință care te limitează, o cercetezi în liniște și o întorci pe toate părțile. Util când anumite gânduri se simt adevărate doar pentru că revin des.</p>
  <span class="card-cta">Deschide <span class="arrow">→</span></span>
</a>
```

- [ ] **Step 3: Verify**

Reload `index.html`. Eight cards visible. The new Credințe limitative card is between Roata Încrederii and Roata Emoțiilor. Lead text reads "Pentru sesiuni 1:1 sau pentru lucrul cu sine." Click the new card — opens `credinte-limitative.html` correctly. Use the "← Toate instrumentele" link on its landing — returns to index.

- [ ] **Step 4: Commit (ask user)**

```bash
git add index.html
git commit -m "index: add Credințe limitative card; soften lead copy"
```

---

## Task 11: Final QA walkthrough

End-to-end manual test. No code changes unless something breaks.

- [ ] **Step 1: Fresh-tab walkthrough**

Open `index.html` in a fresh browser tab. Click the Credințe limitative card. Walk all 10 screens, filling every field. Verify:
- All Continuă buttons enable/disable per spec (belief required; Q1/Q2 require a Da/Nu; Q3/Q4/turnarounds advance freely).
- Back-link returns to the previous screen with state intact at every step.
- Belief echoes correctly at the top of Q1, Q2, Q3, Q4, and all three turnarounds.
- Rezumat shows the belief, all four Q answers (pills + notes), and all three turnarounds with examples.

- [ ] **Step 2: Skip-fields walkthrough**

Restart. Fill only the required fields (belief + Q1 answer + Q2 answer). Leave everything else empty. Reach Rezumat. Verify:
- Q1 and Q2 show pills with no notes.
- Q3, Q4, and all turnarounds blocks are either omitted or render nothing — no orphan headings, no empty bullets.

- [ ] **Step 3: PDF walkthrough**

With filled content: click Salvează PDF. In the browser dialog, choose "Save as PDF". Inspect the result:
- Title and subtitle render correctly.
- PDF header line shows `Nume · Data` (or just date if Nume empty).
- All content is on the page; no clipped text.
- No back-links, no buttons, no other screens.
- Page breaks fall between blocks, not inside them.

- [ ] **Step 4: Începe din nou**

On Rezumat, click "Începe din nou". Verify: returns to landing, all fields cleared, Data input resets to today, Nume empty.

- [ ] **Step 5: Keyboard + accessibility spot-checks**

- Tab through the belief screen — focus ring visible on textarea and button.
- On Q1/Q2 — Tab reaches both Da and Nu buttons; Enter activates the focused button.
- Click outside any input — no JS errors in console at any point during the full flow.

- [ ] **Step 6: Mobile viewport check**

In DevTools, switch to iPhone 12 viewport (~390px wide). Verify all screens are usable: no horizontal scroll, buttons fit, textareas are reasonable size, Nume/Data inputs stack instead of overflowing.

- [ ] **Step 7: Final commit if anything was fixed (ask user)**

If any fixes were applied during QA:

```bash
git add credinte-limitative.html index.html
git commit -m "credinte-limitative: QA fixes"
```

---

## Out of scope (do not implement)

- localStorage / draft saving
- Adolescent variant
- Analytics / tracking
- Share / copy-as-text button
- JYN worksheet entry point
- Language toggle

## Notes

- **Reference files** (use as canonical examples when uncertain): `modelul-woop.html` for the screen + navigation pattern; `roata-vietii.html` for `.session-info` and PDF antet formatting.
- **Commits**: per project policy, the executor should ask the user before each `git commit`. The commands above are suggested messages and timing; the user decides whether to run them or batch.
- **Favicon**: the Task 1 favicon is a placeholder ("4?" italic). If it reads poorly at 16px during QA, replace with concentric rings (4 nested rings) using the same paper palette — but only if needed.
