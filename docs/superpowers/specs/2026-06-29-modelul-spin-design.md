# Modelul SPIN — instrument de coaching pentru echipe

**Data:** 2026-06-29
**Autor:** Ana-Maria Brad (cu Claude)
**Status:** spec, în așteptarea aprobării

## Scop

Un instrument vizual de coaching pentru echipe, bazat pe modelul SPIN al lui
Neil Rackham (Situation, Problem, Implication, Need-payoff). Spre deosebire de
GRPI (diagnostic scorat al întregii echipe) și Roata echipei (radiografie în
timp), SPIN este o **conversație structurată pe o singură provocare**: echipa
alege un subiect și parcurge cele patru tipuri de întrebări până când nevoia de
a-l rezolva devine clară și împărtășită, înainte de a sări la soluții.

Locul în colecție:
- Roata echipei = cum evoluează echipa în timp (radiografie)
- GRPI = unde e blocajul structural (diagnostic)
- **SPIN = lucrul pe o provocare concretă (conversație)**

## Decizii luate la brainstorming

1. **Două scale ușoare 0–10**, nu scor pe fiecare literă: o scală de *urgență*
   la pasul Implicații și o scală de *pregătire / motivație* la pasul Nevoie și
   câștig. Restul rămâne calitativ (întrebări + note).
2. **Română întâi.** Versiunea engleză (`en/spin-model.html`) este un pas de
   follow-up separat, în afara acestui spec.

## Arhitectură

Fișier unic, autonom: `modelul-spin.html`, după exact același tipar ca
`modelul-grpi.html`:
- HTML + CSS + JS într-un singur fișier, fără build
- Paleta paper cognac (aceleași variabile `:root`)
- Fonturi Fraunces + Inter
- Persistență în `localStorage` (prefix `spin:`)
- Export PDF (html2canvas + jsPDF, lazy-loaded)
- Dialog `customConfirm` propriu
- Script analytics umami (același `data-website-id`)

Se reutilizează aproape integral mecanica din GRPI (state, renderScale,
salvare/încărcare sesiuni, dropdown sesiuni, flash, PDF). Diferă: numărul și
conținutul pașilor, câmpul de provocare din antet, faptul că doar doi pași au
scală.

### Structura paginii (de sus în jos)

1. `back-link` către `index.html` + `lang-toggle` (RO activ, EN către
   `en/spin-model.html`)
2. `header.top`: titlu „Modelul *SPIN*”, subtitlu „Instrument de coaching ·
   după Neil Rackham”; în dreapta: input „Nume echipă” + input dată
3. `.intro`: o frază care explică modelul
4. **Câmp provocare** (nou față de GRPI): un câmp text „Provocarea pe care o
   explorăm” sub intro, deasupra pașilor. O singură provocare per sesiune.
5. `details.about`: „Despre exercițiu și ghid de facilitare” (colapsat)
6. `.privacy-note`: „Datele rămân pe dispozitivul tău — nu se trimit nicăieri.”
7. Patru `.step` (S, P, I, N) — vezi conținut mai jos
8. `.step` „Planul de acțiune” (un singur textarea, ca în GRPI)
9. `.actions-bar`: Salvează sesiunea / Sesiuni salvate / Resetează / Salvează PDF
10. `.next-step` CTA (ascuns până la salvare/PDF)
11. `footer.bottom`

## Conținut (copy RO, sentence case, cu diacritice)

### Intro
> **O conversație structurată pe o singură provocare.** SPIN vine de la
> Situație, Problemă, Implicații, Nevoie și câștig: patru tipuri de întrebări,
> după Neil Rackham, care duc echipa de la fapte la o nevoie resimțită
> împreună, înainte de a sări la soluții.

### Câmp provocare
- Label vizibil: „Provocarea pe care o explorăm”
- Placeholder: „Scrie aici subiectul pe care îl ia echipa, pe scurt...”
- **Exemple sub câmp (chip-uri clicabile):** un rând cu textul „Exemple (apasă
  ca să completezi):” urmat de chip-uri; click pe un chip umple câmpul de
  provocare (text editabil după). Chip-urile poartă textul scurt, iar câmpul se
  completează cu varianta lungă, completă.

  | Text chip (scurt) | Completează câmpul cu (lung) |
  |---|---|
  | Ședințe lungi, fără decizii | Ședințele noastre durează mult și se termină fără decizii |
  | Ratăm termene | Ratăm termene și nu e clar de ce |
  | Două părți nu comunică | Două părți ale echipei nu comunică bine |
  | Ne ferim de feedback | Ne ferim să ne dăm feedback unii altora |
  | Muncă împărțită inegal | Munca e împărțită inegal și unii sunt suprasolicitați |
  | Moral scăzut după reorganizare | După reorganizare, moralul a scăzut |

### Despre exercițiu și ghid de facilitare (colapsat)
- Ce este SPIN și de unde vine: cele patru tipuri de întrebări ale lui Rackham,
  apărute inițial în vânzări consultative, folosite aici pentru a ajuta o echipă
  să-și înțeleagă singură o provocare.
- De ce contează ordinea: nu propui soluții până când echipa nu simte nevoia.
  Întrebările de Implicații și de Nevoie sunt cele care mută echipa, nu
  răspunsurile date de facilitator.
- Ghid de facilitare:
  - Alegeți o singură provocare. SPIN lucrează în adâncime pe un subiect, nu pe
    toate deodată.
  - Stați la Situație până faptele sunt aceleași pentru toți. Abia apoi treceți
    la Problemă.
  - Implicațiile sunt inima conversației: lăsați echipa să simtă costul, nu vă
    grăbiți spre soluții.
  - Nevoia și câștigul trebuie să vină de la echipă. Întrebați „ce v-ar aduce”,
    nu „de ce ar trebui”.
  - Închideți cu un singur pas mic, asumat de cineva anume.

### Pasul S · Situație
- Lead: „Faptele, fără interpretări. Unde se află echipa acum, concret, în
  legătură cu provocarea aleasă.”
- Întrebare-cheie: „Care e situația, așa cum o vedem cu toții, fără să o
  explicăm încă?”
- Întrebări de explorare:
  - Ce se întâmplă concret acum, în exemple sau cifre, nu în păreri?
  - De cât timp e așa și ce s-a încercat până acum?
  - Ce e deja stabilit și nu mai e de discutat despre situație?
  - Vedem cu toții aceleași fapte, sau avem imagini diferite?
- Note (textarea)

### Pasul P · Problemă
- Lead: „Unde apar dificultățile și fricțiunile. Ce nu merge, dincolo de fapte.”
- Întrebare-cheie: „Ce anume nu funcționează și pe cine afectează cel mai mult?”
- Întrebări de explorare:
  - Ce vă nemulțumește cel mai tare în situația asta?
  - Unde se blochează lucrurile sau se pierde timp?
  - Cine simte problema cel mai acut și cum?
  - E o singură problemă, sau mai multe amestecate?
- Note (textarea)

### Pasul I · Implicații  *(scală urgență 0–10)*
- Lead: „Ce se întâmplă dacă nimic nu se schimbă. Efectele în lanț și costul
  real, asupra oamenilor și a rezultatelor.”
- Întrebare-cheie: „Dacă lăsăm lucrurile așa, unde ajungem peste șase luni?”
- Scală: „Cât de urgent simțim că e (0–10)”, legendă: deloc urgent / oarecum /
  foarte urgent
- Întrebări de explorare:
  - Ce ne costă deja problema asta, în timp, energie, încredere?
  - Pe ce altceva se răsfrânge, dincolo de echipă?
  - Ce se agravează dacă nu facem nimic?
  - Cine mai e afectat, în afară de noi?
- Note (textarea)

### Pasul N · Nevoie și câștig  *(scală pregătire 0–10)*
- Lead: „Ce devine posibil dacă rezolvăm. Valoarea soluției, văzută de echipă,
  nu impusă din afară.”
- Întrebare-cheie: „Cum ar arăta lucrurile dacă am rezolva asta, și ce ne-ar
  aduce?”
- Scală: „Cât de pregătiți suntem să acționăm (0–10)”, legendă: deloc / oarecum
  / pe deplin
- Întrebări de explorare:
  - Ce s-ar schimba în bine, concret, dacă am depăși problema?
  - Ce ar însemna asta pentru fiecare dintre voi?
  - De ce merită efortul tocmai acum?
  - Care ar fi primul semn că ne mișcăm în direcția bună?
- Note (textarea)

### Planul de acțiune
- Lead: „Acum, că nevoia e clară, alegeți un singur pas concret pentru perioada
  următoare.”
- Întrebare-cheie: „Care e primul pas mic pe care îl putem face în următoarele
  două săptămâni, și cine îl ține?”
- Textarea

### Next-step CTA
- Eyebrow: „Pasul următor”
- Titlu: „Ați văzut de ce contează. *Hai să transformăm asta în mișcare.*”
- Body: „SPIN a scos la lumină nevoia comună. GRPI vă arată unde e blocajul
  structural, iar Roata echipei cum evoluează echipa în timp. Sau, dacă vrei,
  putem privi împreună ce ați descoperit.”
- Butoane: ghost „Vezi modelul GRPI →” (`modelul-grpi.html`), ghost „Vezi Roata
  echipei →” (`roata-echipei.html`), primary „Programează o conversație (30 min,
  gratuit) →” (Calendly)

## Model de date (state)

```js
state = {
  clientName: '',
  sessionDate: '',
  challenge: '',                 // câmpul nou de provocare
  scores: { implications: null, needpayoff: null },  // doar doi pași au scală
  notes: { situation:'', problem:'', implications:'', needpayoff:'' },
  focus: '',                     // planul de acțiune
}
```

`DIMS` are 4 intrări (situation/problem/implications/needpayoff). Doar
`implications` și `needpayoff` randează scală; `renderScale` se apelează doar
pentru ei. PDF-ul afișează scorul doar unde există.

## Fișiere atinse

1. **Nou:** `modelul-spin.html` (RO)
2. **Modificat:** `index.html` — un card nou în secțiunea de echipă (după cardul
   GRPI, linia ~364), `data-audience="adult echipa"`. Contoarele filtrului se
   calculează automat, nu trebuie atinse.

### Copy card hub
- card-label: „Echipă · conversație”
- h2: „Modelul *SPIN*”
- p: „Patru tipuri de întrebări, după Neil Rackham: Situație, Problemă,
  Implicații, Nevoie și câștig. Iei o singură provocare a echipei și o explorezi
  până când nevoia de a o rezolva devine limpede pentru toți, înainte de
  soluții.”

## Detalii vizuale

- Favicon: cerc paper + patru litere S/P/I/N în cadrane (variantă a
  faviconului GRPI)
- OG image: `og-cover.png` (fallback comun, fără imagine dedicată deocamdată)
- Subtitlu antet: „Instrument de coaching · după Neil Rackham”
- Meta title: „Modelul SPIN — Instrument de coaching”
- Canonical + hreflang către `en/spin-model.html` (pagina EN apare la
  follow-up; linkul se adaugă acum pentru consistență)

## În afara scopului (follow-up)

- Versiunea engleză `en/spin-model.html` + cardul în hub-ul EN
- Imagine OG dedicată SPIN
- Variantă pentru adolescenți (SPIN nu se pretează)

## Criterii de acceptare

- Pagina se deschide local, parcurge S→P→I→N, scalele de la I și N
  funcționează, notele se salvează în localStorage, sesiunile se reîncarcă.
- Chip-urile cu exemple umplu câmpul de provocare la click, iar textul rămâne
  editabil.
- Export PDF reușit, cu provocarea, scorurile (unde există) și planul de acțiune.
- Cardul apare în hub la filtrul „Echipă” și contorul crește corect.
- Copy curat în română, sentence case, cu diacritice, fără romgleză.
