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

## Hero

`assets/texture-water.webp` is built from `xx LowRes xx/shutterstock_1462893491-lowres.jpg`
(1800px, WebP q62). Note that file and `xx FX xx/shutterstock_1462893491.jpg` are the same
image — identical 5184x3456 dimensions, mean per-channel difference 0.31/0.23/0.38 out of
255. The "-lowres" name refers to JPEG compression, not resolution.

The photograph is the hero's **base background layer**, with two gradients tinted over it:
a left-to-right falloff and the teal-to-navy ramp. It was previously composited as a
pseudo-element at `mix-blend-mode: soft-light; opacity:.85`, which buried the texture and
made the hero read as a smooth gradient — measurably darker and flatter than the source
build (mean luma 99 against the original's 113).

Tuned against the source capture: mean luma 107 vs 113, bottom corners within 1-6 per
channel, top corners ~15 light. The `::after` rule is gone; everything is layered
backgrounds on `.hero-bg`, which keeps the parallax zoom on a single element.

## Paper grounds

Every section ground is a tiled paper texture, `fixed` at `30%` of the viewport so the
pattern runs continuously down the whole page rather than restarting per section.

Two tiles, both WebP lossless from `xx FX xx/`:
`texture-paper.webp` (paper.png, mean 239, 91KB -> 73KB) and
`texture-paper-lighter.webp` (paper-lighter.png, mean 249, 91KB -> 49KB).

| sections | tile | blend | colour | measured ground | grain |
|---|---|---|---|---|---|
| `#overview` `#properties` `#hl-submarket` `#hl-dfw` `#cta` | paper | screen | `rgba(65,60,57,.05)` | 239 | 3.6 |
| triptych `#hl-location` | paper | multiply | inherited | 208 | 3.2 |
| `.sec-teal` (`#hl-growth` + basis/offering) | lighter | multiply | inherited | 98,134,153 | 2.1 |
| `#hl-brand` | paper | hard-light | `rgba(65,60,57,1)` | 232 | 5.4 |
| `footer#contact` | lighter | color-burn | inherited | 67,65,66 | 4.1 |

`#highlights` (the Harbor divider) is deliberately untouched — it is a full-bleed photo.

### Two things that make this work

**Selectors are addressed by id where a class would cross groups.** `.sec-gray` covers both
`#hl-location` (multiply) and `#hl-brand` (hard-light), and `.cta-close` would not distinguish
`#cta` from the grey sections. Using the classes would have collapsed those distinctions.

**The ground rules use `background-color`, not the `background` shorthand.** `.sec-gray`,
`.sec-teal`, `.sec-light`, `.cta-close` and `footer` previously set `background:` — a shorthand
that resets `background-image` to `none`. Since those rules sit later in the stylesheet, they
would have silently wiped the paper layer off every class-targeted section.

Note `#hl-brand`: hard-light keys off the *top* layer, and the paper tile is light, so it
resolves to screen — a near-black `background-color` produces a **light** 232 panel, not a dark
one. That is the specified behaviour, just an unintuitive one.

`background-attachment: fixed` is ignored on iOS Safari and repaints badly, so
`@media (hover:none)` drops every one of these to `scroll`.

The source build had no texture anywhere — its grounds are flat, stddev 0.00.

## Copy alignment

Intro and all Investment Highlight copy are justified.

- The intro uses `text-align-last: left`, so each paragraph's last line — and the one-line
  third paragraph — sits flush left rather than centred.
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

## Brand affiliation section

`#hl-brand` follows the supplied reference: copy left, property lockups right, then a
full-width bordered box holding the 29 Marriott brands in **four tiers of 6 / 11 / 8 / 4**
— Luxury, Premium, Select, Longer Stays. That is Marriott's own brand architecture, and the
counts match the reference exactly against the 29 logos already in `assets/brands/`.

Two implementation notes:

- **The interrupted top border is a real `<fieldset>` / `<legend>`.** Browsers break the
  fieldset border natively for a legend, so the Bonvoy mark sits in a genuine gap. The
  alternative — a mask in the section's background colour — cannot work here, because the
  ground is a blended paper texture rather than a flat fill.
- **The box is wider than the text column** (`min(calc(100% - 48px), 1560px)`, so 1392px at
  1440 against the 1100px `.wrap`), matching the reference and giving the 11-logo tier room
  to stay on one line.

Tier logos are `clamp(34px, 4.3vw, 68px)` and the property lockups `clamp(160px, 18vw, 320px)`.
The 11-logo premium tier is the binding constraint on logo size, so it was measured rather
than guessed: at 1440 that row uses 1196px of 1306px available, leaving ~8% headroom.
Single-line tiers verified at 1024 / 1200 / 1440 / 1920 / 2479 (44 → 68px); they wrap to
2/3/2/1 lines at 375, still centred. Going much beyond 4.3vw makes the premium tier wrap,
which breaks the tier reading.

The copy sits in a `#64899D` panel with white justified text (`text-align-last: left`), and
the head shares the **same wide container** as the box rather than the narrower `.wrap` — so
the panel's left edge lands exactly on the box's left edge (both x=24 at 1440, verified
equal at 375, 1024 and 2479 too). The head grid is `2.95fr 1fr`, putting the panel at ~72%
of the container width, with the lockups right-aligned flush to the container's right edge. Property lockups are stacked, SpringHill above TownePlace, and use
`logo-shs-navy.svg` / `logo-tps-navy.svg` (fill `#314356`) because they sit on the light 232
ground, not inside the panel.

## Parallax travel ceiling

Both the scroll roller (82% → 18%, i.e. 64% of available slack) and the cursor parallax (12%)
are percentages of each image's **crop slack** — the overflow `object-fit: cover` produces.
Measured travel at 1440:

| image | slack | travel |
|---|---|---|
| `aerial-residential` | 272px | 174px |
| `band-lobby` | 230px | 147px |
| `marina` | 68px | 44px |
| `shs-pool` | 21px | 13px |
| `basis-room` | 11px | 7px |

Images whose box aspect nearly matches their source have almost no slack, so no percentage
increase can make them move much — `basis-room` has 11px to work with. Giving those more
travel means rendering the image larger than its box, which requires a clipping ancestor
(the wrappers that were tried and reverted). Adjusting a box's `min-height` to change its
aspect is the other lever.

## Footer and section padding

`#hl-location` carried an inline `padding-top:0`, a holdover from when the triptych sat
flush above it. Once the triptych moved into `#hl-submarket` that left the section with
0px top against 128px bottom, so its panel ran hard into the top edge. The inline override
is gone and both sides are now `--pad`.

Footer block padding is `clamp(36px,3.9vw,64px)` (was `56px/7vw/104px`) and the grid gap is
`clamp(24px,2.8vw,44px)` (was `36px/5vw/72px`), which cuts the space around the HWE logo
from 317px to 272px a side and the footer height from 703px to 613px at 1440.

The footer grid is `1fr 2.5fr` with the logo flush left, and the representatives sit in a
two-column `.foot-reps` grid with the section rules spanning its full width — matching the
supplied layout. Splitting the reps into two columns also cut the footer from 613px to 459px
at 1440, which shrank the space around the logo far more than the padding change did.

`assets/hwe-white.svg` was **351KB of embedded Illustrator metadata** wrapping 18 paths.
Stripped to 11.4KB, verified pixel-identical. It loads on every page view, twice.

## Scroll and hover effects

Three independent systems, all disabled under `prefers-reduced-motion`:

**Scale-in on scroll.** 15 content blocks (`.prop`, `.tri-under`, the teal/white copy
panels, `.dev-pair`, `.i30`, `.brand-grid`, `.alt`, the aerial map, the DFW figure) start at
`scale(.94)` and settle to 1 over 1.15s as they cross into view, one-shot per element. The
scale only ever runs **up to** 1, never past it, so nothing overflows its box — which is why
this needs no clipping wrappers. The hidden state is gated behind a `.js-rise` class that the
script adds, so the page renders fully if the script never runs.

**Hover zoom on images.** 18 images carry `data-hpx`; on hover they zoom to
`object-view-box: inset(7%)` over 500ms. `object-view-box` crops *into the source* inside the
element box, so the image never grows past its frame — which is why this needs no clipping
wrapper the way `transform: scale()` would. Verified by diffing a forced-hover render against
the resting one: the changed region is exactly 546×240, the image's own box, and the adjacent
column is pixel-identical.

It is pure CSS (no script), gated behind `@supports (object-view-box: inset(0%))` and
`@media (hover:hover)` — browsers without the property simply get no hover zoom, and touch
devices are skipped. Excluded by request: the annotated aerial map and the Dallas skyline
infographic, both of which carry baked-in text.

**Scroll roller and zoom** — see below.

The hover offset composes with the scroll roller rather than fighting it: the roller drives
`--py`, hover drives `--hy`, and `object-position` resolves
`calc(var(--px-x) + var(--hx)) calc(var(--py) + var(--hy))`. The property exteriors express
their bottom anchor as `--py: 100%` for the same reason, so they stay anchored at rest and
still respond to the cursor.

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

The triptych now sits inside `#hl-submarket` beneath the copy and the concentrations graphic; its former standalone `.sec-gray` section is gone.

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
| `data-roll` | Roller. Defaults 82% → 18% |
| `data-roll-from` / `data-roll-to` | Roller range in percent |

Easing is smoothstep (`t²(3−2t)`) — slow in, slow out — for both effects.

`object-position` repaints rather than composites, so cost was measured rather than assumed:
during continuous scrolling, median frame 16.7 ms, p99 17.6 ms, max 18.9 ms, zero frames over
20 ms. Culling plus quantising the value to 0.1% keeps it cheap.

`prefers-reduced-motion: reduce` disables both effects in CSS and JS. Layout is identical
either way — neither effect can change an element's box.

## Open items
- Confidentiality Agreement links are live. Both CTA pairs (4 links total) point at the
  RightSignature signer template and at `assets/docs/TX-Dallas-Rockwall-SHS-TPS-CA.docx`.
  The download uses a hyphenated filename on disk — the original contains an `&`, which
  would need escaping in a URL — and a `download` attribute so the saved file keeps the
  original name. Note the agreement is a public, unauthenticated download, same as the
  other HWE microsites.
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
