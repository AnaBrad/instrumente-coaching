# Cercul de control pentru adolescenți — design

Data: 2026-06-06

## Scop

Variantă pentru adolescenți a instrumentului existent „Cercul de control" (după
Stephen Covey). Aceeași mecanică (trei cercuri: Control / Influență / Preocupare),
copy și exemple adaptate la viața de adolescent, pe cinci teme: prietenie,
apartenență la grup, social media, școală și bullying.

## Fișiere

- `cercul-de-control-adolescenti.html` — fișier nou, clonă a `cercul-de-control.html`.
- `index.html` — card nou `data-audience="teen"` imediat după cardul Cercul de control pentru adulți.

## Ce rămâne identic față de varianta de adulți

- Mecanica SVG cu trei inele (Control / Influență / Preocupare).
- Persistență localStorage, export PDF, dialogul custom de confirmare, placeholder-ul de dată.
- Paleta sage-green (aceleași variabile CSS). Fără modificări de motor.

## Ce se schimbă (conținut pentru adolescenți)

1. **Ton** — „tu", cald și simplu, fără jargon. Titlu: `Cercul de control pentru adolescenți`. Subtitlu: `Instrument de coaching`.

2. **„Despre exercițiu"** — rescris pentru adolescenți, cele trei cercuri explicate prin lumea lor (prieteni, grup, social media, școală, bullying). Linia despre bullying este explicită: ce îți face altcineva nu e vina ta și stă în Preocupare; ce rămâne în mâna ta este cui spui, pe cine te bazezi, pasul tău următor.

3. **Exemple pre-completate** (neașezate în cerc, acoperă cele cinci teme):
   - „Cine îmi dă like sau cine comentează pe Insta/TikTok"
   - „Că am fost lăsat/ă pe dinafară din grup"
   - „Nota de la următorul test"
   - „Un zvon sau o glumă răutăcioasă despre mine"
   - „Cum reacționez când cineva mă jignește"

4. **Notă de siguranță** — casetă vizibilă lângă partea de sus: „Dacă cineva îți face rău sau te simți în pericol, nu ești singur/ă. Vorbește cu un adult în care ai încredere. Poți suna gratuit și anonim la Telefonul Copilului — 116 111." Reluată și în zona de footer a PDF-ului.

5. **Insight + reflecție** — copy adaptat: energia merge în Control/Influență; lucrurile de tip bullying din Preocupare primesc încadrarea „o numești, nu o duci singur/ă".

6. **Pasul următor** — trimite la `modelul-grow-adolescenti.html` + sesiunea gratuită de chemistry (Calendly).

7. **Meta/OG** — titlu/descriere pentru adolescenți, canonical `...cercul-de-control-adolescenti.html`. OG image: `og-cover.png` (nu există cover dedicat teen pentru Cercul de control).

8. **Cheie storage** — `cercul-de-control-adolescenti-v1` (separată de varianta de adulți).

## Compromisuri cunoscute

- Nu există `og-cercul-de-control.png` nici pentru varianta de adulți, nici cover teen dedicat. OG va folosi `og-cover.png`. De înlocuit ulterior cu un cover dedicat.
