# Roata echipei, design spec

Date: 2026-06-17

## Purpose

A new coaching tool for the instrumente-coaching collection: a "Roata echipei" (team
wheel) that lets one person (a team leader, or the coach together with them) rate the
health of a team across 8 dimensions on a 0 to 10 scale, and see the result as a visual
wheel. It mirrors the existing "Roata vieții" exactly in mechanics and design.

## Usage model

One person scores the team. Single device, single wheel. Same interaction as Roata vieții
(no per-member input, no aggregation, no backend).

## Approach

Clone `roata-vietii.html` into `roata-echipei.html` and change only the content. The wheel
engine, sliders, per-dimension notes, saved sessions (snapshots) with dashed comparison
overlay, PDF export, and localStorage persistence are reused unchanged. Then add one card
to `index.html`.

This keeps the new tool behaviourally and visually identical to the other wheels, with no
new mechanics to maintain.

## The 8 dimensions

Single user scores each 0 to 10. Names and helper descriptions (clean Romanian, sentence
case):

1. Încredere, siguranța de a fi vulnerabil: să recunoști o greșeală, să ceri ajutor, fără teamă.
2. Comunicare, cât de deschis, clar și la timp circulă informația între oameni.
3. Obiective clare, toți știu încotro merge echipa și ce înseamnă succesul.
4. Roluri și responsabilități, fiecare știe ce are de făcut și cine de ce răspunde.
5. Feedback, se oferă și se primește feedback onest și constant, fără frică.
6. Colaborare, oamenii se sprijină și lucrează spre același rezultat, fără bariere între roluri sau departamente.
7. Moral și energie, starea de spirit, motivația și entuziasmul echipei.
8. Rezultate, echipa livrează ce și-a propus, la calitatea așteptată.

The 8 sector colors are reused from Roata vieții, in the same order.

## Content decisions

- Subtitle: "Instrument de coaching" (no single author attribution). The "Despre exercițiu"
  note mentions the tool draws on team-health models such as Lencioni and GRPI.
- The "Nume" session field becomes "Nume echipă".
- Next-step CTA links only to Calendly for a team coaching conversation (the "Valori" link
  from Roata vieții is dropped, since it does not fit a team context).
- Audience: adulți only (`data-audience="adult"`). No teen variant.
- OG image points to existing `og-cover.png` for now, so shared links do not break. A
  dedicated `og-roata-echipei.png` is a later follow-up.

## Copy to adapt

- `<title>`, meta description, OG/Twitter title and description, canonical URL.
- `STORAGE_KEY` becomes `roata-echipei-v1`.
- "Despre exercițiu" body: rewrite for a team context (what the wheel shows, how to work
  with it, what to look for in the result), keeping the same three-part structure.
- PDF report title and filename (`roata-echipei_...`).

## Index card

Add one card to `index.html`, `data-audience="adult"`, label "Adulți · echipă", linking to
`roata-echipei.html`, with a short description of the tool.

## Out of scope

- Multi-member input or aggregation.
- Teen variant.
- Dedicated OG image (later follow-up).
- Any change to existing tools.

## Testing

Manual verification in the browser: wheel renders with 8 sectors, sliders update the wheel
and average, notes open/save, a snapshot saves and shows as a dashed comparison overlay,
reset works, PDF export produces a correct A4 report, data persists across reload, and the
new index card filters correctly under "Adulți".
