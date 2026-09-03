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
| Both infographics | `.fig img` | 0 → 12 |

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
