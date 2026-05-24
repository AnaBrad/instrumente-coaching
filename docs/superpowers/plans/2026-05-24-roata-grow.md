# Roata GROW Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `roata-grow.html` — a single-file coaching worksheet on the GROW model (Goal-Reality-Options-Will), deployed to GitHub Pages alongside the other coaching wheels.

**Architecture:** Single self-contained HTML file with embedded CSS and vanilla JS. Reuses the existing design system (paper palette, Fraunces+Inter fonts) and patterns from sibling files: localStorage multi-session save from `roata-increderii.html`, PDF export with lazy-loaded html2canvas+jspdf from `roata-emotiilor.html`, modal `customConfirm` pattern shared across all roti.

**Tech Stack:** HTML5, vanilla CSS (CSS variables, grid, flexbox), vanilla JS, Google Fonts (Fraunces + Inter), localStorage API, html2canvas 1.4.1, jspdf 2.5.1.

**No test framework.** Verification is visual/functional in browser. Each task includes specific browser checks.

---

## File Structure

- **Create:** `roata-grow.html` — the worksheet itself (~1200 lines)
- **Modify:** `index.html` — add a card linking to the new worksheet
- **Reference (read only):** `mockup-grow.html`, `roata-emotiilor.html`, `roata-increderii.html`

The mockup file (`mockup-grow.html`) already has the final visual structure. Reuse it as starting point — most CSS and HTML body is final. Add only JS interactivity and persistence.

---

## Task 1: Scaffold roata-grow.html from mockup

**Files:**
- Create: `roata-grow.html`
- Reference: `mockup-grow.html`, `roata-emotiilor.html`

- [ ] **Step 1: Copy mockup as starting point**

```bash
cp mockup-grow.html roata-grow.html
```

- [ ] **Step 2: Remove the mockup banner**

In `roata-grow.html`, delete this entire line:
```html
<div class="mockup-banner">MOCKUP STATIC — pentru aprobare design</div>
```
Also remove the `.mockup-banner { ... }` CSS rule.

- [ ] **Step 3: Update the `<head>` with meta tags + favicon**

Replace the existing `<title>` line with:

```html
<title>Roata GROW — Instrument de Coaching</title>
<meta name="description" content="Worksheet de coaching pe modelul GROW (Goal-Reality-Options-Will, după John Whitmore). De la intenție, la acțiune. Instrument creat de Ana-Maria Brad.">
<meta name="author" content="Ana-Maria Brad">

<!-- Open Graph -->
<meta property="og:type" content="website">
<meta property="og:title" content="Roata GROW — Instrument de Coaching">
<meta property="og:description" content="De la intenție, la acțiune. Worksheet GROW (Whitmore) — Goal, Reality, Options, Will.">
<meta property="og:url" content="https://anabrad.github.io/Roti-Coaching/roata-grow.html">
<meta property="og:locale" content="ro_RO">
<meta property="og:image" content="https://anabrad.github.io/Roti-Coaching/og-roata-grow.png">

<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Roata GROW — Instrument de Coaching">
<meta name="twitter:description" content="De la intenție, la acțiune. Worksheet GROW (Whitmore).">
<meta name="twitter:image" content="https://anabrad.github.io/Roti-Coaching/og-roata-grow.png">

<!-- Favicon: 4 quadrants in paper palette (G/R/O/W) -->
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='46' fill='%23f1e6d2' stroke='%23a05a25' stroke-width='2'/%3E%3Cg transform='translate(50,50)' font-family='serif' font-style='italic' font-size='22' fill='%23a05a25' text-anchor='middle' dominant-baseline='central'%3E%3Ctext x='-18' y='-14'%3EG%3C/text%3E%3Ctext x='18' y='-14'%3ER%3C/text%3E%3Ctext x='-18' y='18'%3EO%3C/text%3E%3Ctext x='18' y='18'%3EW%3C/text%3E%3C/g%3E%3Cline x1='50' y1='10' x2='50' y2='90' stroke='%23a05a25' stroke-width='0.5'/%3E%3Cline x1='10' y1='50' x2='90' y2='50' stroke='%23a05a25' stroke-width='0.5'/%3E%3C/svg%3E">
```

- [ ] **Step 4: Verify scaffold in browser**

Open `roata-grow.html` in browser:
```bash
open roata-grow.html
```
Expected: same as mockup (4 GROW sections, header, footer), but no red "MOCKUP" banner at top. Tab favicon shows 4 letters G/R/O/W.

- [ ] **Step 5: Commit scaffold**

```bash
git add roata-grow.html
git commit -m "scaffold roata-grow.html din mockup cu meta + favicon"
```

---

## Task 2: Add `sr-only` class + accessibility on inputs

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add `.sr-only` utility class after `* { box-sizing: border-box; }`**

```css
  .sr-only {
    position: absolute;
    width: 1px; height: 1px;
    padding: 0; margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    white-space: nowrap;
    border: 0;
  }
```

- [ ] **Step 2: Add IDs and labels to header inputs**

Replace:
```html
    <div class="session-info">
      <input type="text" placeholder="Nume" value="" oninput="this.value=this.value.replace(/[0-9]/g,'')">
      <input type="date" min="1900-01-01" max="2099-12-31">
    </div>
```

With:
```html
    <div class="session-info">
      <label for="client-name" class="sr-only">Nume</label>
      <input type="text" id="client-name" placeholder="Nume" autocomplete="off" aria-label="Nume" oninput="this.value=this.value.replace(/[0-9]/g,'')">
      <label for="session-date" class="sr-only">Data</label>
      <input type="date" id="session-date" min="1900-01-01" max="2099-12-31" aria-label="Data">
    </div>
```

- [ ] **Step 3: Add IDs to the 4 GROW textareas**

In each `<div class="step">`, add an ID to the textarea matching the step letter:
- Goal step textarea: `id="text-goal"`
- Reality step textarea: `id="text-reality"`
- Options step textarea: `id="text-options"`
- Will step textarea (the main one, NOT the obstacles one): `id="text-will"`

Add `aria-label` matching the step name to each textarea.

- [ ] **Step 4: Add IDs for Will extras**

- Commitment scale `<div class="scale">`: add `id="commitment-scale"` and `role="radiogroup" aria-label="Cât de hotărât/hotărâtă ești"`
- Each `.scale-chip` button: add `type="button"` (prevents accidental form submit) and `aria-label` like `aria-label="Nivel 7"`
- Actions table `<tbody>`: add `id="actions-tbody"`
- Add-row button: add `type="button"` and `id="btn-add-action"`
- Obstacles textarea: add `id="text-obstacles"` and `aria-label="Obstacole anticipate"`

- [ ] **Step 5: Add IDs to action bar buttons**

```html
      <button class="btn" id="btn-save">Salvează sesiunea</button>
      <button class="btn ghost" id="btn-sessions">Sesiuni salvate ▾</button>
      <button class="btn ghost" id="btn-reset">Resetează</button>
    </div>
    <div class="right">
      <button class="btn" id="btn-print">Tipărește</button>
      <button class="btn primary" id="btn-pdf">Exportă PDF</button>
```

Also add `type="button"` to each.

- [ ] **Step 6: Verify in browser**

Reload page. Tab through the fields with the keyboard: each input/button receives focus visibly. Screen reader (VoiceOver: Cmd+F5) reads labels correctly.

- [ ] **Step 7: Commit**

```bash
git add roata-grow.html
git commit -m "a11y: ID-uri + label-uri sr-only pe inputs si butoane"
```

---

## Task 3: Add JS state model and commitment scale interactivity

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add main `<script>` block before `</body>`**

Insert immediately before `</body>`:

```html
<script>
// === State model ===
const state = {
  goal: '',
  reality: '',
  options: '',
  will: '',
  commitment: 0,
  actions: [{ what: '', by: '' }, { what: '', by: '' }],
  obstacles: '',
};

// Bind textareas to state
const TEXTAREA_FIELDS = ['goal', 'reality', 'options', 'will', 'obstacles'];
TEXTAREA_FIELDS.forEach(field => {
  const el = document.getElementById('text-' + field);
  el.addEventListener('input', e => { state[field] = e.target.value; });
});
</script>
```

- [ ] **Step 2: Wire commitment scale chip selection**

Append inside the same `<script>` block, after the textarea bindings:

```js
// === Commitment scale ===
const scaleChips = document.querySelectorAll('#commitment-scale .scale-chip');
scaleChips.forEach((chip, idx) => {
  const level = idx + 1;
  chip.dataset.level = level;
  chip.setAttribute('aria-pressed', 'false');
  chip.addEventListener('click', () => {
    state.commitment = (state.commitment === level) ? 0 : level;
    renderCommitment();
  });
});

function renderCommitment() {
  scaleChips.forEach((chip, idx) => {
    const level = idx + 1;
    const selected = state.commitment === level;
    chip.classList.toggle('selected', selected);
    chip.setAttribute('aria-pressed', selected ? 'true' : 'false');
  });
}
```

- [ ] **Step 3: Remove the static `selected` class from the mockup chip**

In the HTML for the scale chips, change:
```html
<button class="scale-chip selected">7</button>
```
to:
```html
<button class="scale-chip">7</button>
```

(All chips start un-selected.)

- [ ] **Step 4: Verify in browser**

Reload. Click chip "5" — it turns orange (selected). Click "5" again — deselects. Click "8" — switches selection from "5" to "8".

- [ ] **Step 5: Commit**

```bash
git add roata-grow.html
git commit -m "state model + commitment scale interactivity"
```

---

## Task 4: Wire actions table (dynamic rows)

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Replace static action rows with dynamic rendering**

Replace the contents of `<tbody id="actions-tbody">` (the two `<tr>` rows) with empty `<tbody id="actions-tbody"></tbody>`.

- [ ] **Step 2: Add actions render + add-row logic to the `<script>`**

Append to the script block:

```js
// === Actions table ===
function renderActions() {
  const tbody = document.getElementById('actions-tbody');
  tbody.innerHTML = '';
  state.actions.forEach((action, idx) => {
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td><input type="text" placeholder="ex: trimit emailul către X" value="${escapeHtml(action.what)}" data-idx="${idx}" data-field="what"></td>
      <td><input type="date" min="1900-01-01" max="2099-12-31" value="${escapeHtml(action.by)}" data-idx="${idx}" data-field="by"></td>
    `;
    tbody.appendChild(tr);
  });
  // Wire inputs
  tbody.querySelectorAll('input').forEach(input => {
    input.addEventListener('input', e => {
      const idx = Number(e.target.dataset.idx);
      const field = e.target.dataset.field;
      state.actions[idx][field] = e.target.value;
    });
  });
}

function escapeHtml(s) {
  return String(s).replace(/[&<>"']/g, c => ({
    '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'
  }[c]));
}

document.getElementById('btn-add-action').addEventListener('click', () => {
  state.actions.push({ what: '', by: '' });
  renderActions();
});

renderActions();
```

- [ ] **Step 3: Verify in browser**

Reload. The actions table shows 2 empty rows (from initial state). Click "+ adaugă rând" — a third row appears. Type something in row 1 "Ce voi face" — does NOT disappear when you add row 4 (state persists).

- [ ] **Step 4: Commit**

```bash
git add roata-grow.html
git commit -m "actions table dinamic cu add-row"
```

---

## Task 5: Modal customConfirm (copy from sibling)

**Files:**
- Modify: `roata-grow.html`
- Reference: `roata-emotiilor.html` (the bottom `<script>` IIFE that creates `customConfirm`)

- [ ] **Step 1: Copy the customConfirm IIFE from `roata-emotiilor.html`**

Open `roata-emotiilor.html`. Find the second `<script>` block at the bottom — the one wrapped in `(function() { ... })();` that creates `.cc-overlay`, `.cc-dialog`, `.cc-message`, `.cc-actions`, etc. Copy the entire block.

Paste it immediately AFTER the main `<script>` block in `roata-grow.html`, before `</body>`.

This block includes:
- The `<style>` injection for `.cc-overlay` / `.cc-dialog` / `.cc-btn-*`
- The dynamic overlay creation with `role="dialog"`, `aria-modal`, `aria-labelledby`
- `window.customConfirm = function(message) { ... }` with Escape + Tab focus trap + focus restoration

- [ ] **Step 2: Verify in browser console**

Reload. Open DevTools console. Run:
```js
customConfirm('Test?').then(r => console.log('result:', r));
```
A modal appears. Press Escape — console logs `result: false`. Run again, click "Confirmă" — logs `result: true`. Press Tab — focus cycles between the two buttons.

- [ ] **Step 3: Commit**

```bash
git add roata-grow.html
git commit -m "customConfirm modal (acelasi pattern ca celelalte roti)"
```

---

## Task 6: Reset button

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add reset logic**

Append to the main script (before the final close):

```js
// === Reset ===
function applyState() {
  document.getElementById('client-name').value = state.clientName || '';
  document.getElementById('session-date').value = state.sessionDate || '';
  TEXTAREA_FIELDS.forEach(field => {
    document.getElementById('text-' + field).value = state[field] || '';
  });
  renderCommitment();
  renderActions();
}

document.getElementById('btn-reset').addEventListener('click', async () => {
  if (!await customConfirm('Resetez toate câmpurile? (Sesiunile salvate rămân.)')) return;
  Object.assign(state, {
    clientName: '',
    sessionDate: '',
    goal: '', reality: '', options: '', will: '',
    commitment: 0,
    actions: [{ what: '', by: '' }, { what: '', by: '' }],
    obstacles: '',
  });
  applyState();
});

// Also bind name + date inputs to state
document.getElementById('client-name').addEventListener('input', e => {
  state.clientName = e.target.value;
});
document.getElementById('session-date').addEventListener('input', e => {
  state.sessionDate = e.target.value;
});

// Default date = today
document.getElementById('session-date').valueAsDate = new Date();
state.sessionDate = document.getElementById('session-date').value;
```

- [ ] **Step 2: Verify in browser**

Reload. Type a goal, select commitment 7, add an action. Click "Resetează" — modal appears. Click "Confirmă" — all fields clear, commitment chips deselect, actions back to 2 empty rows.

Click "Resetează" again. Click "Anulează" — nothing changes.

- [ ] **Step 3: Commit**

```bash
git add roata-grow.html
git commit -m "reset button cu confirm + bind name/date la state"
```

---

## Task 7: localStorage save sessions

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add save logic**

Append to the script:

```js
// === Persistence (localStorage) ===
const STORAGE_PREFIX = 'grow:';

function listSessions() {
  const out = [];
  for (let i = 0; i < localStorage.length; i++) {
    const key = localStorage.key(i);
    if (!key.startsWith(STORAGE_PREFIX)) continue;
    try {
      const data = JSON.parse(localStorage.getItem(key));
      out.push({ key, ...data });
    } catch (e) { /* skip corrupted */ }
  }
  return out.sort((a, b) => (b.savedAt || '').localeCompare(a.savedAt || ''));
}

function saveSession() {
  const name = (state.clientName || '').trim();
  if (!name) {
    alert('Adaugă un nume înainte de a salva.');
    document.getElementById('client-name').focus();
    return;
  }
  const date = state.sessionDate || new Date().toISOString().slice(0, 10);
  const savedAt = new Date().toISOString();
  const key = STORAGE_PREFIX + savedAt + '_' + name.toLowerCase().replace(/\s+/g, '-');
  const data = {
    clientName: name,
    sessionDate: date,
    savedAt,
    goal: state.goal,
    reality: state.reality,
    options: state.options,
    will: state.will,
    commitment: state.commitment,
    actions: state.actions.filter(a => a.what.trim() || a.by),
    obstacles: state.obstacles,
  };
  localStorage.setItem(key, JSON.stringify(data));
  flash(`Sesiune salvată: ${name} — ${date}`);
}

function flash(msg) {
  const el = document.createElement('div');
  el.textContent = msg;
  el.style.cssText = `
    position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%);
    background: var(--accent); color: #fff7e8;
    padding: 10px 20px; border-radius: 999px;
    font-size: 13px; z-index: 9999;
    box-shadow: 0 4px 16px rgba(0,0,0,0.15);
    opacity: 0; transition: opacity 0.2s;
  `;
  document.body.appendChild(el);
  requestAnimationFrame(() => { el.style.opacity = '1'; });
  setTimeout(() => {
    el.style.opacity = '0';
    setTimeout(() => el.remove(), 250);
  }, 2200);
}

document.getElementById('btn-save').addEventListener('click', saveSession);
```

- [ ] **Step 2: Verify in browser**

Reload. Try clicking "Salvează" with empty name → alert appears, focus jumps to name field.

Type a name "Test client", commit chip "8", add a goal. Click "Salvează" → orange toast appears bottom.

Open DevTools → Application → Local Storage → see `grow:2026-...test-client` key with JSON data.

- [ ] **Step 3: Commit**

```bash
git add roata-grow.html
git commit -m "save sesiuni in localStorage cu flash toast"
```

---

## Task 8: Sessions dropdown (load + delete)

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add dropdown UI markup**

Find the `<div class="left">` in the action bar. Wrap `#btn-sessions` in a position-relative container and add a hidden dropdown next to it:

```html
      <div class="sessions-wrap" style="position:relative">
        <button class="btn ghost" id="btn-sessions" type="button">Sesiuni salvate ▾</button>
        <div class="sessions-list" id="sessions-list" hidden></div>
      </div>
```

- [ ] **Step 2: Add CSS for the dropdown**

Insert this CSS into the existing `<style>` block (anywhere logical, e.g., after the action bar styles):

```css
  .sessions-list {
    position: absolute;
    top: calc(100% + 4px);
    left: 0;
    min-width: 280px;
    max-height: 320px;
    overflow-y: auto;
    background: var(--surface);
    border: 0.5px solid var(--paper-deep);
    border-radius: var(--radius-lg);
    box-shadow: 0 6px 24px rgba(0,0,0,0.12);
    padding: 6px;
    z-index: 100;
  }
  .session-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px 10px;
    border-radius: var(--radius);
    cursor: pointer;
  }
  .session-item:hover { background: var(--paper-soft); }
  .session-item-info { flex: 1; min-width: 0; }
  .session-item-name {
    font-size: 14px;
    color: var(--ink);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .session-item-date {
    font-size: 11px;
    color: var(--ink-mute);
    text-transform: uppercase;
    letter-spacing: 0.06em;
  }
  .session-item-delete {
    background: transparent;
    border: none;
    color: var(--ink-mute);
    cursor: pointer;
    font-size: 16px;
    padding: 2px 6px;
    border-radius: 4px;
  }
  .session-item-delete:hover { color: var(--bordo); background: var(--paper); }
  .sessions-empty {
    padding: 14px 10px;
    font-size: 13px;
    color: var(--ink-mute);
    font-style: italic;
    text-align: center;
  }
```

- [ ] **Step 3: Wire dropdown render + load + delete**

Append to the main script:

```js
// === Sessions dropdown ===
const sessionsList = document.getElementById('sessions-list');
const btnSessions = document.getElementById('btn-sessions');

function renderSessions() {
  const sessions = listSessions();
  if (sessions.length === 0) {
    sessionsList.innerHTML = '<div class="sessions-empty">Nicio sesiune salvată încă.</div>';
    return;
  }
  sessionsList.innerHTML = sessions.map(s => `
    <div class="session-item" data-key="${escapeHtml(s.key)}" role="button" tabindex="0">
      <div class="session-item-info">
        <div class="session-item-name">${escapeHtml(s.clientName || '—')}</div>
        <div class="session-item-date">${escapeHtml(s.sessionDate || '')}</div>
      </div>
      <button class="session-item-delete" data-key="${escapeHtml(s.key)}" type="button" aria-label="Șterge sesiunea">✕</button>
    </div>
  `).join('');

  sessionsList.querySelectorAll('.session-item').forEach(item => {
    item.addEventListener('click', async (e) => {
      if (e.target.classList.contains('session-item-delete')) return;
      if (!await customConfirm('Încarc această sesiune? Datele curente se pierd dacă nu sunt salvate.')) return;
      const data = JSON.parse(localStorage.getItem(item.dataset.key));
      loadSession(data);
      sessionsList.hidden = true;
    });
  });
  sessionsList.querySelectorAll('.session-item-delete').forEach(btn => {
    btn.addEventListener('click', async (e) => {
      e.stopPropagation();
      if (!await customConfirm('Șterg această sesiune? Nu se poate recupera.')) return;
      localStorage.removeItem(btn.dataset.key);
      renderSessions();
    });
  });
}

function loadSession(data) {
  Object.assign(state, {
    clientName: data.clientName || '',
    sessionDate: data.sessionDate || '',
    goal: data.goal || '',
    reality: data.reality || '',
    options: data.options || '',
    will: data.will || '',
    commitment: data.commitment || 0,
    actions: (data.actions && data.actions.length > 0) ? data.actions.slice() : [{ what:'', by:'' }, { what:'', by:'' }],
    obstacles: data.obstacles || '',
  });
  applyState();
  flash(`Sesiune încărcată: ${data.clientName}`);
}

btnSessions.addEventListener('click', (e) => {
  e.stopPropagation();
  const willOpen = sessionsList.hidden;
  if (willOpen) renderSessions();
  sessionsList.hidden = !willOpen;
});

// Close dropdown when clicking outside
document.addEventListener('click', (e) => {
  if (!sessionsList.contains(e.target) && e.target !== btnSessions) {
    sessionsList.hidden = true;
  }
});
```

- [ ] **Step 4: Save a few sessions and verify**

Reload. Create 2 sessions with different names and dates. Click "Sesiuni salvate ▾" — dropdown appears with both. Click outside — dropdown closes.

Click a session entry → modal asks to load → confirm → form fills with that session's data, toast appears.

Click the ✕ on a session → modal asks to delete → confirm → session removed from list, second session remains.

Reload page — saved sessions still in dropdown (localStorage persists).

- [ ] **Step 5: Commit**

```bash
git add roata-grow.html
git commit -m "sesiuni salvate: dropdown cu load + delete"
```

---

## Task 9: PDF export (lazy-loaded)

**Files:**
- Modify: `roata-grow.html`
- Reference: `roata-emotiilor.html` (PDF export pattern)

- [ ] **Step 1: Add hidden `pdfReport` container + PDF styles**

Right before `</body>`, immediately after the customConfirm IIFE, add:

```html
<div class="pdf-report" id="pdfReport"></div>
```

Then add these styles inside the existing `<style>` block:

```css
  .pdf-report {
    position: absolute;
    left: -10000px;
    top: 0;
    width: 794px;
    background: var(--paper);
    padding: 32px 44px;
    font-family: 'Inter', sans-serif;
    color: var(--ink);
    box-sizing: border-box;
  }
  .pdf-report.live { left: 0; z-index: -1; }
  .pdf-report .pdf-title {
    font-family: 'Fraunces', serif;
    font-weight: 300;
    font-size: 34px;
    line-height: 1.1;
    margin: 0 0 4px 0;
  }
  .pdf-report .pdf-title em { font-style: italic; color: var(--accent); font-weight: 400; }
  .pdf-report .pdf-meta {
    font-size: 12px;
    color: var(--ink-mute);
    letter-spacing: 0.08em;
    text-transform: uppercase;
    margin-bottom: 22px;
  }
  .pdf-report .pdf-step {
    margin-bottom: 18px;
    padding-bottom: 14px;
    border-bottom: 0.5px solid var(--paper-deep);
  }
  .pdf-report .pdf-step:last-of-type { border-bottom: none; }
  .pdf-report .pdf-step-head {
    display: flex;
    align-items: baseline;
    gap: 10px;
    margin-bottom: 6px;
  }
  .pdf-report .pdf-step-letter {
    font-family: 'Fraunces', serif;
    font-style: italic;
    font-size: 26px;
    color: var(--accent);
    line-height: 1;
  }
  .pdf-report .pdf-step-name {
    font-family: 'Fraunces', serif;
    font-size: 18px;
    color: var(--ink);
  }
  .pdf-report .pdf-step-body {
    font-size: 13px;
    color: var(--ink);
    line-height: 1.5;
    margin-left: 36px;
    white-space: pre-wrap;
  }
  .pdf-report .pdf-step-empty {
    font-style: italic;
    color: var(--ink-mute);
  }
  .pdf-report .pdf-commitment {
    margin-left: 36px;
    margin-top: 8px;
    font-size: 12px;
    color: var(--ink-soft);
  }
  .pdf-report .pdf-commitment strong {
    color: var(--accent);
    font-weight: 600;
  }
  .pdf-report .pdf-actions {
    margin-left: 36px;
    margin-top: 8px;
  }
  .pdf-report .pdf-action-row {
    display: flex;
    gap: 14px;
    font-size: 12.5px;
    color: var(--ink);
    padding: 3px 0;
    border-bottom: 0.5px dashed var(--paper-deep);
  }
  .pdf-report .pdf-action-row:last-child { border-bottom: none; }
  .pdf-report .pdf-action-what { flex: 1; }
  .pdf-report .pdf-action-by {
    color: var(--ink-mute);
    text-transform: uppercase;
    letter-spacing: 0.06em;
    font-size: 11px;
  }
  .pdf-report .pdf-obstacles {
    margin-left: 36px;
    margin-top: 8px;
    font-size: 12.5px;
    color: var(--ink-soft);
    font-style: italic;
    white-space: pre-wrap;
  }
  .pdf-report .pdf-section-label {
    font-size: 11px;
    color: var(--ink-mute);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-left: 36px;
    margin-top: 10px;
  }
  .pdf-report .pdf-footer {
    margin-top: 28px;
    padding-top: 14px;
    border-top: 0.5px solid var(--paper-deep);
    font-size: 11px;
    color: var(--ink-mute);
    text-align: center;
    letter-spacing: 0.04em;
  }
```

- [ ] **Step 2: Add lazy PDF loader + report builder + click handler**

Append to the main `<script>` block:

```js
// === PDF export (lazy-loaded) ===
let pdfLibsPromise = null;
function loadPdfLibs() {
  if (pdfLibsPromise) return pdfLibsPromise;
  const loadScript = (src) => new Promise((resolve, reject) => {
    const s = document.createElement('script');
    s.src = src;
    s.onload = resolve;
    s.onerror = () => reject(new Error('Nu am putut încărca ' + src));
    document.head.appendChild(s);
  });
  pdfLibsPromise = Promise.all([
    loadScript('https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js'),
    loadScript('https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js'),
  ]).catch(err => { pdfLibsPromise = null; throw err; });
  return pdfLibsPromise;
}

function buildPdfReport() {
  const nume = (state.clientName || '').trim() || '—';
  const data = state.sessionDate || new Date().toISOString().slice(0, 10);
  const stepCard = (letter, name, sub, body) => `
    <div class="pdf-step">
      <div class="pdf-step-head">
        <span class="pdf-step-letter">${letter}</span>
        <span class="pdf-step-name">${name}</span>
      </div>
      <div class="pdf-step-body ${body.trim() ? '' : 'pdf-step-empty'}">${body.trim() ? escapeHtml(body) : '— necompletat —'}</div>
    </div>
  `;
  const commitmentHtml = state.commitment > 0
    ? `<div class="pdf-commitment">Hotărâre: <strong>${state.commitment}/10</strong></div>`
    : '';
  const validActions = state.actions.filter(a => a.what.trim() || a.by);
  const actionsHtml = validActions.length > 0 ? `
    <div class="pdf-section-label">Acțiuni concrete</div>
    <div class="pdf-actions">
      ${validActions.map(a => `
        <div class="pdf-action-row">
          <span class="pdf-action-what">${escapeHtml(a.what) || '—'}</span>
          <span class="pdf-action-by">${escapeHtml(a.by) || ''}</span>
        </div>
      `).join('')}
    </div>
  ` : '';
  const obstaclesHtml = state.obstacles.trim() ? `
    <div class="pdf-section-label">Obstacole anticipate</div>
    <div class="pdf-obstacles">${escapeHtml(state.obstacles)}</div>
  ` : '';

  return `
    <h1 class="pdf-title">Roata <em>GROW</em></h1>
    <div class="pdf-meta">${escapeHtml(nume)} &nbsp;·&nbsp; ${escapeHtml(data)}</div>
    ${stepCard('G', 'Goal · Obiectiv', '', state.goal)}
    ${stepCard('R', 'Reality · Realitate', '', state.reality)}
    ${stepCard('O', 'Options · Opțiuni', '', state.options)}
    <div class="pdf-step">
      <div class="pdf-step-head">
        <span class="pdf-step-letter">W</span>
        <span class="pdf-step-name">Will · Voință</span>
      </div>
      <div class="pdf-step-body ${state.will.trim() ? '' : 'pdf-step-empty'}">${state.will.trim() ? escapeHtml(state.will) : '— necompletat —'}</div>
      ${commitmentHtml}
      ${actionsHtml}
      ${obstaclesHtml}
    </div>
    <div class="pdf-footer">© Ana-Maria Brad · Instrument de coaching · calendly.com/annamaria-brad/sedinta-coaching</div>
  `;
}

document.getElementById('btn-pdf').addEventListener('click', async () => {
  const btn = document.getElementById('btn-pdf');
  const original = btn.textContent;
  btn.disabled = true;
  btn.textContent = 'generez...';
  try {
    await loadPdfLibs();
  } catch (err) {
    alert('Eroare la încărcarea bibliotecilor PDF: ' + err.message);
    btn.disabled = false;
    btn.textContent = original;
    return;
  }
  const report = document.getElementById('pdfReport');
  report.innerHTML = buildPdfReport();
  report.classList.add('live');
  try {
    await new Promise(r => requestAnimationFrame(() => requestAnimationFrame(r)));
    const canvas = await html2canvas(report, { backgroundColor: '#f1e6d2', scale: 2 });
    const { jsPDF } = window.jspdf;
    const pdf = new jsPDF({ orientation: 'portrait', unit: 'mm', format: 'a4' });
    const pageW = pdf.internal.pageSize.getWidth();
    const pageH = pdf.internal.pageSize.getHeight();
    const paintBeige = () => {
      pdf.setFillColor(241, 230, 210);
      pdf.rect(0, 0, pageW, pageH, 'F');
    };
    const imgW = pageW;
    const imgH = (canvas.height / canvas.width) * imgW;
    if (imgH <= pageH) {
      paintBeige();
      pdf.addImage(canvas.toDataURL('image/jpeg', 0.92), 'JPEG', 0, 0, imgW, imgH);
    } else {
      const scale = imgW / canvas.width;
      const pagePxH = pageH / scale;
      let offsetY = 0;
      let remaining = canvas.height;
      while (remaining > 0) {
        if (offsetY > 0 && remaining < pagePxH * 0.08) break;
        const sliceH = Math.min(pagePxH, remaining);
        const c = document.createElement('canvas');
        c.width = canvas.width;
        c.height = sliceH;
        c.getContext('2d').drawImage(canvas, 0, offsetY, canvas.width, sliceH, 0, 0, canvas.width, sliceH);
        if (offsetY > 0) pdf.addPage();
        paintBeige();
        pdf.addImage(c.toDataURL('image/jpeg', 0.92), 'JPEG', 0, 0, imgW, sliceH * scale);
        offsetY += sliceH;
        remaining -= sliceH;
      }
    }
    const nameKebab = (state.clientName || 'client').trim().replace(/\s+/g, '-').toLowerCase();
    const date = state.sessionDate || new Date().toISOString().slice(0, 10);
    pdf.save(`roata-grow_${nameKebab}_${date}.pdf`);
  } catch (err) {
    alert('Eroare la export: ' + err.message);
  } finally {
    report.classList.remove('live');
    report.innerHTML = '';
    btn.disabled = false;
    btn.textContent = original;
  }
});
```

- [ ] **Step 3: Verify PDF export end-to-end**

Reload. Fill in:
- Nume: "Maria Popescu"
- Goal: "Să termin teza până la sfârșitul lunii."
- Commitment: 8
- Actions: 1 row with "scriu 500 cuvinte pe zi" + a date
- Obstacles: "Mă distrag rețelele sociale."

Click "Exportă PDF". Wait 2-3 secunde. Verify:
- Buton afișează "generez..." apoi revine
- Se descarcă fișier `roata-grow_maria-popescu_YYYY-MM-DD.pdf`
- Deschide PDF: vezi titlu, meta cu nume + dată, toate cele 4 secțiuni cu textul completat, Hotărâre 8/10, lista de acțiuni, obstacole, footer Calendly
- Câmpurile necompletate apar "— necompletat —" italic

- [ ] **Step 4: Commit**

```bash
git add roata-grow.html
git commit -m "export PDF A4 cu html2canvas + jspdf lazy-loaded"
```

---

## Task 10: Print stylesheet

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add print stylesheet**

Append inside the `<style>` block:

```css
  @media print {
    body { background: white; font-size: 12pt; }
    .page { padding: 0; max-width: none; }
    .actions-bar, .sessions-list { display: none; }
    .step {
      page-break-inside: avoid;
      border: 0.5pt solid #999;
      background: white;
      margin-bottom: 12pt;
    }
    .prompts { display: none; }
    textarea {
      border: none;
      background: transparent;
      min-height: 0;
      padding: 4pt 0;
    }
    h1 { font-size: 22pt; }
    footer.bottom { font-size: 9pt; }
  }
```

- [ ] **Step 2: Wire print button**

Append to the main script:

```js
document.getElementById('btn-print').addEventListener('click', () => window.print());
```

- [ ] **Step 3: Verify**

Reload. Fill in a couple of fields. Click "Tipărește" → browser print dialog appears. Preview shows clean layout without buttons or coaching prompts, just titles + content.

- [ ] **Step 4: Commit**

```bash
git add roata-grow.html
git commit -m "print stylesheet curat fara butoane/prompts"
```

---

## Task 11: Mobile responsive tweaks

**Files:**
- Modify: `roata-grow.html`

- [ ] **Step 1: Add mobile media query**

Append inside the `<style>` block:

```css
  @media (max-width: 600px) {
    .page { padding: 24px 18px 40px; }
    h1 { font-size: 30px; }
    .step { padding: 18px 18px; }
    .step-letter { font-size: 34px; }
    .step-title { font-size: 20px; }
    .step-sub { display: none; }
    .actions-bar { flex-direction: column; align-items: stretch; }
    .actions-bar .left, .actions-bar .right {
      margin-left: 0;
      justify-content: stretch;
    }
    .actions-bar .btn { flex: 1; text-align: center; }
    .scale-chip { width: 34px; height: 34px; font-size: 13px; }
    table.actions th, table.actions td { font-size: 12px; }
    .sessions-list { min-width: 240px; }
  }
```

- [ ] **Step 2: Verify on mobile viewport**

Reload. DevTools → Responsive mode → iPhone 12 (390×844).
- Padding redus, header compact
- Sub-text-urile etapelor ascunse
- Action bar verticală
- Chip-urile commitment se așază natural pe 2 rânduri dacă e nevoie
- Dropdown sesiuni mai îngust

- [ ] **Step 3: Commit**

```bash
git add roata-grow.html
git commit -m "mobile responsive sub 600px"
```

---

## Task 12: Add link card in index.html

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add the GROW card as 5th entry**

In `index.html`, find the `<div class="cards">` block. After the last existing `</a>` (the one for `roata-emotiilor.html`) and before `</div>` (closing `.cards`), insert:

```html

    <a class="card" href="roata-grow.html">
      <div class="card-label">Coaching structurat · acțiune</div>
      <h2>Roata <em>GROW</em></h2>
      <p>Worksheet după John Whitmore — patru întrebări care te poartă de la intenție la primul pas concret: Goal, Reality, Options, Will. Util la finalul unei sesiuni sau ca temă între ședințe.</p>
      <span class="card-cta">Deschide <span class="arrow">→</span></span>
    </a>
```

- [ ] **Step 2: Verify locally**

```bash
open index.html
```

A 5th card "Roata GROW" apare (singură pe ultimul rând al grid-ului 2×N). Click pe ea → încarcă `roata-grow.html`. Stilul e identic cu celelalte 4 cardul.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "index: card la Roata GROW"
```

---

## Task 13: Final sweep + push

**Files:** All

- [ ] **Step 1: Run through acceptance checklist from spec**

For each item in the spec's "Acceptance checklist", verify in browser:
- [ ] 4 secțiuni GROW cu prompt-urile corecte
- [ ] Will: commitment + acțiuni + obstacole
- [ ] Save / load / delete sesiuni
- [ ] PDF export funcționează
- [ ] Print arată decent
- [ ] Date inputs limitate la an 2099
- [ ] Nume strip-uiește cifre
- [ ] Tab navigation + screen reader OK
- [ ] Mobile arată bine
- [ ] Meta + favicon + OG (test cu https://www.opengraph.xyz/url/... după push)
- [ ] Link în index.html funcționează

- [ ] **Step 2: Final commit (if any cleanup) + push**

```bash
git status
# only commit if there's something to commit
git push
```

- [ ] **Step 3: Wait 1-2 min for GitHub Pages deploy, then test live**

Open on phone: https://anabrad.github.io/Roti-Coaching/roata-grow.html

Test on mobile: scroll, type, select commitment, add action, save, load, export PDF.

- [ ] **Step 4 (follow-up — not blocking):** Create 1200×630 social preview image and add as `og-roata-grow.png` in repo root.
