# Credințe limitative — design

A new coaching instrument for the `instrumente-coaching` site that walks the user through Byron Katie's "The Work": one limiting belief, four questions, three turnarounds with three examples each, ending in a printable summary.

## Goals

- Add an 8th instrument to the collection, filling the gap between assessment (the four wheels) and action (GROW/WOOP) — a tool for working *with* a thought rather than around it.
- Stay faithful to Byron Katie's canonical method (no JYN worksheet, but the full 4 questions + 3 turnarounds × 3 examples).
- Match the existing site's aesthetic, code conventions, and shipping pattern exactly: single static HTML file, paper palette, paginated screens.

## Non-goals

- No JYN ("Judge Your Neighbor") worksheet entry point.
- No adolescent-adapted version (a separate card may be added later, parallel to Roata Vieții / Roata Vieții adolescenți).
- No localStorage, no session resume, no analytics, no backend.
- No language toggle.

## File

`credinte-limitative.html` at the root of the repo, alongside `modelul-woop.html`, `roata-vietii.html`, etc. No build step, no dependencies beyond Google Fonts (Fraunces + Inter) already used elsewhere.

## Method

Byron Katie's "The Work" — short path:

1. Identify a limiting belief.
2. Q1: E adevărat? (Da / Nu)
3. Q2: Pot ști cu absolută certitudine că e adevărat? (Da / Nu)
4. Q3: Cum reacționez, ce se întâmplă, când cred acest gând? (free text)
5. Q4: Cine aș fi fără acest gând? (free text)
6. Răsturnări — three, each with one statement + three concrete examples of how it could be as true or truer:
   - **La opus** — invert the belief.
   - **La sine** — turn it toward yourself.
   - **La celălalt** — turn it toward the other person or the situation.

## Audience

Adults. The tool will be filed under "Adulți · credințe limitative" on the index card.

## Card on `index.html`

Position: between Roata Încrederii and Roata Emoțiilor (groups it with the existing "adulți" cluster).

- Label: `Adulți · credințe limitative`
- Title: `Credințe <em>limitative</em>`
- Body: *"Patru întrebări după Byron Katie. Iei o credință care te limitează, o cercetezi în liniște și o întorci pe toate părțile. Util când anumite gânduri se simt adevărate doar pentru că revin des."*
- Link: `credinte-limitative.html`

## Index lead copy update

Replace:
> Instrumente vizuale pentru sesiuni de coaching. Câte 10 minute, fără cont, fără date trimise.

With:
> Instrumente vizuale pentru sesiuni de coaching. Scurte. Fără cont, fără date trimise.

Update the matching `<meta name="description">` and OG/Twitter description tags on `index.html` to the new wording (drop the "Câte 10 minute" claim).

## Flow — 10 screens

Each screen is a `<section class="screen">` toggled via the `hidden` attribute, same pattern as `modelul-woop.html`. Smooth scroll-to-top on transition. Required-field gates are enforced in JS, not via HTML `required` (so the back-link doesn't trigger native validation).

1. **Landing**
   - Title: `Credințe <em>limitative</em>` (Fraunces, italic on "limitative", accent color).
   - Subtitle: `Cele 4 întrebări · după Byron Katie`.
   - Short intro paragraph explaining the practice and the privacy stance ("nimic nu se trimite, nimic nu se salvează").
   - Single "Începe" primary button.

2. **Credința**
   - Single `<textarea>` with placeholder `Ex: „Nu sunt suficient de bun(ă)."`.
   - Helper text below the field: *"Scrie o credință care te limitează. Fă-o scurtă, la persoana întâi sau a treia, exact așa cum sună în mintea ta."*
   - **Required** — Continuă button disabled while empty/whitespace-only.

3. **Întrebarea 1 — E adevărat?**
   - Echo of the belief at top, centered, in Fraunces italic, accent-deep color, paper-deep dashed border above and below.
   - Two large radio-style buttons: `Da` / `Nu`. One must be selected.
   - Optional `<textarea>` below: *"Ce ai observat?"*.
   - **Required**: a Da/Nu choice. Note is optional.

4. **Întrebarea 2 — Pot ști cu absolută certitudine că e adevărat?**
   - Same structure as Q1.

5. **Întrebarea 3 — Cum reacționez, ce se întâmplă, când cred acel gând?**
   - Echo of the belief at top (same component as Q1/Q2).
   - Single `<textarea>`.
   - Helper text below, in `--ink-mute`: *"Ce emoții apar? Cum te porți cu tine? Cu ceilalți? Ce imagini, amintiri, ce viitor îți vin?"*
   - **Not required** — free to leave empty and continue.

6. **Întrebarea 4 — Cine ai fi fără acel gând?**
   - Same shape as Q3.
   - Helper: *"Imaginează-te în aceeași situație, dar fără credința aceasta. Cine ești?"*

7. **Răsturnarea 1 — la opus**
   - Explainer block: *"Inversează credința. Ex: «Nu sunt suficient de bun» → «Sunt suficient de bun»."*.
   - One `<textarea>` for the turnaround statement.
   - Three numbered `<textarea>`s for examples, labeled *"Exemplu 1 — unde e la fel de adevărat sau mai adevărat?"*, *"Exemplu 2 — …"*, *"Exemplu 3 — …"*.
   - **Not required** — Byron Katie's practice tolerates incomplete output; the user advances when ready.

8. **Răsturnarea 2 — la sine**
   - Explainer: *"Întoarce credința către tine. Ex: «El nu mă respectă» → «Eu nu mă respect (în această situație)»."*.
   - Same 1 + 3 fields.

9. **Răsturnarea 3 — la celălalt**
   - Explainer: *"Întoarce credința către celălalt sau către situație. Ex: «El nu mă respectă» → «Eu nu îl respect»."*.
   - Same 1 + 3 fields.
   - If the belief contains no second party, the helper text suggests addressing the situation, life, or oneself in that situation.

10. **Rezumat**
    - Full worksheet rendered from the in-memory state:
      - Header with title and the "După Byron Katie" subtitle.
      - **Nume + Data** inputs in the top-right of the header, same convention as Roata Vieții (see "Session info" section below).
      - The belief, centered, in the Q1/Q2 echo style.
      - Four `.block` sections, one per question. Q1/Q2 show a `.answer-pill` (`Da` or `Nu`) followed by the optional note. Q3/Q4 show the note as paragraph text.
      - Three `.turnaround-block` sections, each showing its title, the turnaround statement (italic, accent-deep), and the three examples (left-bordered, paper-deep).
      - Empty fields are gracefully hidden — if the user skipped a note or an example, no empty block is rendered.
    - Two buttons in the action row:
      - `Salvează PDF` (primary, accent background) — calls `window.print()`.
      - `Începe din nou` (ghost) — resets in-memory state and returns to landing. No confirmation dialog (nothing is saved that could be lost).

## Navigation

- Each non-landing screen has a back-link in the top-left (`← înapoi`) that returns to the previous screen without resetting state.
- Each non-landing screen has a `Continuă` primary button at the bottom-right.
- The landing screen has `Începe`. The Rezumat screen replaces `Continuă` with the two action buttons described above.
- Forward navigation on Q1/Q2 is gated until Da/Nu is selected. Forward navigation on Credința is gated until the textarea is non-empty.
- Back navigation is never gated.

## Session info (Nume + Data)

Two inputs in a `.session-info` block — same DOM and styling convention as `roata-vietii.html`:

- `<input type="text" placeholder="Nume" autocomplete="off">` with an `sr-only` label.
- `<input type="date">` with an `sr-only` label, prepopulated with today's date on page load.

Behaviour:

- Renders in the top-right of the Rezumat screen header.
- Nume is optional; if empty at print time, the PDF antet shows just the date.
- Data is editable — coach can backdate or forward-date the session.
- Both stored only in JS state; never persisted, never transmitted.

Rationale: coaches decide how to use the tool. Some will write the client's name, some their own, some leave it blank — the field is a placeholder for that judgment, not a requirement.

## Print / PDF layout

`@media print`:

- Hide all nav, back-links, action buttons, and the browser-visible footer.
- Hide all screens except the Rezumat.
- Expand the Rezumat into a clean A4-portrait worksheet:
  - Title in Fraunces at top, italic em on "limitative".
  - Subtitle `După Byron Katie`.
  - Meta row: `${nume} · ${dateFmt}` if name is non-empty, or just `${dateFmt}` if name is blank. Date format: `Intl.DateTimeFormat('ro-RO', { day: 'numeric', month: 'long', year: 'numeric' })` — matches Roata Vieții.
  - Belief block (dashed top/bottom borders, Fraunces italic).
  - Four question blocks. Q1/Q2 inline-display the Da/Nu pill before the note. Q3/Q4 show the note as paragraph.
  - Three turnaround blocks with statement + examples.
  - Footer centered: `Lucrul cu credințele limitative · după Byron Katie · anabrad.github.io/instrumente-coaching`.
- Use `page-break-inside: avoid` on each `.block` and `.turnaround-block` so a block doesn't split across pages mid-thought.

## State

A single JavaScript object held in module-scope variables (no class needed):

```js
const state = {
  belief: "",
  q1: { answer: null, note: "" },   // answer: "da" | "nu" | null
  q2: { answer: null, note: "" },
  q3: "",
  q4: "",
  ta1: { statement: "", examples: ["", "", ""] },
  ta2: { statement: "", examples: ["", "", ""] },
  ta3: { statement: "", examples: ["", "", ""] },
  name: "",
  date: "",   // ISO yyyy-mm-dd, defaulted to today on page load
};
```

On every input/change, the state is updated from the DOM. On navigation to the Rezumat screen, `renderSummary()` reads the state and builds the summary DOM. `Începe din nou` resets the state to defaults and clears all form fields.

## Visual / typographic conventions

- Reuse the existing CSS variables verbatim: `--paper`, `--paper-deep`, `--surface`, `--ink`, `--ink-soft`, `--ink-mute`, `--accent`, `--accent-deep`, `--bordo`.
- Fonts: Fraunces (display, italic for em), Inter (body and inputs).
- Buttons follow `modelul-woop.html`'s `.cta-primary` / ghost button pattern.
- Inputs follow `roata-vietii.html`'s `.session-info input` styling (transparent background, single underline, accent on focus).

## Favicon

Inline SVG, same engraved-paper style as the other tools. Pattern: a circle with `4?` in Fraunces italic — invokes the four questions. Final SVG to be authored at implementation time; placeholder if needed.

## Out of scope (YAGNI)

- No save/resume across sessions.
- No adolescent variant in this file.
- No analytics, tracking, or backend.
- No language toggle.
- No share/copy-as-text button (PDF covers export).
- No JYN worksheet.

## Open question

None — all decisions confirmed during brainstorming and validated against the visual mockup.
