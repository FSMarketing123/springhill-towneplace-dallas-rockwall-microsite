# SpringHill Suites & TownePlace Suites Dallas Rockwall — Investment Offering Microsite

Hodges Ward Elliott offering microsite for the fee-simple acquisition of the
105-key SpringHill Suites Dallas Rockwall and the 112-key TownePlace Suites Dallas Rockwall.

**Live:** https://springhilltowneplacedallasrockwall.hodgeswardelliott.com/

## Stack
Static single page. No build step, no dependencies. `index.html` carries its own
inline `<style>`; everything else is in `assets/`. Deployed via GitHub Pages from `main`.

## Structure
```
index.html          single page, all sections
CNAME               custom domain
.nojekyll           bypass Jekyll processing
assets/
  brands/           29 Marriott brand SVGs
  *.webp            photography and infographics
  *.svg             HWE, property lockups, Ballard, Chocxo, Bonvoy
```

Section anchors: `#hero #overview #properties #highlights #hl-submarket #hl-location
#hl-growth #hl-brand #hl-dfw #hl-basis #hl-offering #cta #contact`

## Provenance
Rebuilt from `../xx Working Files xx/squarespacebuild-shstps-rockwall.jpg`, a full-page
capture of the original Squarespace build. The Squarespace WXR export
(`Squarespace-Wordpress-Export-09-03-2026.xml`) contained **zero content items** — only
channel metadata, two authors, and a `null - null` placeholder category — so it could not
be used as a content source. See `../xx Working Files xx/AUDIT.md` for the full audit.

All copy is transcribed verbatim from the capture. Nothing was invented. Two grammatical
errors present in the original are preserved deliberately:
- "In 2023, DFW **lead** the entire country in population growth" (should be "led")
- "These large and unique investment opportunities highlight the types of opportunities
  Rockwall is expected to continue its trend of high growth." (garbled in the original)

## Scale

The original Squarespace build was authored at a **2479px viewport** with proportional
sizing — confirmed by matching the button height in the capture (66px) against a real
button. The first rebuild used fixed pixel caps, so on wide displays it stopped growing
and read as undersized: the content column held at 1100px where the original was 1526px
(61.6vw), and section padding capped at 104px where the original used 222px.

Sizing is now fluid, with ratios measured off that capture. At a 2479px viewport every
value lands within 1px of the original:

| Token | Original | Rebuild | CSS |
|---|---|---|---|
| Content column | 1526 | 1527 | `min(calc(100% - 48px), max(1100px, 61.6vw), 1720px)` |
| Text measure | 1267 | 1267 | `min(calc((100% - 48px)*.83), max(913px, 51.1vw), 1428px)` |
| Hero height | 1367 | 1363 | `max(min(60vh, 620px), min(55vw, 100vh))` |
| Hero lockup | 1082 | 1081 | `clamp(300px, 43.6vw, 1100px)` |
| Masthead logo | 182 | 181 | `clamp(118px, 7.3vw, 190px)` |
| Button | 438 × 74 | 439 × 74 | `min-width:clamp(270px, 17.7vw, 440px)` |
| Footer logo | 254 | 253 | `clamp(180px, 10.2vw, 260px)` |
| Body line-height | 30 | 30 | `clamp(16px, calc(14.6px + .096vw), 18px)` / 1.76 |
| Section padding | 222 | 221 | `--pad: clamp(48px, 8.9vw, 226px)` |

Floors keep small screens sane — at 375px the column is 327px, the hero 487px, and the
body stays at 16px. Nothing changes at 1440px, which is where the earlier caps already sat.

Note `.wrap` and `.narrow` share one element, so a bare percentage on `.narrow` would
resolve against the section rather than the column. The measure mirrors the `.wrap`
expression scaled to 83% instead.

## Copy alignment

Intro and all Investment Highlight copy are justified.

- The intro is a centre-composed block, so it uses `text-align-last: center`. Plain justify
  would leave each paragraph's last line — and the one-line third paragraph — flush left,
  which reads as a broken centre.
- `hyphens: none` on every justified block: words are never broken across lines. This is a
  deliberate choice, not a default. The trade-off is visible word gaps in the narrowest
  panel (Recent Vintage, 344px at 1440) — e.g. "Rockwall.   Offering   state-of-the-art".
  Turning hyphenation back on evens that out at the cost of breaking words; widening that
  panel or dropping its type a step would fix it without either.

Justified blocks: `#overview .narrow p`, `#hl-submarket p`, and every `.panel-teal p` /
`.panel-white p`. On phones the measure narrows to ~284px (~33 characters), which is tight
for justification — switch those to `text-align: start` below ~600px if it reads poorly.

## Concentrations graphic

The "Professional Concentrations of Rockwall Residents" panel is live HTML/CSS/SVG, not a
raster. It replaced `infographic-concentrations.webp`, which has been deleted.

Proportions are measured off the source artwork (`Rockwall-Residence-graphx.png`, 4951px
wide) and expressed in `cqw` against a `container-type: inline-size` wrapper, so the whole
panel scales exactly as the image did — rendered aspect 0.8218 against the source's 0.8215.

**Bars are to scale.** The white bar is the national average (1.00x) and the teal bar is
Rockwall's level, so +109% draws at 2.09x the baseline. Measured ratios in the source
artwork are 2.065 / 1.930 / 1.596 / 1.493 against the stated +109 / 94 / 58 / 48%, which
confirms the original was drawn the same way. The track is sized to the longest bar and
each row's width is `(1 + pct/100) / 2.09`.

**Icons** are the supplied `Architecture/Legal/Sales/Management-icon.svg` files. Each was
2.1 MB of embedded Illustrator metadata; stripped to 43 KB total and each
viewBox was tightened to its ink bounds so `contain` sizing renders the artwork at a
predictable size. They are applied as CSS `mask-image` rather than `<img>` so they can
recolour on hover.

**Animation** — bars grow and the percentages count up when the figure crosses 35% of the
viewport, staggered 90ms per row. **Rollover** — the card lifts, the icon, label, bar,
number and arrow all shift to `--navy`, and the arrow rises. Both are disabled under
`prefers-reduced-motion`, which renders the finished state immediately.

Accessibility improved as a side effect: the figures, labels and footnote are now real
text rather than pixels baked into an image.

## Parallax

Scroll-driven, no library. One rAF-batched handler, viewport-culled by IntersectionObserver.
Two independent effects:

**Zoom** (`transform: scale`) — needs a clipping ancestor, so it is limited to the 7 elements
whose container already clips.

| Section | Element | Zoom |
|---|---|---|
| Hero | `.hero-bg` | 0 → 40 |
| Communal table band | `.band img` | 40 → 0 |
| Harbor aerial divider | `.divider img` | 0 → 40 |
| Ballard / Chocxo renderings | `.dev-media img` | 0 → 12 |

The two infographics deliberately carry **no** zoom. They have text baked into the raster,
and scaling them inside a clipping figure cropped the top and bottom edges off — 34px on the
Dallas skyline graphic, 25px on the concentrations graphic. `.fig` no longer clips and the
images are capped at `max-width:100%; max-height:100%`.

**Roller** (`object-position`) — on all 18 `object-fit: cover` images. This slides the crop
window *inside* the image's own box, so the element never changes size and cannot overlap a
neighbour. No wrapper elements, no layout risk.

The roller is axis-aware. A cover crop only has slack on the axis it overflows: width-limited
crops roll vertically, height-limited crops pan horizontally, and a box matching the image
aspect exactly gets skipped rather than repainting for nothing. `gauge()` measures this per
image on load and on resize. At 1440px, 13 roll on Y and 4 on X; at 375px all 18 move.

| Attribute | Meaning |
|---|---|
| `data-px` | Zoom. Defaults 0% → 12% |
| `data-px-from` / `data-px-to` | Zoom range in percent (`40` = `scale(1.4)`) |
| `data-px-mode="top"` | Progress measured from the top of the document. Hero only — it is already on screen at load and must start at zoom 0 |
| `data-roll` | Roller. Defaults 66% → 34% |
| `data-roll-from` / `data-roll-to` | Roller range in percent |

Easing is smoothstep (`t²(3−2t)`) — slow in, slow out — for both effects.

`object-position` repaints rather than composites, so cost was measured rather than assumed:
during continuous scrolling, median frame 16.7 ms, p99 17.6 ms, max 18.9 ms, zero frames over
20 ms. Culling plus quantising the value to 0.1% keeps it cheap.

`prefers-reduced-motion: reduce` disables both effects in CSS and JS. Layout is identical
either way — neither effect can change an element's box.

## Open items
- **Confidentiality Agreement URLs are unset.** Both CTA pairs use `href="#"`, marked
  with `<!-- TODO -->` comments. Four links total (2 in `#overview`, 2 in `#cta`).
- `assets/aerial-map.webp` is 1657px wide — the largest source available. Re-export
  larger from `../SpringHill & Towneplace Dallas Rockwall Microsite.indd` for a crisper
  full-width render.
- The original brand grid showed **Design Hotels twice**; the duplicate was dropped
  (29 unique brands render here, 30 tiles in the original).

## Local preview
Dev servers can't read from `~/Downloads`, so mirror to `/tmp` first:
```
rm -rf /tmp/rockwall-preview && mkdir -p /tmp/rockwall-preview
cp -R site/. /tmp/rockwall-preview/
python3 -m http.server 8943 -d /tmp/rockwall-preview
```
A `rockwall-microsite` entry in `../../.claude/launch.json` does this on port 8943.
