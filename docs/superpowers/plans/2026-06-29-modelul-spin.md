# Modelul SPIN Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `modelul-spin.html`, a team-coaching tool that walks a team through one shared challenge using Neil Rackham's SPIN questions (Situație, Problemă, Implicații, Nevoie și câștig), and add its card to the Romanian hub.

**Architecture:** Single self-contained HTML file built by copying `modelul-grpi.html` and adapting it. Reuse GRPI's machinery verbatim (CSS base, localStorage sessions, sessions dropdown, PDF export, `customConfirm`). Change: meta/head, header, a new challenge field with clickable example chips, the four SPIN step cards (scales only on Implicații and Nevoie), and the JS data model.

**Tech Stack:** Plain HTML/CSS/JS, no build. Fonts Fraunces + Inter. html2canvas + jsPDF (lazy-loaded via CDN, already wired in GRPI). Paper-cognac palette.

**Reference files (read before starting):**
- `modelul-grpi.html` — the template to copy and adapt (closest analog)
- `index.html` — the RO hub, where the new card goes
- `docs/superpowers/specs/2026-06-29-modelul-spin-design.md` — the approved spec

**Note on verification:** There is no test runner in this project. "Verify" means opening the file in a browser (`open modelul-spin.html` on macOS) and checking behavior, plus structural `grep` checks. Run from the project root: `/Users/ana/Claude/aplicatii create cu Claude/instrumente-coaching`.

---

### Task 1: Scaffold modelul-spin.html from GRPI (head, header, meta)

**Files:**
- Create: `modelul-spin.html` (copy of `modelul-grpi.html`, then adapt)

- [ ] **Step 1: Copy the GRPI file as the starting point**

```bash
cp modelul-grpi.html modelul-spin.html
```

- [ ] **Step 2: Replace the `<head>` SEO/meta block**

Replace lines 6–27 of `modelul-spin.html` (the `<title>` through the `x-default` `<link>`) with:

```html
<title>Modelul SPIN — Instrument de coaching</title>
<meta name="description" content="Instrument de coaching pe modelul SPIN (Situație, Problemă, Implicații, Nevoie și câștig) după Neil Rackham. O conversație de echipă pe o singură provocare. Creat de Ana-Maria Brad.">
<meta name="author" content="Ana-Maria Brad">

<!-- Open Graph -->
<meta property="og:type" content="website">
<meta property="og:title" content="Modelul SPIN — Instrument de coaching">
<meta property="og:description" content="O conversație de echipă pe o singură provocare: Situație, Problemă, Implicații, Nevoie și câștig. După Neil Rackham.">
<meta property="og:url" content="https://anabrad.github.io/instrumente-coaching/modelul-spin.html">
<meta property="og:locale" content="ro_RO">
<meta property="og:image" content="https://anabrad.github.io/instrumente-coaching/og-cover.png">

<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Modelul SPIN — Instrument de coaching">
<meta name="twitter:description" content="O conversație de echipă pe o singură provocare: Situație, Problemă, Implicații, Nevoie și câștig.">
<meta name="twitter:image" content="https://anabrad.github.io/instrumente-coaching/og-cover.png">

<link rel="canonical" href="https://anabrad.github.io/instrumente-coaching/modelul-spin.html">
<link rel="alternate" hreflang="ro" href="https://anabrad.github.io/instrumente-coaching/modelul-spin.html">
<link rel="alternate" hreflang="en" href="https://anabrad.github.io/instrumente-coaching/en/spin-model.html">
<link rel="alternate" hreflang="x-default" href="https://anabrad.github.io/instrumente-coaching/modelul-spin.html">
```

- [ ] **Step 3: Replace the favicon (4 letters S/P/I/N in quadrants)**

Replace the favicon `<link>` (the `data:image/svg+xml` line, GRPI line 30) with:

```html
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='46' fill='%23f1e6d2' stroke='%23a05a25' stroke-width='2'/%3E%3Cg transform='translate(50,50)' font-family='serif' font-style='italic' font-size='22' fill='%23a05a25' text-anchor='middle' dominant-baseline='central'%3E%3Ctext x='-18' y='-14'%3ES%3C/text%3E%3Ctext x='18' y='-14'%3EP%3C/text%3E%3Ctext x='-18' y='18'%3EI%3C/text%3E%3Ctext x='18' y='18'%3EN%3C/text%3E%3C/g%3E%3Cline x1='50' y1='10' x2='50' y2='90' stroke='%23a05a25' stroke-width='0.5'/%3E%3Cline x1='10' y1='50' x2='90' y2='50' stroke='%23a05a25' stroke-width='0.5'/%3E%3C/svg%3E">
```

- [ ] **Step 4: Update the lang-toggle and header**

Replace the lang-toggle nav (GRPI lines 797–800) with:

```html
<nav class="lang-toggle" aria-label="Limbă">
  <a href="modelul-spin.html" aria-current="page">🇷🇴 Română</a>
  <a href="en/spin-model.html">🇬🇧 English</a>
</nav>
```

Replace the `<h1>` + subtitle inside `header.top` (GRPI lines 804–805) with:

```html
      <h1>Modelul <em>SPIN</em></h1>
      <p class="subtitle">Instrument de coaching · după Neil Rackham</p>
```

The "Nume echipă" / date inputs in `.session-info` stay unchanged (team tool, same as GRPI).

- [ ] **Step 5: Verify head/header**

```bash
grep -c "Modelul <em>SPIN</em>" modelul-spin.html   # expect 1
grep -c "după Neil Rackham" modelul-spin.html        # expect 1 (subtitle)
grep -c "modelul-grpi" modelul-spin.html             # expect 0 (no leftover self-refs in head/header)
```
Expected: `1`, `1`, `0`. (Note: `modelul-grpi.html` is still referenced later in the next-step CTA — that grep is run before Task 5 adds it, so 0 here is correct. If non-zero, find stray GRPI head/header references and fix.)

Open `modelul-spin.html` in a browser: tab title reads "Modelul SPIN", header shows "Modelul SPIN · după Neil Rackham", favicon shows S/P/I/N.

- [ ] **Step 6: Commit**

```bash
git add modelul-spin.html
git commit -m "Scaffold modelul-spin.html from GRPI (head, header, meta)"
```

---

### Task 2: Intro + challenge field with clickable example chips

**Files:**
- Modify: `modelul-spin.html` (CSS in `<style>`, body markup, intro)

- [ ] **Step 1: Add CSS for the challenge field and example chips**

In the `<style>` block, immediately after the `.intro` / `.privacy-note` rules (GRPI lines 142–158), add:

```css
  /* Challenge field + example chips */
  .challenge-field {
    width: 100%;
    padding: 12px 14px;
    font-size: 15px;
    border: 0.5px solid var(--paper-deep);
    border-radius: var(--radius);
    background: var(--paper);
    color: var(--ink);
    font-family: inherit;
  }
  .challenge-field:focus { outline: none; border-color: var(--accent); }
  .examples-hint {
    font-size: 12px;
    color: var(--ink-mute);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin: 14px 0 8px;
  }
  .examples { display: flex; flex-wrap: wrap; gap: 8px; }
  .example-chip {
    padding: 7px 14px;
    font-family: inherit;
    font-size: 13px;
    background: transparent;
    border: 0.5px solid var(--paper-deep);
    border-radius: 999px;
    color: var(--ink-soft);
    cursor: pointer;
    transition: all 0.15s;
  }
  .example-chip:hover { border-color: var(--accent); color: var(--accent); }
```

- [ ] **Step 2: Replace the intro paragraph**

Replace the `.intro` paragraph (GRPI lines 815–817) with:

```html
  <p class="intro">
    <strong>O conversație structurată pe o singură provocare.</strong> SPIN vine de la Situație, Problemă, Implicații, Nevoie și câștig: patru tipuri de întrebări, după Neil Rackham, care duc echipa de la fapte la o nevoie resimțită împreună, înainte de a sări la soluții.
  </p>
```

- [ ] **Step 3: Add the challenge card immediately after the intro**

Right after the `.intro` paragraph (and before the `details.about` block), insert:

```html
  <div class="step">
    <div class="step-header">
      <h2 class="step-title">Provocarea pe care o explorăm</h2>
    </div>
    <p class="step-lead">O singură provocare per sesiune. SPIN lucrează în adâncime pe un subiect, nu pe toate deodată.</p>
    <label for="challenge" class="sr-only">Provocarea pe care o explorăm</label>
    <input type="text" id="challenge" class="challenge-field" placeholder="Scrie aici subiectul pe care îl ia echipa, pe scurt..." autocomplete="off">
    <div class="examples-hint">Exemple (apasă ca să completezi):</div>
    <div class="examples" id="examples">
      <button type="button" class="example-chip" data-full="Ședințele noastre durează mult și se termină fără decizii">Ședințe lungi, fără decizii</button>
      <button type="button" class="example-chip" data-full="Ratăm termene și nu e clar de ce">Ratăm termene</button>
      <button type="button" class="example-chip" data-full="Două părți ale echipei nu comunică bine">Două părți nu comunică</button>
      <button type="button" class="example-chip" data-full="Ne ferim să ne dăm feedback unii altora">Ne ferim de feedback</button>
      <button type="button" class="example-chip" data-full="Munca e împărțită inegal și unii sunt suprasolicitați">Muncă împărțită inegal</button>
      <button type="button" class="example-chip" data-full="După reorganizare, moralul a scăzut">Moral scăzut după reorganizare</button>
    </div>
  </div>
```

- [ ] **Step 4: Verify markup (chip wiring comes in Task 6)**

```bash
grep -c 'id="challenge"' modelul-spin.html        # expect 1
grep -c 'class="example-chip"' modelul-spin.html  # expect 6
```
Expected: `1`, `6`. Open in browser: the challenge card shows the input + six chips under "Exemple". Chips do nothing yet (handler added in Task 6).

- [ ] **Step 5: Commit**

```bash
git add modelul-spin.html
git commit -m "Add SPIN intro, challenge field and example chips markup"
```

---

### Task 3: Facilitation guide (Despre exercițiu)

**Files:**
- Modify: `modelul-spin.html` (the `details.about` block)

- [ ] **Step 1: Replace the About block content**

Replace the entire `details.about` block (GRPI lines 819–835) with:

```html
  <details class="about">
    <summary>Despre exercițiu și ghid de facilitare</summary>
    <div class="about-body">
      <p>SPIN vine de la Situation, Problem, Implication, Need-payoff: Situație, Problemă, Implicații, Nevoie și câștig. Sunt cele patru tipuri de întrebări descrise de Neil Rackham, apărute inițial în vânzarea consultativă și folosite aici pentru a ajuta o echipă să-și înțeleagă singură o provocare, înainte de a căuta soluții.</p>

      <h3>De ce contează ordinea</h3>
      <p>Nu propui soluții până când echipa nu simte nevoia. Întrebările de Situație și Problemă pregătesc terenul, dar cele care mută cu adevărat echipa sunt Implicațiile (ce ne costă dacă nu schimbăm nimic) și Nevoia și câștigul (ce devine posibil dacă rezolvăm). Răspunsurile trebuie să vină de la echipă, nu de la facilitator.</p>

      <h3>Ghid de facilitare</h3>
      <ul>
        <li>Alegeți o singură provocare. SPIN lucrează în adâncime pe un subiect, nu pe toate deodată.</li>
        <li>Stați la Situație până faptele sunt aceleași pentru toți. Abia apoi treceți la Problemă.</li>
        <li>Implicațiile sunt inima conversației. Lăsați echipa să simtă costul, nu vă grăbiți spre soluții.</li>
        <li>Nevoia și câștigul vin de la echipă. Întrebați „ce v-ar aduce”, nu „de ce ar trebui”.</li>
        <li>Închideți cu un singur pas mic, asumat de cineva anume.</li>
      </ul>
    </div>
  </details>
```

The `.privacy-note` paragraph right after it stays unchanged.

- [ ] **Step 2: Verify**

```bash
grep -c "Despre exercițiu și ghid de facilitare" modelul-spin.html  # expect 1
grep -c "Ghid de facilitare" modelul-spin.html                       # expect 1
```
Expected: `1`, `1`. Open in browser: clicking the "Despre" summary expands the guide; `+` toggles to `−`.

- [ ] **Step 3: Commit**

```bash
git add modelul-spin.html
git commit -m "Add SPIN facilitation guide content"
```

---

### Task 4: The four SPIN step cards (S, P, I, N)

**Files:**
- Modify: `modelul-spin.html` (replace the four GRPI step cards)

- [ ] **Step 1: Replace all four GRPI step cards with the four SPIN cards**

Replace the four GRPI step `<div class="step">` blocks (the G, R, P, I cards — GRPI lines 839–929, from `<!-- G -->` through the closing `</div>` of the Interpersonal card) with the following. Note: only Implicații and Nevoie have a `.score-block`.

```html
  <!-- S -->
  <div class="step">
    <div class="step-header">
      <span class="step-letter">S</span>
      <h2 class="step-title">Situație</h2>
    </div>
    <p class="step-lead">Faptele, fără interpretări. Unde se află echipa acum, concret, în legătură cu provocarea aleasă.</p>
    <div class="key-q">Care e situația, așa cum o vedem cu toții, fără să o explicăm încă?</div>
    <div class="extra-label">Întrebări de explorare</div>
    <ul class="prompts">
      <li>Ce se întâmplă concret acum, în exemple sau cifre, nu în păreri?</li>
      <li>De cât timp e așa și ce s-a încercat până acum?</li>
      <li>Ce e deja stabilit și nu mai e de discutat despre situație?</li>
      <li>Vedem cu toții aceleași fapte, sau avem imagini diferite?</li>
    </ul>
    <textarea id="text-situation" aria-label="Note Situație" placeholder="Note, observații, exemple..."></textarea>
  </div>

  <!-- P -->
  <div class="step">
    <div class="step-header">
      <span class="step-letter">P</span>
      <h2 class="step-title">Problemă</h2>
    </div>
    <p class="step-lead">Unde apar dificultățile și fricțiunile. Ce nu merge, dincolo de fapte.</p>
    <div class="key-q">Ce anume nu funcționează și pe cine afectează cel mai mult?</div>
    <div class="extra-label">Întrebări de explorare</div>
    <ul class="prompts">
      <li>Ce vă nemulțumește cel mai tare în situația asta?</li>
      <li>Unde se blochează lucrurile sau se pierde timp?</li>
      <li>Cine simte problema cel mai acut și cum?</li>
      <li>E o singură problemă, sau mai multe amestecate?</li>
    </ul>
    <textarea id="text-problem" aria-label="Note Problemă" placeholder="Note, observații, exemple..."></textarea>
  </div>

  <!-- I -->
  <div class="step">
    <div class="step-header">
      <span class="step-letter">I</span>
      <h2 class="step-title">Implicații</h2>
    </div>
    <p class="step-lead">Ce se întâmplă dacă nimic nu se schimbă. Efectele în lanț și costul real, asupra oamenilor și a rezultatelor.</p>
    <div class="key-q">Dacă lăsăm lucrurile așa, unde ajungem peste șase luni?</div>
    <div class="score-block">
      <div class="extra-label">Cât de urgent simțim că e (0–10)</div>
      <div class="scale" id="scale-implications" role="group" aria-label="Scor de urgență"></div>
      <div class="scale-legend"><span>deloc urgent</span><span>oarecum</span><span>foarte urgent</span></div>
    </div>
    <div class="extra-label">Întrebări de explorare</div>
    <ul class="prompts">
      <li>Ce ne costă deja problema asta, în timp, energie, încredere?</li>
      <li>Pe ce altceva se răsfrânge, dincolo de echipă?</li>
      <li>Ce se agravează dacă nu facem nimic?</li>
      <li>Cine mai e afectat, în afară de noi?</li>
    </ul>
    <textarea id="text-implications" aria-label="Note Implicații" placeholder="Note, observații, exemple..."></textarea>
  </div>

  <!-- N -->
  <div class="step">
    <div class="step-header">
      <span class="step-letter">N</span>
      <h2 class="step-title">Nevoie și câștig</h2>
    </div>
    <p class="step-lead">Ce devine posibil dacă rezolvăm. Valoarea soluției, văzută de echipă, nu impusă din afară.</p>
    <div class="key-q">Cum ar arăta lucrurile dacă am rezolva asta, și ce ne-ar aduce?</div>
    <div class="score-block">
      <div class="extra-label">Cât de pregătiți suntem să acționăm (0–10)</div>
      <div class="scale" id="scale-needpayoff" role="group" aria-label="Scor de pregătire"></div>
      <div class="scale-legend"><span>deloc</span><span>oarecum</span><span>pe deplin</span></div>
    </div>
    <div class="extra-label">Întrebări de explorare</div>
    <ul class="prompts">
      <li>Ce s-ar schimba în bine, concret, dacă am depăși problema?</li>
      <li>Ce ar însemna asta pentru fiecare dintre voi?</li>
      <li>De ce merită efortul tocmai acum?</li>
      <li>Care ar fi primul semn că ne mișcăm în direcția bună?</li>
    </ul>
    <textarea id="text-needpayoff" aria-label="Note Nevoie și câștig" placeholder="Note, observații, exemple..."></textarea>
  </div>
```

- [ ] **Step 2: Verify**

```bash
grep -o 'id="text-[a-z]*"' modelul-spin.html | sort   # expect text-focus, text-implications, text-needpayoff, text-problem, text-situation
grep -c 'id="scale-implications"' modelul-spin.html    # expect 1
grep -c 'id="scale-needpayoff"' modelul-spin.html      # expect 1
grep -c 'id="scale-' modelul-spin.html                 # expect 2 (only I and N have scales)
```
Expected: the four `text-*` ids present, `scale-implications` and `scale-needpayoff` each once, exactly 2 scale containers. The scales render empty for now (JS rebuilt in Task 6); page may have stale GRPI JS referencing old keys — that is fixed in Task 6, so the scales/notes are not expected to fully work until then.

- [ ] **Step 3: Commit**

```bash
git add modelul-spin.html
git commit -m "Add four SPIN step cards (scales on Implicații and Nevoie)"
```

---

### Task 5: Action plan step + next-step CTA

**Files:**
- Modify: `modelul-spin.html` (action plan card text, next-step CTA)

- [ ] **Step 1: Update the action plan card**

The "Planul de acțiune" `.step` card (GRPI lines 931–939) keeps the same structure (single `textarea id="text-focus"`). Replace its `.step-lead` and `.key-q` text so it reads:

```html
  <!-- Plan de acțiune -->
  <div class="step">
    <div class="step-header">
      <h2 class="step-title">Planul de acțiune</h2>
    </div>
    <p class="step-lead">Acum, că nevoia e clară, alegeți un singur pas concret pentru perioada următoare.</p>
    <div class="key-q">Care e primul pas mic pe care îl putem face în următoarele două săptămâni, și cine îl ține?</div>
    <textarea id="text-focus" aria-label="Planul de acțiune" placeholder="Scrie aici..."></textarea>
  </div>
```

The `.actions-bar` block (Salvează / Sesiuni / Resetează / Salvează PDF) stays unchanged.

- [ ] **Step 2: Replace the next-step CTA**

Replace the `aside.next-step` block (GRPI lines 957–965) with:

```html
  <!-- Next step CTA: revealed after save / PDF -->
  <aside class="next-step" id="next-step-cta" hidden aria-label="Pasul următor">
    <div class="next-step-eyebrow">Pasul următor</div>
    <h3 class="next-step-title">Ați văzut de ce contează. <em>Hai să transformăm asta în mișcare.</em></h3>
    <p class="next-step-body">SPIN a scos la lumină nevoia comună. GRPI vă arată unde e blocajul structural, iar Roata echipei cum evoluează echipa în timp. Sau, dacă vrei, putem privi împreună ce ați descoperit.</p>
    <div class="next-step-actions">
      <a class="btn ghost" href="modelul-grpi.html">Vezi modelul GRPI →</a>
      <a class="btn ghost" href="roata-echipei.html">Vezi Roata echipei →</a>
      <a class="btn primary" href="https://calendly.com/annamaria-brad/sedinta-coaching" target="_blank" rel="noopener">Programează o conversație (30 min, gratuit) →</a>
    </div>
  </aside>
```

The `footer.bottom` stays unchanged.

- [ ] **Step 3: Verify**

```bash
grep -c 'id="text-focus"' modelul-spin.html                 # expect 1
grep -c 'href="modelul-grpi.html"' modelul-spin.html        # expect 1
grep -c 'href="roata-echipei.html"' modelul-spin.html       # expect 1
```
Expected: `1`, `1`, `1`.

- [ ] **Step 4: Commit**

```bash
git add modelul-spin.html
git commit -m "Add SPIN action plan copy and next-step CTA"
```

---

### Task 6: Rewire the JavaScript data model

**Files:**
- Modify: `modelul-spin.html` (the main `<script>` block — DIMS, state, scales, bindings, save/load, PDF, storage prefix). The `customConfirm` IIFE script and the PDF-engine portion of `btn-pdf` stay verbatim from GRPI.

- [ ] **Step 1: Replace DIMS, emptyScores, emptyNotes, and state**

Replace GRPI lines 977–994 (the `DIMS` array through the `state` object) with:

```js
// === Dimensions ===
const DIMS = [
  { key: 'situation',    letter: 'S', label: 'Situație' },
  { key: 'problem',      letter: 'P', label: 'Problemă' },
  { key: 'implications', letter: 'I', label: 'Implicații',      scale: true },
  { key: 'needpayoff',   letter: 'N', label: 'Nevoie și câștig', scale: true },
];

// Only the scaled dimensions hold a score.
const SCALED = DIMS.filter(d => d.scale);

function emptyScores() { return { implications: null, needpayoff: null }; }
function emptyNotes()  { return { situation: '', problem: '', implications: '', needpayoff: '' }; }

// === State model ===
const state = {
  clientName: '',
  sessionDate: '',
  challenge: '',
  scores: emptyScores(),
  notes: emptyNotes(),
  focus: '',
};
```

- [ ] **Step 2: Render scales only for scaled dims, and wire the example chips + challenge field**

Replace GRPI line 1030 (`DIMS.forEach(d => renderScale(d.key));`) with:

```js
SCALED.forEach(d => renderScale(d.key));

// Challenge field binding
const challengeInput = document.getElementById('challenge');
challengeInput.addEventListener('input', e => { state.challenge = e.target.value; });

// Example chips fill the challenge field
document.querySelectorAll('#examples .example-chip').forEach(chip => {
  chip.addEventListener('click', () => {
    const full = chip.dataset.full || chip.textContent;
    challengeInput.value = full;
    state.challenge = full;
    challengeInput.focus();
  });
});
```

(The `renderScale`, `escapeHtml`, and note-binding `DIMS.forEach(...)` code above line 1030 stay unchanged — note bindings correctly loop all four `text-*` ids.)

- [ ] **Step 3: Include `challenge` in applyState and reset**

In `applyState()` (GRPI lines 1033–1041), add the challenge line. Replace the function with:

```js
function applyState() {
  document.getElementById('client-name').value = state.clientName || '';
  document.getElementById('session-date').value = state.sessionDate || '';
  document.getElementById('challenge').value = state.challenge || '';
  DIMS.forEach(d => {
    document.getElementById('text-' + d.key).value = state.notes[d.key] || '';
  });
  SCALED.forEach(d => renderScale(d.key));
  document.getElementById('text-focus').value = state.focus || '';
}
```

In the reset handler (GRPI lines 1043–1053), add `challenge: '',` to the `Object.assign(state, {...})`. The block becomes:

```js
document.getElementById('btn-reset').addEventListener('click', async () => {
  if (!await customConfirm('Resetez toate câmpurile? (Sesiunile salvate rămân.)')) return;
  Object.assign(state, {
    clientName: '',
    sessionDate: '',
    challenge: '',
    scores: emptyScores(),
    notes: emptyNotes(),
    focus: '',
  });
  applyState();
});
```

- [ ] **Step 4: Change the storage prefix**

Replace GRPI line 1067:

```js
const STORAGE_PREFIX = 'spin:';
```

- [ ] **Step 5: Include `challenge` in saveSession and loadSession**

In `saveSession()` (GRPI lines 1082–1103), add `challenge` to the `data` object. The `data` literal becomes:

```js
  const data = {
    clientName: name,
    sessionDate: date,
    savedAt,
    challenge: state.challenge,
    scores: { ...state.scores },
    notes: { ...state.notes },
    focus: state.focus,
  };
```

In `loadSession()` (GRPI lines 1172–1182), add `challenge`. The `Object.assign` becomes:

```js
function loadSession(data) {
  Object.assign(state, {
    clientName: data.clientName || '',
    sessionDate: data.sessionDate || '',
    challenge: data.challenge || '',
    scores: Object.assign(emptyScores(), data.scores || {}),
    notes: Object.assign(emptyNotes(), data.notes || {}),
    focus: data.focus || '',
  });
  applyState();
  flash(`Sesiune încărcată: ${data.clientName}`);
}
```

- [ ] **Step 6: Update the PDF report builder (title, challenge line, score only on scaled dims)**

Replace `buildPdfReport()` (GRPI lines 1215–1255) with:

```js
function buildPdfReport() {
  const nume = (state.clientName || '').trim() || '—';
  const dateIso = state.sessionDate || new Date().toISOString().slice(0, 10);
  const data = (() => {
    try {
      const [y, m, d] = dateIso.split('-').map(Number);
      return new Intl.DateTimeFormat('ro-RO', {
        day: 'numeric', month: 'long', year: 'numeric'
      }).format(new Date(y, m - 1, d));
    } catch (e) { return dateIso; }
  })();
  const dimCard = (d) => {
    const score = state.scores[d.key];
    const note = state.notes[d.key] || '';
    const showScore = d.scale && score != null;
    return `
    <div class="pdf-step">
      <div class="pdf-step-head">
        <span class="pdf-step-letter">${d.letter}</span>
        <span class="pdf-step-name">${d.label}</span>
        ${showScore ? `<span class="pdf-score">${score}/10</span>` : ''}
      </div>
      <div class="pdf-step-body ${note.trim() ? '' : 'pdf-step-empty'}">${note.trim() ? escapeHtml(note) : '— necompletat —'}</div>
    </div>`;
  };

  const challengeHtml = state.challenge.trim() ? `
    <div class="pdf-step">
      <div class="pdf-step-head">
        <span class="pdf-step-name">Provocarea</span>
      </div>
      <div class="pdf-step-body">${escapeHtml(state.challenge)}</div>
    </div>` : '';

  const focusHtml = state.focus.trim() ? `
    <div class="pdf-step">
      <div class="pdf-step-head">
        <span class="pdf-step-name">Planul de acțiune</span>
      </div>
      <div class="pdf-step-body">${escapeHtml(state.focus)}</div>
    </div>` : '';

  return `
    <h1 class="pdf-title">Modelul <em>SPIN</em></h1>
    <div class="pdf-meta">${escapeHtml(nume)} &nbsp;·&nbsp; ${escapeHtml(data)}</div>
    ${challengeHtml}
    ${DIMS.map(dimCard).join('')}
    ${focusHtml}
    <div class="pdf-footer">© Ana-Maria Brad · Instrument de coaching · calendly.com/annamaria-brad/sedinta-coaching</div>
  `;
}
```

- [ ] **Step 7: Update the PDF filename**

In the `btn-pdf` click handler (GRPI line 1310), replace the filename line:

```js
    pdf.save(`modelul-spin_${nameKebab}_${date}.pdf`);
```

- [ ] **Step 8: Verify the JS rewiring in a browser**

```bash
grep -c "STORAGE_PREFIX = 'spin:'" modelul-spin.html   # expect 1
grep -c "modelul-spin_" modelul-spin.html               # expect 1 (PDF filename)
grep -c "state.challenge" modelul-spin.html             # expect >= 5
grep -c "grpi" modelul-spin.html                         # expect 1 (only the GRPI CTA link href)
```
Expected: `1`, `1`, `>=5`, `1`.

Open `modelul-spin.html` in a browser and confirm, with the console open (expect zero JS errors):
1. Clicking an example chip fills the challenge field with the long sentence.
2. The 0–10 scales appear under Implicații and Nevoie only (S and P have no scale).
3. Typing in each note field, the challenge, and the action plan works.
4. "Salvează sesiunea" with a team name shows the flash and reveals the next-step CTA; reload, open "Sesiuni salvate", load it back — challenge, scores, notes, and action plan all restore.
5. "Salvează PDF" downloads `modelul-spin_<echipa>_<data>.pdf` containing the challenge, the two scores, notes, and the action plan.
6. "Resetează" clears every field including the challenge.

- [ ] **Step 9: Commit**

```bash
git add modelul-spin.html
git commit -m "Rewire SPIN JS: data model, chips, scales, sessions, PDF"
```

---

### Task 7: Add the SPIN card to the Romanian hub

**Files:**
- Modify: `index.html` (insert a card after the GRPI card)

- [ ] **Step 1: Insert the SPIN card**

In `index.html`, immediately after the GRPI card `</a>` (line 364) and before the "credinte-limitative" card, insert:

```html
    <a class="card" data-audience="adult echipa" href="modelul-spin.html">
      <div class="card-label">Echipă · conversație</div>
      <h2>Modelul <em>SPIN</em></h2>
      <p>Patru tipuri de întrebări, după Neil Rackham: Situație, Problemă, Implicații, Nevoie și câștig. Iei o singură provocare a echipei și o explorezi până când nevoia de a o rezolva devine limpede pentru toți, înainte de soluții.</p>
      <span class="card-cta">Deschide <span class="arrow">→</span></span>
    </a>
```

- [ ] **Step 2: Verify**

```bash
grep -c 'href="modelul-spin.html"' index.html        # expect 1
grep -c 'data-audience="adult echipa"' index.html    # expect 3 (roata-echipei, GRPI, SPIN)
```
Expected: `1`, `3`. Open `index.html` in a browser: the SPIN card appears; click the "Echipă" filter and confirm the count is now `3` and the SPIN card stays visible; the card links to `modelul-spin.html`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "Add SPIN card to Romanian coaching hub"
```

---

### Task 8: Final end-to-end verification

**Files:** none (verification only)

- [ ] **Step 1: Structural sweep for leftover GRPI references**

```bash
grep -n "GRPI\|grpi\|Goals\|Roles\|Processes\|Interpersonal\|Rubin, Plovnick" modelul-spin.html
```
Expected: ONLY the next-step CTA references (`Vezi modelul GRPI`, `href="modelul-grpi.html"`). Any other GRPI/Goals/Roles/Processes/Interpersonal leftover is a bug — fix it, then re-run.

- [ ] **Step 2: Full manual run-through in a browser**

Open `modelul-spin.html` and complete one full session end to end: pick a chip, set a team name and date, score Implicații and Nevoie, write notes in all four steps and the action plan, save the session, export the PDF, reload and load the saved session, then reset. Confirm everything behaves and the console shows no errors.

- [ ] **Step 3: Confirm the hub flow**

Open `index.html`, filter by "Echipă", click the SPIN card, confirm it opens the working tool. Click the in-tool "← Instrumente coaching" back-link and confirm it returns to the hub.

- [ ] **Step 4: Final commit (if any fixes were made)**

```bash
git add -A
git commit -m "Fix leftover references found in SPIN final verification"
```

(Skip if Step 1 found nothing to fix.)

---

## Out of scope (separate follow-up)

- English version `en/spin-model.html` + the EN hub card (the `hreflang`/lang-toggle links point to it already, so it 404s until built — acceptable until the follow-up).
- Dedicated SPIN OG image (uses shared `og-cover.png`).
- A teens variant.

## Notes for the implementer

- Do NOT touch the `customConfirm` IIFE (GRPI lines 1323–1418), the sessions dropdown logic, the `flash`/`revealNextStep` helpers, or the PDF rendering engine inside `btn-pdf` (canvas slicing). They are reused verbatim and already correct.
- The hub filter counts are computed in JS from `data-audience` attributes — never hand-edit the counts.
- Copy must stay clean Romanian: sentence case, diacritice, no romgleză. The model letters are RO words (Situație, Problemă, Implicații, Nevoie și câștig); the English origins appear only inside the "Despre" guide.
