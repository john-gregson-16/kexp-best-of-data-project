# Design decisions

Mirrors `D:\_Audio_KEXP\GOVERNANCE_RULES.md`'s habit for the pipeline project:
every subjective call with real output impact gets written down with the
reasoning and the numbers behind it, not just the conclusion. Scoped to this
blog/visualization project specifically — pipeline-side decisions stay in
`D:\_Audio_KEXP`, not duplicated here. User's explicit direction (2026-09-11):
keep doing this as new decisions get made, not just for the first chart.

## Standing rules (apply to every visual, not just the first one)

**✅ Visuals get a dark surface; the blog's prose stays on a light surface.**
Decided 2026-09-11. Every embedded chart renders on `#1a1a19` (the dataviz
skill's validated dark chart surface) regardless of the page around it being
light — a deliberate, consistent split between "reading" (light) and "looking
at an instrument" (dark), not a per-chart aesthetic call. Consistent with
patterns seen in data journalism (Bloomberg, FT graphics), and especially
suited to radial/spoke layouts, which read naturally as a star chart or
orbital diagram on black.

**Consequence for every future ramp:** a color ramp validated for the light
surface does **not** carry over to dark automatically. Each ramp needs its
own dark-surface version, independently run through
`validate_palette.js --mode dark --ordinal` (or the categorical six-check
equivalent) — never an auto-flip. For an ordinal/sequential ramp specifically,
note the check flips which end matters: on a light surface the *lightest*
step needs the 2:1 contrast floor (it's nearest the surface); on dark, it's
the *darkest* step that needs it.

## First visual: "25 years of KEXP Annual Best-Of lists" radial chart

### Concept

A circle with one spoke per year of KEXP's Annual "Best Albums of [Year]"
lists, dots ordered rank-1-at-hub outward along each spoke. Dot color encodes
how many distinct albums that dot's artist has had across *all* 25 years
combined (a career-representation/"canon frequency" metric, not the same
album repeating). Hover reveals artist/album/rank. Reference aesthetic:
public.tableau.com/app/profile/jasna/viz/CalendarCircleChart_16906234505150/CalendarCircleChart
(visual layout only — the color logic and metric are original to this project).

### Year range — corrected from the original assumption (2026-09-10/11)

**25 years = 2001-2025, not 2000-2025.** There is no "KEXP Best Albums of
2000" list in the data at all. Verified directly against
`output/step17/dim_lists.xlsx`/`dim_list_entries.xlsx` in the pipeline
project. First spoke (2001) sits at 12:00, last (2025) at ~11:00 — the
deliberate gap (not a closed circle) avoids implying the timeline is cyclical.

### List sizes — real counts, not assumed

22 of 25 years have exactly 91 entries. Three real outliers: **2012 has 120**,
**2024 and 2025 both have 100**. User confirmed comfortable with these
producing visibly longer spokes rather than normalizing all spokes to equal
length.

### Color encoding — why an ordinal ramp, not categorical hues

"Albums per artist across 25 years" is an ordered quantity (1 < 2 < 3-9 <
10+), not an identity — so a single-hue light-to-dark ramp was used instead
of arbitrary categorical colors (the original red/blue/green sketch). This
also makes the encoding inherently colorblind-safe: order is carried by
lightness, which color-vision deficiency does not impair (CVD affects hue
discrimination, not light/dark within one hue) — confirmed with the user's
own explicit ask about colorblind-safety.

**✅ Locked: teal, 4 tiers, wide range, size-stepped.** Chosen over an
initial blue ramp and a purple alternative — teal was the user's preferred
hue alongside purple; teal was ultimately picked for the shipped version.
Generated via proper OKLCH color math (not eyeballed hex), validated with
the dataviz skill's `validate_palette.js --ordinal` (checks: monotone
lightness, adjacent step gap >= 0.06 OKLCH L, light-end contrast >= 2:1
against a `#fcfcfb` surface, single hue) — passed all four checks.

**Shipped values (dark surface — see standing rule above)**, re-validated
against `#1a1a19` with `validate_palette.js --mode dark --ordinal` (all 4
checks pass; the darkest step is the one that needed the 2:1 floor here,
not the lightest, since it's the step nearest the dark surface):

| Tier | Meaning | Hex (dark surface) | Hex (light surface, reference only) | Dot diameter (actual chart) |
|---|---|---|---|---|
| 1 | 1 album in 25 years | `#74eeee` | `#45c5c5` | 4.4px |
| 2 | 2 albums in 25 years | `#39bcbc` | `#009090` | 5.6px |
| 3 | 3-9 albums in 25 years | `#008c8c` | `#005e5f` | 7.2px |
| 4 | 10+ albums in 25 years | `#005e60` | `#002f32` | 9.6px |

The light-surface column is kept for reference only (e.g. if a table/list
view of the same data ever needs to sit on a light page) — it is not used
on the chart itself, since the chart always renders dark per the standing
rule. **Dot diameters shrank from the original 11-24px mockup once real data
was in place** — 2,322 dots across 25 spokes (up to 120 per spoke) need to
be visibly smaller than the illustrative widget mockup to avoid overlapping;
the chart's own legend swatches are shown at 2.5x the actual dot size
(11/14/18/24px) specifically for legend legibility, not because the chart
dots themselves are that large.

**Real constraint found while widening the range:** teal has much less room
to get *lighter* than purple does before failing the 2:1 contrast floor —
not arbitrary, teal/cyan hues read as brighter than purple at the same
nominal OKLCH lightness (green contributes more to perceived luminance than
blue does), so teal hits the "still visible on a white page" ceiling sooner.
The dark end had no comparable floor — pushed down to L≈0.25 for real
visual range without losing the hue.

**Size stepping is additional, not a replacement for color** — confirmed
with the user directly against a live rendered comparison (not just
described) that size made the alternating-tier pattern easier to read at a
glance than color alone, and that the specific size range (11-24px) reads
as tasteful once actually colored, not just abstractly reasonable.

### Tier cutoffs — 1 / 2 / 3-9 / 10+, chosen on a real cliff in the data, not roundness

Real distribution (1,094 distinct artists across all 25 Annual lists,
queried from `dim_list_entries.xlsx` joined through `dim_artist_release.xlsx`
for canonical artist IDs — corrected 2026-09-11 from an initial 1,095/585/218
figure that grouped by (artist_id, artist_name) instead of artist_id alone;
one real artist, Rosalía, has two albums recorded under two different name
spellings and was silently double-counted as two separate "1 album" artists
until the export script fixed it by grouping on the canonical ID only):

| Albums (25-yr total) | Artists | % of all artists |
|---|---|---|
| 1 | 583 | 53.3% |
| 2 | 219 | 20.0% |
| 3-9 | 289 | 26.4% |
| 10+ | 3 | 0.3% |

**User's own real finding, worth carrying into the write-up:** 53.3% of
every artist who has ever made an Annual list has exactly *one* album on it,
ever — "one and done" is the majority case, not an edge case.

**Quartiles don't work here, confirmed with the real numbers, not assumed:**
the "1 album" cohort alone (583) already exceeds two quartiles' worth of the
1,094-artist population before any cutoff is drawn — no split point can
produce four balanced groups given this shape.

**3-9 / 10+ chosen over 3-8/9+ and 3-7/8+ specifically because it's the
sharpest real cliff in the tail, not the most obvious round number:**

| Candidate cut | Middle tier | High tier | Drop into high tier |
|---|---|---|---|
| 3-7 / 8+ | 271 | 21 | 20% (10→8 artists) |
| 3-8 / 9+ | 281 | 11 | 20% (9→8 artists) |
| **3-9 / 10+** | **289** | **3** | **87.5% (8 artists at "9" → 1 artist at "10")** |

Every other candidate boundary sits on a gentle, steady slope (20-35% drop
per step); 9→10 is uniquely where the population falls off a cliff. The
resulting top tier is also a real, nameable narrative hook: only 3 artists
have ever cracked 10+ albums on an Annual list in 25 years — **King Gizzard
& the Lizard Wizard (13), Wilco (11), The Decemberists (10)**.

### Not yet decided

- Whether the "3-9" and "10+" tier boundary should be re-examined once the
  actual chart is built and visually reviewed (per the dataviz skill's own
  step 7: render it and look at it before calling any of this final).

### First build (2026-09-11) — verified against real data, one real Framework gotcha found

Built at `src/annual-circle.md`, data at `src/data/annual_circle_2001_2025.csv`
(exported by the private `D:\_Audio_KEXP\scripts\utilities\export_blog_annual_circle_data.py`
— that script is not committed here, only its output, per the data-sharing
model above). Verified working: dark surface, correct per-year spoke length
(2012 visibly longer, matching its real 120-entry list), hover tooltips
(artist/album/rank), and the on-screen tier color counts match the source
data exactly (583/439/1266/34 circles at `#74eeee`/`#39bcbc`/`#008c8c`/`#005e60`).

**Reusable technical gotcha for every future chart:** Framework's `resize()`
helper skips calling its render callback entirely if the measured container
width is `0` — and a `display:flex; justify-content:center` wrapper around
an *empty* child can measure that child at 0 width before anything is
rendered into it, so the chart silently never appears (no error, nothing in
the console). Fix: don't center the chart's wrapper with flex; use
`max-width` + `margin: 0 auto` on a block-level container instead, which
always gives the child real width to measure against.

**Second gotcha:** `rows` (a `FileAttachment(...).csv()` promise) resolves
correctly when read inside a fenced &#96;&#96;&#96;js cell, but did **not**
reliably resolve when referenced from an inline `${...}` markdown
interpolation in this Framework version — confirmed directly (a fenced cell
printing `rows.length` worked; the identical inline `${rows.length}` stayed
blank forever). Any FileAttachment-derived value that feeds a chart should
be consumed inside a fenced cell, not built directly in an inline
expression.

### Second pass (2026-09-11): wedge/grid layout, fixing dot crowding

**Problem, diagnosed with real numbers, not just "it looks cramped":** the
first build placed every rank on a single radial line per year. At that
chart's size, each rank step was only ~2.55px of radius, but the dots
themselves are 4.4–9.6px in diameter — meaning even the smallest tier
overlapped its neighbors, and the 34 tier-4 dots (9.6px) overlapped by
roughly 3.8x. No amount of re-coloring fixes a spacing budget that's already
short by that much.

**Reference re-examined:** the user's original Tableau reference
(`public.tableau.com/.../CalendarCircleChart`) and a second one supplied
this round (`.../WOWSubmissionTracker`) both turned out to use two spatial
dimensions inside each category's wedge (radius for week-of-month, angle for
day-of-week) rather than one — that's the actual mechanism that gives their
dots room, not a bigger canvas. Confirmed by loading the WOW tracker directly
and inspecting its layout rather than assuming from the thumbnail.

**Fix: fan each year's dots into a small snaking grid inside its own wedge,**
instead of stacking them on one line — same mechanism as the reference
charts. Rank still increases outward on average (hover still reports the
exact rank), but consecutive ranks alternate across `columns = 5` lanes in a
boustrophedon (snake) pattern, so neighbors are separated in two directions
instead of one.

**Geometry is fixed pixels, not proportional to container width:**
`colPitchPx = 8` (lateral spacing between lanes) and `bandHeight = 10px`
(radial spacing between rows) are constant regardless of chart size, and are
applied as a Cartesian offset perpendicular to each spoke's direction vector
— not a polar-angle offset — specifically so the pixel spacing stays
constant near the hub, where a fixed *angle* would otherwise correspond to a
vanishingly small (or, near center, a wildly oversized) physical gap.

**`innerR = 180` is deliberately large,** not cosmetic: it's the minimum hub
radius at which band-0's wedge width (5 lanes x 8px) stays within one year's
angular slot (330° / 25 years) without crossing into a neighboring year's
wedge. A smaller hub radius was tried first and visibly bled into adjacent
spokes near the center.

**Consequence:** the chart's intrinsic size grew from 760px to ~960px
(`(innerR + bands*bandHeight + label padding) * 2`, driven by 2012's 120
entries needing 24 bands at 5 columns). Rather than rescale this geometry to
fit an arbitrary container width, the chart now renders at one fixed
canonical size and lets CSS (`max-width:100%; height:auto`) shrink the whole
image proportionally on narrow screens — preserving relative dot spacing at
every viewport size, rather than the first version's approach of
recalculating spacing per-container-width (which was part of why the spacing
was too tight to begin with: it targeted a fairly narrow default column
width). This also let the `resize()` helper be dropped entirely for this
chart — the layout no longer depends on measured container width, so
there's no 0-width-skip risk to guard against.

**Verified after the rework:** exact same tier-color counts as before
(583 / 439 / 1266 / 34 at `#74eeee` / `#39bcbc` / `#008c8c` / `#005e60`) —
confirms this was purely a layout change, not a data change — plus working
hover tooltips at the new dot positions, and no visible wedge-to-wedge
collision at any radius from hub to outer edge.

### Third pass (2026-09-11): legend on-chart, direction arrow, year filter

**Legend moved inside the dark card.** It previously sat in a plain HTML
`<div>` below the card, on the light page background, using the page's
theme-aware muted-text color — a real mismatch, since its swatches are the
dark-surface hex values but it wasn't rendered against the dark surface.
Now it's nested inside the same `#1a1a19` card as the chart, with its text
color hardcoded to `#c9c8c3` (fixed, not theme-aware, matching the rest of
the chart's on-dark text) so it reads correctly regardless of the site's own
light/dark theme.

**Hub direction arrow, no text.** A plain curved arrow (SVG arc + a
hand-built arrowhead triangle, not a marker or icon font) sweeps 300° inside
the hub at `arcR = 95` — comfortably clear of `innerR = 180` where the dots
start — showing which way the wheel reads. Deliberately no text: the user's
ask was specifically for a wordless visual cue, not a caption.

**Year filter: hand-built checkboxes, not the `Inputs` package.** The
original plan was `Inputs.checkbox(...)` (Observable's standard form-input
library), but this dev environment's outbound network can't reach
`cdn.jsdelivr.net` — the exact same class of failure as the earlier `htl`
CDN-timeout gotcha — and Framework fetches npm-hosted packages like `Inputs`
from that CDN on first use, so the page failed outright (`fetch failed`)
until the dependency was removed. Rebuilt with a plain `<input
type="checkbox">` per year and Framework's own `view()` / `Generators.input`
(core stdlib, ships with Framework itself, no external fetch) — a DOM
element just needs a `.value` getter and to let native checkbox `input`
events bubble up to it, which `view()` picks up the same way it would for
an `Inputs` element. **Worth re-checking once this repo runs outside this
session's constrained network** — `Inputs.checkbox` may well be preferable
there for its built-in styling, but the hand-built version works everywhere
and has no external dependency, so there's no urgency to switch.

**Behavior: dim, don't remove.** Unchecking a year fades its dots and label
to 12% opacity (`dimOpacity`) rather than deleting them from the layout —
chosen over hiding/removing spokes specifically because removing a spoke
would change the angular scale (`angleForYear`'s domain is the full 25-year
set) and reflow every remaining spoke's position on every checkbox click,
which is more disorienting than useful for a "highlight what I care about"
interaction. Verified: unchecking 2012/2013/2014 dims exactly those three
wedges and their labels while the rest stay at full opacity and the on-screen
tier-color counts are unaffected (583/439/1266/34, confirming this is a
pure display toggle, not a data filter).

**Not yet decided:** whether checkboxes are the final control, or whether a
range slider (mentioned as a possible alternative, "may change my mind")
better fits 25 discrete years — both are easy to build with this same
`view()`-based approach if the checkboxes prove too dense in practice.

### Fourth pass (2026-09-11 → 2026-09-12): multi-hue tier ramp, locked

**Problem, diagnosed with real OKLCH numbers, not just a visual impression:**
on the original single-hue teal ramp, adjacent-tier lightness gaps were
actually almost even (~0.15 OKLCH L each) — so tiers 2/3 reading as harder
to tell apart than 1/2 or 3/4 wasn't a measurement problem. It's a known
limitation of single-hue sequential ramps: the eye discriminates *extreme*
lightness far better than *mid-tone* lightness, especially at small mark
sizes (4.4-9.6px dots) against a dark background.

**First candidate (superseded): teal -> sky blue -> vivid blue -> purple.**
Evenly-spaced hue steps (35 deg each), monotonic lightness. Fixed the
original 2/3 confusion, but the user's next-morning re-look flagged tiers
1/2 (teal vs. sky blue) as the new hardest pair -- evenly spacing the hue
steps doesn't guarantee evenly spaced *perceived* distinctiveness, since
that depends on how much chroma each hue can actually hold at that specific
lightness (see next finding).

**Root cause of that leftover 1/2 confusion, found by mapping achievable
chroma across the hue wheel at each tier's exact lightness:** which hues
can be vivid depends heavily on how light or dark you're asking them to be.
At tier 1's near-white lightness (L=0.88), yellow/green (H=90-160) hold far
more chroma (up to ~0.27) than blue/purple (H=240-300, down around ~0.06) --
practically desaturated pastels by comparison. At tier 4's dark lightness
(L=0.48), it flips: magenta/violet (H=260-320) hold the most chroma
(~0.20-0.29) while yellow/green drops to ~0.10-0.15 and reads as muddy
olive/brown rather than a clean color. So a hue path that sweeps evenly
through the *middle* of the wheel (blue, in the first candidate) sits in
mediocre-chroma territory for every tier -- never hitting each tier's own
peak vividness the way jumping straight to that tier's *best* hue would.

**Locked ramp: yellow -> magenta -> violet -> blue-violet**, chosen by
picking each tier's hue from where it's actually most vivid at that tier's
lightness, not by evenly dividing the hue wheel:

| Tier | Meaning | Hue | Hex (dark surface) | Dot diameter |
|---|---|---|---|---|
| 1 | 1 album | yellow (H=95) | `#f9d53f` | 4.4px |
| 2 | 2 albums | magenta (H=322) | `#e67bf7` | 5.6px |
| 3 | 3-9 albums | violet (H=300) | `#9c57f3` | 7.2px |
| 4 | 10+ albums | blue-violet (H=280) | `#5127e9` | 9.6px |

Hue gaps: **133 deg between tiers 1 and 2** (was 35 deg in the first
candidate -- this is the fix for the user's specific complaint), then 22 deg
and 20 deg for 2-3 and 3-4 (those pairs were never flagged as a problem, and
tier 4's rarity -- 34 of 2,322 dots -- already makes it easy to spot without
hue doing extra work). Lightness still decreases in the same even ~0.133
OKLCH-L steps as every prior ramp, preserving the colorblind-safety
guarantee (order recoverable via light/dark alone regardless of hue
confusion) -- widening hue gaps never came at the cost of that property.
Tier 4 vs. the `#1a1a19` dark surface: 2.34:1 contrast (essentially
unchanged from every earlier ramp's ~2.3:1).

**Verified and committed 2026-09-12:** identical on-screen tier-color counts
to every prior version (583/439/1266/34), confirming each recolor pass was
purely visual, never a data change. **User's verdict: locked** -- "bright
pops of color against the black background" is the aesthetic direction, a
real shift from the original single-hue-teal "moody/cohesive" starting
point, and one the user explicitly wants considered as a starting palette
family for future visuals on this blog, not treated as one-off to this
chart. Next visual should start from this yellow/magenta/violet/blue-violet
family (or the same lightness-first, hue-from-peak-chroma *method* on a new
hue set) rather than reintroducing single-hue teal by default.

## Below-the-fold key findings (2026-09-12)

First two "key findings" charts, appended below the wheel on the same page
rather than a separate page -- per the user's framing, these exist to
surface conclusions the wheel itself doesn't state outright, not just
re-plot the same dots. Both charts and their surrounding prose (kept as
shipped, not placeholder copy) were built following the `dataviz` skill's
form-choice table: magnitude-across-categories -> bar chart; part-to-whole
trend over time -> stacked bar. Both reuse the locked
yellow/magenta/violet/blue-violet tier ramp exactly, so a reader carries the
same color->tier mapping from the wheel into these charts without
relearning it. Both charts compute their numbers live from `rows` (the same
loaded CSV), nothing hardcoded or pre-aggregated -- so they can't drift out
of sync with the wheel above them.

**Chart 1, "How rare is a repeat appearance?"** -- a plain bar chart of
distinct-artist counts per tier (583 / 219 / 289 / 3 of 1,094 total, i.e.
53.3% / 20.0% / 26.4% / 0.3%). Deliberately artist-level counts, not the
row/entry-level counts the wheel's dots use (583/439/1266/34) -- "cohort
size" is a question about how many *artists* fall in each tier, and the
row-level counts would overstate tier 3 by counting the same prolific
artists' many list appearances repeatedly.

**Chart 2, "Does it matter when an artist first shows up?"** -- built to
test the user's own hypothesis (arbitrary 2001 start date should shift
tier composition over the 25 years) against real per-year data rather than
taking it on faith. **Finding contradicts the hypothesized direction, but
confirms the underlying mechanism:** tier-1 share is not a one-way drift --
it's a U-shape, elevated at *both* window edges (2001-2004 and 2024-2025,
each >30%) and lowest mid-window (2008/2017/2019-2020, 12-14%). Named
correctly for the write-up: **left-truncation** at the 2001 edge (a legacy
act's pre-2001 output is invisible to a metric that only counts 2001-2025)
and **right-censoring** at the 2025 edge (a brand-new act hasn't had
calendar time yet to earn a second listing) -- symmetric edge effects, not
a single directional drift. Verified against a proper quote-aware CSV
parse (an ad hoc terminal check during development used a naive
comma-split and mis-parsed one row where a title contained a comma --
the live chart itself, built on Framework's real CSV parser, was correct
throughout; the terminal-only bug never reached the page).

**Mark-spec compliance notes:** stacked segments separated by a 2px
surface-color gap (skill spec), only the topmost segment of each stack
rounded (data-end), bottom/interior segments square: bars grow from a
single baseline. Legend present for both charts (4-series floor from the
skill's series-count ladder) using rect swatches for the stacked chart
(mirrors the bar/area mark shape) vs. the wheel's dot swatches (mirrors its
circular marks). Hover tooltip lists every tier's share at that year in one
readout (interaction.md's "one tooltip, every series"), not gated behind
per-segment hover.

**Open for later:** more below-the-fold findings, restricted to what's
derivable from the raw list structure alone (year, rank, list size, artist,
album) before any outside metadata (genre, geography, play counts) enters
the picture -- candidates discussed: rank-vs-eventual-tier correlation
(do chart-toppers skew veteran?), year-over-year list turnover/freshness
rate (a natural companion to the U-shape chart), and multi-year gap/comeback
patterns for repeat artists. None built yet.
