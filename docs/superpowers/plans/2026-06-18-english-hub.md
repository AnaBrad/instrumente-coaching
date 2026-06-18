# English Coaching Hub Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish a full English version of the coaching tools site under `en/`, with a clearly visible RO/EN language switch, English-translated instruments and hub, English OG images, and an updated Romanian hub tagline.

**Architecture:** Each page stays a self-contained HTML file (no build system), matching the existing repo. English files live in a new `en/` subfolder with English filenames. A small shared `RO | EN` toggle component is added to every page. Translation is faithful coaching English (not literal), using a shared glossary for consistency. OG images are rendered from brand-styled HTML templates via headless Chrome.

**Tech Stack:** Static HTML/CSS/JS, Google Fonts (Fraunces + Inter), GitHub Pages, headless Chrome for OG PNG rendering.

---

## Reference: shared translation glossary

Use these exact renderings everywhere for consistency. Translate all OTHER prose
faithfully and naturally (full sentences, warm coaching voice), not word-for-word.

| Romanian | English |
|---|---|
| ← Instrumente coaching (back link) | ← Coaching tools |
| Instrument de coaching | A coaching tool |
| după [Author] | based on [Author] |
| Datele rămân pe dispozitivul tău — nu se trimit nicăieri. | Your data stays on your device — nothing is sent anywhere. |
| Nume (input placeholder/label) | Name |
| Data (input label) | Date |
| Continuă / Continuă → | Continue / Continue → |
| Salvează sesiunea | Save session |
| Salvează PDF | Save PDF |
| Salvează roata ca PDF | Save the wheel as PDF |
| Salvează harta ca PDF | Save the map as PDF |
| Resetează | Reset |
| Șterge | Delete |
| Înapoi la listă | Back to list |
| Despre exercițiu | About this exercise |
| Despre exercițiu și ghid de facilitare | About this exercise & facilitation guide |
| Despre valori | About values |
| Despre bullying și ce nu e vina ta | About bullying and what isn't your fault |
| Programează o sesiune (30 min, gratuit) → | Book a session (30 min, free) → |
| Programează chemistry session (30 min, gratuit) → | Book a chemistry session (30 min, free) → |
| Programează o conversație (30 min, gratuit) → | Book a conversation (30 min, free) → |
| pentru adolescenți (in titles) | for teenagers |

**Keep in English already / do not change:** "chemistry session", "GROW", "WOOP",
"GRPI", brand name "Ana-Maria Brad", the Calendly URL.

**Audience labels (hub filter chips):** Toate→All, Adulți→Adults, Adolescenți→Teenagers, Echipă→Teams.

---

## Reference: language toggle component

Add to EVERY page (RO and EN), immediately after the existing back-link on
instruments, and in the hero on the hub. `aria-current="page"` marks the active
language. `HREF_OTHER` is the counterpart file (see per-task mapping).

CSS (add inside each page's `<style>`):

```css
  .lang-toggle {
    display: inline-flex;
    gap: 2px;
    border: 0.5px solid var(--paper-deep, #cdd9cc);
    border-radius: 999px;
    overflow: hidden;
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.06em;
  }
  .lang-toggle a {
    padding: 6px 14px;
    text-decoration: none;
    color: var(--ink-soft, #41513f);
    background: transparent;
    transition: background 0.15s, color 0.15s;
  }
  .lang-toggle a[aria-current="page"] {
    background: var(--accent, #4e7a52);
    color: #fbfcfa;
  }
  .lang-toggle a:not([aria-current]):hover { color: var(--accent, #4e7a52); }
```

HTML on a **Romanian** instrument page (active = RO):

```html
<nav class="lang-toggle" aria-label="Language">
  <a href="ROATA-NOASTRA.html" aria-current="page">RO</a>
  <a href="en/HREF_OTHER.html">EN</a>
</nav>
```

HTML on an **English** instrument page (active = EN, counterpart one level up):

```html
<nav class="lang-toggle" aria-label="Language">
  <a href="../RO_FILE.html">RO</a>
  <a href="HREF_OTHER.html" aria-current="page">EN</a>
</nav>
```

On the **hub** the toggle is larger and placed in the hero (see Task 9 / Task 1).

---

## Reference: file mapping

| RO file | EN file | EN title | audience |
|---|---|---|---|
| modelul-grow.html | en/grow-model.html | GROW Model | adult |
| modelul-grow-adolescenti.html | en/grow-model-teens.html | GROW Model for teenagers | teen |
| modelul-woop.html | en/woop-model.html | WOOP Model | adult |
| modelul-woop-adolescenti.html | en/woop-model-teens.html | WOOP Model for teenagers | teen |
| valori.html | en/personal-values.html | Personal Values | adult |
| valori-adolescenti.html | en/personal-values-teens.html | Personal Values for teenagers | teen |
| roata-vietii.html | en/wheel-of-life.html | Wheel of Life | adult |
| roata-vietii-adolescenti.html | en/wheel-of-life-teens.html | Wheel of Life for teenagers | teen |
| roata-increderii.html | en/wheel-of-confidence.html | Wheel of Confidence | adult |
| roata-increderii-adolescenti.html | en/wheel-of-confidence-teens.html | Wheel of Confidence for teenagers | teen |
| roata-echipei.html | en/team-wheel.html | Team Wheel | echipa |
| modelul-grpi.html | en/grpi-model.html | GRPI Model | echipa |
| credinte-limitative.html | en/limiting-beliefs.html | Limiting Beliefs | adult |
| credinte-limitative-adolescenti.html | en/limiting-beliefs-teens.html | Limiting Beliefs for teenagers | teen |
| cercul-de-control.html | en/circle-of-control.html | Circle of Control | adult |
| cercul-de-control-adolescenti.html | en/circle-of-control-teens.html | Circle of Control for teenagers | teen |
| roata-emotiilor.html | en/wheel-of-emotions.html | Wheel of Emotions | both |
| harta-energiei.html | en/energy-map.html | Energy Map | adult |
| harta-energiei-adolescenti.html | en/energy-map-teens.html | Energy Map for teenagers | teen |

`woop.html` (RO redirect) is NOT translated.

---

## Per-instrument translation procedure (applies to Tasks 2–8)

Each instrument task copies one or two RO files into `en/` and translates them.
For EACH file, the executing agent MUST:

1. `cp ROFILE.html en/ENFILE.html` then edit `en/ENFILE.html` in place.
2. Set `<html lang="en">`.
3. `<title>`: `EN_TITLE — A coaching tool` (e.g. `GROW Model — A coaching tool`).
4. `<meta name="description">`, `og:title`, `og:description`, `twitter:title`,
   `twitter:description`: translate faithfully from the RO equivalents.
5. `<meta property="og:locale">` → `en_US`.
6. `og:url`, `twitter:image`, `og:image`, `<link rel="canonical">`: point to the
   EN URL/asset, i.e. `https://anabrad.github.io/instrumente-coaching/en/ENFILE.html`
   and `.../en/og-ENBASE.png` (the EN OG image from Task 11; if Task 11 falls
   back to a shared cover, use `.../en/og-cover.png`).
7. Back-link text → `← Coaching tools`; its `href` stays `index.html` (resolves
   to `en/index.html`).
8. Insert the **English instrument toggle** (see component above) right after the
   back-link, with `RO` linking `../ROFILE.html` and `EN` linking `ENFILE.html`
   (active). Add the `.lang-toggle` CSS into the page `<style>`.
9. Translate ALL visible copy: header h1/subtitle, intro, privacy note, every
   step/section, labels, buttons, tooltips, empty states, confirmation dialogs,
   the "About"/facilitation sections, AND any strings inside the JavaScript
   (PDF export text, alert/confirm messages, dynamically built labels). Use the
   glossary for recurring strings; translate the rest naturally.
10. Do NOT change layout, palette, fonts, IDs, classes, or logic — copy only.

**Per-file verification (run after each file):**
- `grep -c 'lang="en"' en/ENFILE.html` → expect ≥1, and `grep -c 'lang="ro"'` → 0.
- Romanian-leftover scan (diacritics are the tell):
  `grep -nE '[ăâîșțĂÂÎȘȚ]' en/ENFILE.html` → expect no matches in visible copy
  (proper names without diacritics are fine; investigate any hits).
- Open the file in a browser; confirm the page renders, the toggle shows EN
  active, the RO link points back correctly, and the PDF export produces English.

---

### Task 1: Scaffold `en/` and define the toggle on a real page (GROW adult)

This task establishes the pattern end-to-end on one file before fanning out.

**Files:**
- Create: `en/grow-model.html`

- [ ] **Step 1: Create the `en/` folder and translate GROW (adult)**

Follow the *Per-instrument translation procedure* for `modelul-grow.html` →
`en/grow-model.html`, title `GROW Model`. RO link `../modelul-grow.html`,
EN link `grow-model.html`.

- [ ] **Step 2: Verify**

```bash
cd "$(git rev-parse --show-toplevel)"
grep -c 'lang="en"' en/grow-model.html   # ≥1
grep -c 'lang="ro"' en/grow-model.html   # 0
grep -nE '[ăâîșțĂÂÎȘȚ]' en/grow-model.html   # no visible-copy hits
```
Open `en/grow-model.html` in a browser: page renders, toggle EN active, RO link
works, PDF export is English.

- [ ] **Step 3: Commit**

```bash
git add en/grow-model.html
git commit -m "feat(en): GROW Model (English) + en/ scaffold"
```

---

### Task 2: Translate GROW teens + WOOP (adult + teens)

**Files:**
- Create: `en/grow-model-teens.html`, `en/woop-model.html`, `en/woop-model-teens.html`

- [ ] **Step 1: Translate the three files**

Apply the per-instrument procedure to:
- `modelul-grow-adolescenti.html` → `en/grow-model-teens.html` (GROW Model for teenagers)
- `modelul-woop.html` → `en/woop-model.html` (WOOP Model)
- `modelul-woop-adolescenti.html` → `en/woop-model-teens.html` (WOOP Model for teenagers)

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/grow-model-teens.html en/woop-model.html en/woop-model-teens.html
git commit -m "feat(en): GROW teens + WOOP (adult + teens)"
```

---

### Task 3: Translate Personal Values (adult + teens)

**Files:**
- Create: `en/personal-values.html`, `en/personal-values-teens.html`

- [ ] **Step 1: Translate** `valori.html` → `en/personal-values.html` (Personal Values)
  and `valori-adolescenti.html` → `en/personal-values-teens.html` (Personal Values
  for teenagers). These are the largest files (lots of value-card terms); translate
  every value name and its description.

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/personal-values.html en/personal-values-teens.html
git commit -m "feat(en): Personal Values (adult + teens)"
```

---

### Task 4: Translate Wheel of Life (adult + teens)

**Files:**
- Create: `en/wheel-of-life.html`, `en/wheel-of-life-teens.html`

- [ ] **Step 1: Translate** `roata-vietii.html` → `en/wheel-of-life.html` (Wheel of
  Life) and `roata-vietii-adolescenti.html` → `en/wheel-of-life-teens.html` (Wheel
  of Life for teenagers). Translate all 8 area labels and prompts.

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/wheel-of-life.html en/wheel-of-life-teens.html
git commit -m "feat(en): Wheel of Life (adult + teens)"
```

---

### Task 5: Translate Wheel of Confidence (adult + teens)

**Files:**
- Create: `en/wheel-of-confidence.html`, `en/wheel-of-confidence-teens.html`

- [ ] **Step 1: Translate** `roata-increderii.html` → `en/wheel-of-confidence.html`
  (Wheel of Confidence) and `roata-increderii-adolescenti.html` →
  `en/wheel-of-confidence-teens.html` (Wheel of Confidence for teenagers).

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/wheel-of-confidence.html en/wheel-of-confidence-teens.html
git commit -m "feat(en): Wheel of Confidence (adult + teens)"
```

---

### Task 6: Translate team tools — Team Wheel + GRPI Model

**Files:**
- Create: `en/team-wheel.html`, `en/grpi-model.html`

- [ ] **Step 1: Translate** `roata-echipei.html` → `en/team-wheel.html` (Team Wheel)
  and `modelul-grpi.html` → `en/grpi-model.html` (GRPI Model). Keep "GRPI" and its
  pillar names (Goals, Roles, Processes, Relationships) in English.

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/team-wheel.html en/grpi-model.html
git commit -m "feat(en): Team Wheel + GRPI Model"
```

---

### Task 7: Translate Limiting Beliefs + Circle of Control (adult + teens)

**Files:**
- Create: `en/limiting-beliefs.html`, `en/limiting-beliefs-teens.html`,
  `en/circle-of-control.html`, `en/circle-of-control-teens.html`

- [ ] **Step 1: Translate** the four files:
- `credinte-limitative.html` → `en/limiting-beliefs.html` (Limiting Beliefs)
- `credinte-limitative-adolescenti.html` → `en/limiting-beliefs-teens.html` (Limiting Beliefs for teenagers)
- `cercul-de-control.html` → `en/circle-of-control.html` (Circle of Control)
- `cercul-de-control-adolescenti.html` → `en/circle-of-control-teens.html` (Circle of Control for teenagers)

  For Limiting Beliefs keep the Byron Katie four-question framing faithful. For
  Circle of Control teens, translate the "About bullying…" section.

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/limiting-beliefs.html en/limiting-beliefs-teens.html en/circle-of-control.html en/circle-of-control-teens.html
git commit -m "feat(en): Limiting Beliefs + Circle of Control (adult + teens)"
```

---

### Task 8: Translate Wheel of Emotions + Energy Map (adult + teens)

**Files:**
- Create: `en/wheel-of-emotions.html`, `en/energy-map.html`, `en/energy-map-teens.html`

- [ ] **Step 1: Translate** the three files:
- `roata-emotiilor.html` → `en/wheel-of-emotions.html` (Wheel of Emotions; audience
  "both"). Translate all 8 emotions × 3 intensities (Plutchik adaptation).
- `harta-energiei.html` → `en/energy-map.html` (Energy Map)
- `harta-energiei-adolescenti.html` → `en/energy-map-teens.html` (Energy Map for teenagers)

- [ ] **Step 2: Verify each** with the per-file verification checks.

- [ ] **Step 3: Commit**

```bash
git add en/wheel-of-emotions.html en/energy-map.html en/energy-map-teens.html
git commit -m "feat(en): Wheel of Emotions + Energy Map (adult + teens)"
```

---

### Task 9: Build the English hub (`en/index.html`)

**Files:**
- Create: `en/index.html` (translate from `index.html`)

- [ ] **Step 1: Create and translate the hub**

```bash
cp index.html en/index.html
```
Then edit `en/index.html`:
- `<html lang="en">`.
- `<title>`: `Coaching tools · Ana-Maria Brad`.
- Eyebrow: `Ana-Maria Brad · coach`.
- H1: `Coaching <em>tools</em>`.
- Lead (reflecting teams): `Visual tools for coaching sessions — 1:1, with teams,
  or for working on your own.`
- All meta/OG/Twitter/canonical → English copy, `og:locale` `en_US`,
  `og:url`/canonical = `https://anabrad.github.io/instrumente-coaching/en/`,
  `og:image`/`twitter:image` = `.../en/og-cover.png`.
- Filter chips text: `All`, `Adults`, `Teenagers`, `Teams` (keep `data-audience`
  values `all`/`adult`/`teen`/`echipa` and the JS unchanged). Update the
  `aria-label` to `Filter tools by audience` and chip labels; the count `<span>`
  injection JS stays as-is.
- Each card: translate `card-label`, `<h2>` (use the EN titles from the mapping),
  and `<p>` description faithfully. Update every `href` to the EN filename from
  the mapping (e.g. `modelul-grow.html` → `grow-model.html`). `data-audience`
  values stay unchanged.
- Footer CTA: `Book a coaching session →` (Calendly URL unchanged).
- Signature: `© Ana-Maria Brad · Coaching tools`.

- [ ] **Step 2: Add the hero language toggle**

Add the `.lang-toggle` CSS (from the component reference) to the hub `<style>`,
plus this rule so it sits at the top of the hero:

```css
  .hero .lang-toggle { margin: 0 auto 20px; }
```

Insert at the very top of `.hero`, before the eyebrow:

```html
    <nav class="lang-toggle" aria-label="Language">
      <a href="../index.html">RO</a>
      <a href="index.html" aria-current="page">EN</a>
    </nav>
```

- [ ] **Step 3: Verify**

```bash
grep -c 'lang="en"' en/index.html            # ≥1
grep -nE '[ăâîșțĂÂÎȘȚ]' en/index.html         # no visible-copy hits
grep -oE 'href="[a-z-]+\.html"' en/index.html # all EN filenames, none Romanian
```
Open `en/index.html`: 19 cards, filters work (All/Adults/Teenagers/Teams), every
card links to an existing `en/*.html`, toggle shows EN active and RO links to `../`.

- [ ] **Step 4: Commit**

```bash
git add en/index.html
git commit -m "feat(en): English hub"
```

---

### Task 10: Romanian-side updates — toggle on every RO page + tagline fix

**Files:**
- Modify: `index.html` and all 19 translated RO instrument files (every RO file
  that now has an EN counterpart in the mapping; NOT `woop.html`).

- [ ] **Step 1: Fix the RO hub tagline**

In `index.html`, replace the lead text
`Instrumente vizuale pentru sesiuni de coaching. Pentru sesiuni 1:1 sau pentru lucrul cu sine.`
with
`Instrumente vizuale pentru sesiuni de coaching 1:1, pentru echipe sau pentru lucrul cu sine.`
Also update the `<meta name="description">`, `og:description`, and
`twitter:description` in `index.html` where they repeat "Pentru sesiuni 1:1 sau
pentru lucrul cu sine." to the teams-inclusive phrasing.

- [ ] **Step 2: Add the RO hero toggle to `index.html`**

Add the `.lang-toggle` CSS + `.hero .lang-toggle { margin: 0 auto 20px; }` to the
RO hub `<style>`, and insert at the top of `.hero`:

```html
    <nav class="lang-toggle" aria-label="Language">
      <a href="index.html" aria-current="page">RO</a>
      <a href="en/index.html">EN</a>
    </nav>
```

- [ ] **Step 3: Add the toggle to every RO instrument**

For each RO instrument file in the mapping, add the `.lang-toggle` CSS to its
`<style>` and insert, right after the existing back-link, the Romanian-instrument
toggle (RO active, EN → `en/ENFILE.html`):

```html
<nav class="lang-toggle" aria-label="Language">
  <a href="ROFILE.html" aria-current="page">RO</a>
  <a href="en/ENFILE.html">EN</a>
</nav>
```
(Substitute each file's own name for `ROFILE.html` and its mapped `en/ENFILE.html`.)

- [ ] **Step 4: Verify**

```bash
# every translated RO instrument now has a toggle linking into en/
for f in modelul-grow modelul-grow-adolescenti modelul-woop modelul-woop-adolescenti \
  valori valori-adolescenti roata-vietii roata-vietii-adolescenti roata-increderii \
  roata-increderii-adolescenti roata-echipei modelul-grpi credinte-limitative \
  credinte-limitative-adolescenti cercul-de-control cercul-de-control-adolescenti \
  roata-emotiilor harta-energiei harta-energiei-adolescenti; do
    grep -q 'class="lang-toggle"' "$f.html" || echo "MISSING toggle: $f.html"
done
grep -q 'pentru echipe' index.html && echo "RO tagline updated"
```
Open `index.html` and one RO instrument: toggle visible, RO active, EN link goes
to the matching `en/` page.

- [ ] **Step 5: Commit**

```bash
git add index.html *.html
git commit -m "feat: RO/EN toggle on all Romanian pages + teams-inclusive hub tagline"
```

---

### Task 11: English OG images

**Files:**
- Create: `en/og-templates/*.html` (throwaway render sources), `en/og-*.png`

- [ ] **Step 1: Inventory the OG images referenced by EN pages**

```bash
grep -hoE 'og-[a-z-]+\.png' en/*.html | sort -u
```
This is the list of `en/og-*.png` files to produce (cover + one per tool family).

- [ ] **Step 2: Build brand-styled HTML templates (1200×630)**

For each needed image create `en/og-templates/<name>.html`: a 1200×630 page using
the paper palette (`--paper #eef2ec`, `--accent #4e7a52`, ink `#1e2a1f`), Fraunces
for the title and Inter for the subtitle, mirroring the look of the existing RO OG
PNGs but with the English title (from the mapping) and a short English subtitle.
Center the title; add `· Ana-Maria Brad` small at the bottom.

- [ ] **Step 3: Render each template to PNG with headless Chrome**

```bash
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
for t in en/og-templates/*.html; do
  name=$(basename "$t" .html)
  "$CHROME" --headless --disable-gpu --hide-scrollbars \
    --window-size=1200,630 --default-background-color=00000000 \
    --screenshot="en/${name}.png" "file://$(pwd)/$t"
done
ls -la en/og-*.png
```
Expected: a non-empty PNG per template. Open a couple to confirm fonts loaded and
layout is correct.

- [ ] **Step 4: Fallback if rendering misbehaves**

If Chrome isn't found, fonts don't load, or output is blank/wrong: produce a
single neutral `en/og-cover.png` (brand background + "Coaching tools · Ana-Maria
Brad", no tool-specific text) and point every EN page's `og:image`/`twitter:image`
at `.../en/og-cover.png`. Note in the commit message that per-tool EN OG images
are deferred.

- [ ] **Step 5: Verify references resolve**

```bash
# every og:image referenced by an EN page must exist on disk
for img in $(grep -hoE 'en/og-[a-z-]+\.png' en/*.html | sed 's#en/##' | sort -u); do
  test -f "en/$img" || echo "MISSING image: en/$img"
done
```

- [ ] **Step 6: Commit**

```bash
git add en/og-*.png en/og-templates
git commit -m "feat(en): English OG preview images"
```

---

### Task 12: hreflang cross-links + final verification

**Files:**
- Modify: all RO + EN page `<head>`s (add hreflang alternates)

- [ ] **Step 1: Add hreflang alternates**

In the `<head>` of each RO/EN page pair, add (using each page's real URLs):

```html
<link rel="alternate" hreflang="ro" href="https://anabrad.github.io/instrumente-coaching/ROFILE.html">
<link rel="alternate" hreflang="en" href="https://anabrad.github.io/instrumente-coaching/en/ENFILE.html">
<link rel="alternate" hreflang="x-default" href="https://anabrad.github.io/instrumente-coaching/ROFILE.html">
```
For the hubs use the directory URLs (`.../instrumente-coaching/` and `.../en/`).

- [ ] **Step 2: Full-site verification**

```bash
cd "$(git rev-parse --show-toplevel)"
echo "EN files present:"; ls en/*.html | wc -l    # expect 20 (19 tools + hub)
echo "Romanian leftovers in en/ (visible copy):"
grep -lnE '>[^<]*[ăâîșțĂÂÎȘȚ]' en/*.html || echo "none"
echo "Broken internal links in en/ hub:"
for h in $(grep -oE 'href="[a-z-]+\.html"' en/index.html | sed 's/href="//;s/"//'); do
  test -f "en/$h" || echo "BROKEN: en/$h"
done
echo "All EN pages declare lang=en:"
grep -L 'lang="en"' en/*.html || echo "all good"
```

- [ ] **Step 3: Manual smoke test (live preview)**

Serve locally and click through:
```bash
python3 -m http.server 8080
```
Visit `http://localhost:8080/en/`, open 3–4 tools incl. a teen and a team tool,
test the RO↔EN toggle both directions, run a PDF export, confirm English
throughout. Then push and review on GitHub Pages.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "feat: hreflang alternates linking RO and EN; final cross-link pass"
```

---

## Self-review notes

- **Spec coverage:** §1 routing → Tasks 1–9 + 12 (hreflang); §2 toggle → component
  ref + Tasks 1–10; §3 instruments → Tasks 1–8 (all 19) + glossary; hub/filters →
  Task 9; §4 OG images → Task 11 (with fallback); §5 RO tagline → Task 10; §6
  delivery → Task 12 smoke test + push. All spec sections mapped.
- **No placeholders:** recurring strings fixed in the glossary; the open-ended
  prose translation is inherent to a translation project and is bounded by the
  per-instrument procedure + per-file verification, not left vague.
- **Consistency:** EN filenames, titles, and `og:image` names use the single
  mapping table throughout; toggle markup is defined once and referenced.
