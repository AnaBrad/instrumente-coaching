# English version of the coaching hub — design

**Date:** 2026-06-18
**Author:** Ana-Maria Brad (with Claude)
**Status:** Approved for planning

## Goal

Publish a full English version of the coaching tools site at
`anabrad.github.io/instrumente-coaching/en/`: an English hub plus English
translations of all instruments, with a clearly visible language switch
between Romanian and English. The English site targets international clients
and English-speaking teams.

## 1. Structure & routing

- New `en/` subfolder inside the existing repo. Same GitHub Pages deploy, same
  domain. No new repo, no new setup.
- EN hub at `en/index.html`. Each instrument is an English-named file in `en/`
  (see mapping in §3).
- Shared assets reused where possible: Google Fonts (Fraunces + Inter) via CDN,
  favicon (inline SVG data URI), Umami analytics snippet.
- `lang="en"` on every English page.
- `hreflang` alternate tags: each RO page points to its EN counterpart and each
  EN page points back to its RO counterpart (plus `x-default`), so search
  engines serve the right language.
- No legacy redirect files in `en/` (the existing `woop.html` redirect is a
  Romanian-only artifact and is not mirrored).

## 2. Language switch (must be very visible)

- **Hub:** a prominent `RO | EN` pill toggle in the hero area, styled in the
  paper-cognac / sage palette, with the active language clearly marked. This is
  a deliberate, visible control, not a small text link.
- **Each instrument:** a clear `RO | EN` toggle in a page corner that jumps to
  the same tool in the other language.
- **Bidirectional:** existing Romanian pages are updated to add the link to
  their English counterpart; English pages link back to Romanian.

## 3. Instruments → English

Translation is applied to all 20 instruments (the tiny `woop.html` redirect is
not translated). For each tool, both the adult and, where it exists, the
`-teens` variant are translated.

| Romanian file | English file | English title |
|---|---|---|
| modelul-grow.html | en/grow-model.html | GROW Model |
| modelul-grow-adolescenti.html | en/grow-model-teens.html | GROW Model for teenagers |
| modelul-woop.html | en/woop-model.html | WOOP Model |
| modelul-woop-adolescenti.html | en/woop-model-teens.html | WOOP Model for teenagers |
| valori.html | en/personal-values.html | Personal Values |
| valori-adolescenti.html | en/personal-values-teens.html | Personal Values for teenagers |
| roata-vietii.html | en/wheel-of-life.html | Wheel of Life |
| roata-vietii-adolescenti.html | en/wheel-of-life-teens.html | Wheel of Life for teenagers |
| roata-increderii.html | en/wheel-of-confidence.html | Wheel of Confidence |
| roata-increderii-adolescenti.html | en/wheel-of-confidence-teens.html | Wheel of Confidence for teenagers |
| roata-echipei.html | en/team-wheel.html | Team Wheel |
| modelul-grpi.html | en/grpi-model.html | GRPI Model |
| credinte-limitative.html | en/limiting-beliefs.html | Limiting Beliefs |
| credinte-limitative-adolescenti.html | en/limiting-beliefs-teens.html | Limiting Beliefs for teenagers |
| cercul-de-control.html | en/circle-of-control.html | Circle of Control |
| cercul-de-control-adolescenti.html | en/circle-of-control-teens.html | Circle of Control for teenagers |
| roata-emotiilor.html | en/wheel-of-emotions.html | Wheel of Emotions |
| harta-energiei.html | en/energy-map.html | Energy Map |
| harta-energiei-adolescenti.html | en/energy-map-teens.html | Energy Map for teenagers |

Hub filter chips: **All / Adults / Teenagers / Teams** (the `data-audience`
values `adult` / `teen` / `echipa` / `both` stay the same internally; only the
visible labels change). All internal `href`s in the EN hub point to the EN
filenames; the back-to-hub link in each EN instrument points to `index.html`
(i.e. `en/index.html`).

### Translation principle

Natural coaching English, not literal word-for-word. Same warm, conversational
voice as the Romanian: full sentences, no clipped fragments. Established
coaching jargon (e.g. "chemistry session") stays in English. Everything
baked into each page is translated:

- page `<title>`, `<meta name="description">`, Open Graph + Twitter meta
- all visible UI copy: headings, instructions, button/label text, tooltips,
  empty states, confirmation dialogs
- guidance / "About" / interpretation sections (which by convention appear
  *after* the exercise, not before)
- any text rendered into the PDF export
- `og:locale` set to `en_US`, canonical + og:url pointing at the EN URL

## 4. English OG images (~11)

The shared OG preview images currently have Romanian text baked into the PNGs.
No image-generation tooling exists in the repo today, so this is built as part
of the work:

- Build lightweight HTML templates in the brand style (paper-cognac, Fraunces),
  one per unique cover, sized for a 1200×630 viewport.
- Render each to PNG with headless Chrome
  (`--headless --screenshot --window-size=1200,630`), output to `en/og-*.png`.
- English pages reference these via their OG/Twitter meta tags.

**Risk:** headless rendering of webfonts/layout can be fiddly. If it misbehaves,
fall back to a single neutral shared English cover (no tool-specific text) so
sharing still works, and iterate on per-tool images later. This fallback is
acceptable and should not block the rest of the launch.

## 5. Romanian fix (in scope)

The Romanian hub lead is outdated now that team tools exist. Update it (and the
matching meta descriptions where they repeat the phrase) from
"Pentru sesiuni 1:1 sau pentru lucrul cu sine." to:

> Instrumente vizuale pentru sesiuni de coaching 1:1, pentru echipe sau pentru
> lucrul cu sine.

## 6. Delivery & review

- Build the EN hub + all EN instruments + EN OG images, add the language toggle
  to the existing RO pages, fix the RO tagline, then push to GitHub Pages.
- Ana reviews the live `/en/` site end to end and gives feedback in batches.

## Out of scope

- Adding new instruments or changing instrument logic/behavior.
- Restructuring the Romanian pages beyond the toggle + tagline fix.
- Translations into any third language.
- A build system or templating engine (pages stay self-contained HTML, matching
  the current repo convention).
