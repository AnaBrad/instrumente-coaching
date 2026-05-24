# Roata GROW — Design Spec

**Data:** 2026-05-24
**Autor:** Ana-Maria Brad
**Status:** Draft pentru review

## 1. Scop

Worksheet de coaching pe modelul **GROW** (John Whitmore): Goal — Reality — Options — Will.

Pagină web single-file, distribuită pe GitHub Pages alături de celelalte roti de coaching (`anabrad.github.io/Roti-Coaching/`). Servește trei use case-uri cu aceeași interfață:
- Sesiune live coach-client (Ana completează împreună cu clientul pe ecran partajat)
- Temă asincronă (clientul completează singur, aduce PDF la sesiunea următoare)
- Self-coaching (oricine intră pe pagină fără context de coaching)

## 2. Structură UI

Layout: o singură pagină, vertical, 4 secțiuni vizibile + bar de acțiuni.

```
Header (titlu + Nume + Data)
Intro (1 linie)
Step G — Goal
Step R — Reality
Step O — Options
Step W — Will (+ commitment + acțiuni + obstacole)
Action bar (salvare, sesiuni, reset, print, PDF)
Footer (© + Calendly)
```

### 2.1 Header
- `<h1>Roata <em>GROW</em></h1>` — Fraunces 300, accent italic pe "GROW"
- Subtitle: "Instrument de coaching · după John Whitmore"
- Input `Nume` (filtrare: doar litere; cifrele sunt strip-uite automat la `oninput`)
- Input `Data` (HTML5 date, `min="1900-01-01" max="2099-12-31"` pentru a garanta an de 4 cifre)

### 2.2 Intro
`<strong>De la intenție, la acțiune.</strong> Patru întrebări care te poartă de la ce vrei, la ce vei face concret.`

### 2.3 Cele 4 secțiuni GROW

Fiecare secțiune are aceeași structură vizuală:
- Literă mare cursivă (G/R/O/W) în Fraunces accent
- Titlu: cuvântul EN + traducere RO (ex: "Goal · Obiectiv")
- Sub-text uppercase mic (ex: "unde vrei să ajungi")
- Listă cu prompt-uri coaching italic (vizibile permanent, fără toggle)
- `<textarea>` mare (min-height 90px, resize vertical)

**Prompt-uri (versiunea aprobată din mockup):**

**G — Goal · Obiectiv** (unde vrei să ajungi)
- Ce vrei să obții, exact?
- Cum vei ști că ai reușit?
- E ceva în controlul tău, sau depinde de alții?
- Până când vrei să fie realizat?

**R — Reality · Realitate** (unde ești acum)
- Ce se întâmplă acum, concret?
- Ce ai încercat până acum?
- Ce funcționează? Ce nu funcționează?
- Cine sau ce te ține pe loc?

**O — Options · Opțiuni** (ce ai putea face)
- Ce ai putea face?
- Și ce altceva? Și?
- Dacă n-ai avea nicio limită, ce ai încerca?
- Cine te-ar putea ajuta?

**W — Will · Voință** (ce vei face)
- Ce *vei* face, concret?
- Care e primul pas, și până când?
- Ce te poate opri?

### 2.4 Will — extras

Sub textarea, separate cu border-top dashed:

**a) Commitment 1–10**
- Label: "Cât de hotărât/hotărâtă ești? (1–10)"
- 10 chip-uri rotunde (38×38px), una selectată activează `--accent` ca background
- Legendă: "deloc" — "total" (extreme dreapta-stânga sub chips)

**b) Acțiuni concrete**
- Tabel cu 2 coloane: `Ce voi face` (70%) | `Până când` (30%)
- 2 rânduri inițial, buton `+ adaugă rând` (dashed border)
- Input date are `max="2099-12-31"`, `width: auto; max-width: 160px`

**c) Obstacole anticipate**
- Label uppercase mic
- Textarea: `Ce-ar putea sta în cale? Cum răspunzi?`

### 2.5 Action bar

```
[Salvează sesiunea] [Sesiuni salvate ▾] [Resetează]    [Tipărește] [Exportă PDF]
```

- Salvează: localStorage cu nume (clientName + data + timestamp ca key)
- Sesiuni salvate: dropdown cu list-uri de sesiuni; click încarcă datele în formular (modal `customConfirm` cere confirmare dacă există date nesalvate)
- Resetează: customConfirm + curăță toate câmpurile (cele salvate rămân în istoric)
- Tipărește: `window.print()` cu `@media print` care ascunde butoanele
- Exportă PDF: html2canvas + jspdf lazy-loaded la primul click (pattern din roata-emotiilor)

### 2.6 Footer
`© Ana-Maria Brad · Instrument de coaching · [programează o sesiune de coaching →]` (link Calendly)

## 3. Persistență

Pattern identic cu `roata-increderii.html`:
- `localStorage` key prefix: `grow:`
- Schema sesiune:
  ```json
  {
    "clientName": "...",
    "sessionDate": "YYYY-MM-DD",
    "savedAt": "ISO8601",
    "goal": "...",
    "reality": "...",
    "options": "...",
    "will": "...",
    "commitment": 7,
    "actions": [{ "what": "...", "by": "YYYY-MM-DD" }],
    "obstacles": "..."
  }
  ```
- Dropdown "Sesiuni salvate" afișează: `{clientName} — {sessionDate}`
- Operații: load, delete (cu customConfirm)

## 4. PDF export

Pattern A4 portrait, identic cu `roata-emotiilor.html`:
- `pdf-report` div ascuns (`position: absolute; left: -10000px`) cu layout dedicat
- html2canvas → JPEG → jsPDF
- Background uniform `--paper`
- Footer cu URL Calendly
- Nume fișier: `roata-grow_{nume-kebab}_{data}.pdf`

Conținut PDF: titlu, meta (nume + dată), cele 4 secțiuni cu prompt-uri și răspunsuri, commitment 1-10 (vizual cu cifră evidențiată), tabel acțiuni, obstacole.

## 5. Style

Reutilizează design system-ul din celelalte roti:
- CSS variables: `--paper #f1e6d2`, `--surface #fbf4e3`, `--ink #2a1d10`, `--ink-soft #5a4530`, `--ink-mute #6e5a45`, `--accent #a05a25`, `--bordo #7a2336`, `--paper-deep #d9c69e`
- Font-uri: Fraunces (300/400 + italic) pentru titluri, Inter (400/500/600) pentru body
- Radius: 6px (mic) / 10px (mare)
- Border-uri: 0.5px

## 6. Accesibilitate

- `lang="ro"`
- Label-uri `.sr-only` pe inputs (Nume, Data, fiecare date din acțiuni)
- Contrast `--ink-mute` >= 4.5:1 pe `--paper` (folosim `#6e5a45`)
- `customConfirm` cu `role="dialog"`, `aria-modal`, focus trap, Escape, restaurare focus
- `<textarea>` cu label-uri legate (sr-only sau aria-label)
- Chip-urile de commitment sunt `<button>` cu `aria-pressed`

## 7. Mobile

- Layout vertical natural (nu sunt grid-uri laterale ca în roata-emotiilor)
- `@media (max-width: 600px)`: padding redus pe `.step`, tabel acțiuni cu coloane mai înguste
- Tap targets: chip-uri min 36×36px, butoane min 44px

## 8. Meta + favicon + SEO

- `<title>Roata GROW — Instrument de Coaching</title>`
- `<meta name="description">` cu rezumat pentru search/social
- OG tags (title, description, url, image, locale=ro_RO)
- Twitter card summary_large_image
- Inline SVG favicon (versiune stilizată cu 4 cercuri/sferturi: G/R/O/W cu paleta paper+accent)
- TODO: imagine OG 1200×630 separată (`og-roata-grow.png`)

## 9. Hosting & deploy

- File: `roata-grow.html` în root-ul repo-ului `AnaBrad/Roti-Coaching`
- Link în `index.html` (la celelalte roti) — TODO la implementare
- Live URL: `https://anabrad.github.io/Roti-Coaching/roata-grow.html`

## 10. Out of scope (deliberat exclus)

- Întrebări configurabile (prompt-urile sunt fixe)
- Multi-user/cloud sync (doar localStorage local)
- Export Word/Markdown
- Auto-save (clientul apasă Salvează explicit)
- A/B test pentru prompt-uri (precum `?q=on` la emoțiilor) — adăugabil ulterior dacă e nevoie

## 11. Acceptance checklist (la finalul implementării)

- [ ] 4 secțiuni GROW cu prompt-urile aprobate
- [ ] Will: commitment scale + tabel acțiuni + obstacole
- [ ] Save/Load/Delete sesiuni în localStorage funcționează
- [ ] PDF export A4 cu layout coerent
- [ ] Print arată decent (`@media print` ascunde butoanele)
- [ ] Toate input-urile date au `max="2099-12-31"`
- [ ] Nume strip-uiește cifre la `oninput`
- [ ] Accesibilitate: label-uri sr-only, contrast, focus trap în customConfirm
- [ ] Mobile responsive
- [ ] Meta + OG + favicon
- [ ] Adăugat link în `index.html`
