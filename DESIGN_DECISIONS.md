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
the picture -- candidates discussed: year-over-year list turnover/freshness
rate (a natural companion to the U-shape chart), and multi-year gap/comeback
patterns for repeat artists (real stat found while scoping this: median gap
between an artist's appearances is 3 years, only 14.3% of comebacks are
back-to-back, longest single gap is The Hives at 19 years (2004->2023), and
Mogwai spans the entire dataset, 2001->2025, in just 6 appearances -- good
callout material, not yet built into anything).

### Third chart, corrected mid-design: "Is the top of the list reserved for familiar names?" (2026-09-12)

**The original plan was to cross-tabulate rank against the same 25-year
tier used everywhere else on the page** (rank 1-5 was 83.2% tier-3+,
rank 51+ was only 41.7% -- looked like a clean, strong finding). **User
caught a real methodological problem before it shipped:** the 25-year tier
is a retrospective total, attached identically to every one of an artist's
rows regardless of which row's year you're looking at -- so an artist's
*first-ever* appearance already gets full credit for albums they wouldn't
release for another decade. Asking "does rank correlate with being an
already-proven artist" with that metric silently answers a different
question: "does rank correlate with an artist who eventually turned out to
be prolific," which is not the same claim and overstates how much a reader
in, say, 2002 could have actually known.

**Fix: a second, purpose-built metric** -- for each row, count how many
times that artist had appeared on an Annual list *up to and including that
row's year* (built from `list_year` + `artist_id` alone, sorting each
artist's own appearance years and taking the index). Deliberately does
**not** touch album-release chronology/discography order -- the user is
already planning a separate purpose-built analysis for "is this typically
an artist's 1st/2nd/3rd released album," and conflating the two would jam
two different questions into one chart. This one stays scoped to *this
list's own history*, nothing about an artist's broader discography.

**Real numbers changed once corrected, but the underlying gradient
survived:**

| Rank band | 1st appearance | 2nd | 3rd-9th | 10th+ |
|---|---|---|---|---|
| 1-5 | 25.6% | 32.8% | 41.6% | 0.0% |
| 6-10 | 32.0% | 29.6% | 38.4% | 0.0% |
| 11-20 | 40.0% | 22.8% | 37.2% | 0.0% |
| 21-50 | 46.1% | 20.9% | 32.8% | 0.1% |
| 51+ | 54.1% | 20.9% | 24.8% | 0.2% |

First-timer share still climbs steadily top-to-bottom of the list (25.6% ->
54.1%), so "the top skews toward familiar names" still holds -- just far
less absolutely than the original 0%-tier-1-at-rank-1 framing implied.
Sharpest single fact: of the 25 albums that have ever hit #1, **7 (28%)**
were that artist's first-ever Annual-list appearance -- debuting at #1
happens; those artists just then go on to reappear, which is what pulled
their retrospective tier up in the original (wrong-question) version.

**Lesson for every future chart on this page:** any time a chart wants to
ask "what did we know at the time," check whether the metric being reused
is itself retrospective/whole-window before reusing it -- the tier column
answers "how did this turn out," not "what was known then," and those are
easy to conflate silently.

**Follow-up fix (2026-09-12): added an in-chart axis label.** User
correctly flagged that "1-5, 6-10, 11-20..." reads as meaningless without
context -- unlike the year-composition chart (where "2001, 2002..." is
self-evidently a timeline), a rank band has no obvious unit on its own.
Added a small recessive-gray axis title, "Rank on that year's list
(1 = best)", directly under the band tick labels rather than relying on
the surrounding prose to carry that meaning.

**Dev-environment note, not a real code bug:** while making that axis-label
change, every chart on the page (including the wheel, unrelated to the
edit) briefly stopped rendering across several full server restarts and
fresh tabs, with zero console or server errors -- diagnosed as this dev
sandbox's environment flakiness, not a Framework parsing bug or anything
wrong in the file. Confirmed by reverting to byte-identical known-working
code and seeing it still fail, then seeing everything recover after a full
`preview_stop`/`preview_start` cycle with a brand-new tab. No lasting
gotcha to document here -- if a future session sees every chart on a page
go blank with no error, a full server + tab restart before debugging the
code is the first thing to try.

## Fourth key finding: year-over-year turnover rate (2026-09-12)

**Form choice: line, not bar.** Per the `dataviz` skill's form table,
"trend over time" for a *single* series is a line (bars/stacks are for
comparing categories or multiple series at once, which is what the other
three key-finding charts are doing). Single series also means no legend box
is needed -- the heading already says what's plotted, matching the skill's
"a single series needs no legend box" rule.

**Color: reused tier-1 yellow (`#f9d53f`), not a new hue.** The metric is
literally "share of the list that's a first-ever appearance," which is the
same concept as tier 1 / "1st appearance" elsewhere on the page -- reusing
that exact color keeps the association intentional rather than introducing
an unrelated fifth hue to the page's palette.

**Interaction:** a single shared vertical crosshair + one tooltip that
follows pointer X, snapping to the nearest year (interaction.md's "the
crosshair finds the X" pattern for line/bar charts) -- not per-point hover
targets, since with 25 evenly-spaced points a full-width tracking rect is
both simpler and more forgiving to aim at than 25 small individual hit
areas. Verified live: hovering shows "2013 - 40 of 91 entries were a first
appearance - 44.0% turnover," matching the computed data exactly.

**2001's 100% is real data, deliberately not specially encoded.** It's
trivially 100% by definition (no prior list exists for anyone to have
already appeared on), not a finding -- but rather than hide or visually
distinguish that point on the chart itself (which risks looking like the
chart is editorializing), it's plotted like every other point and the
triviality is explained once, plainly, in the prose immediately below.

**Real numbers (recomputed with the quote-aware parser to rule out the
comma-parsing bug found earlier -- confirmed identical to the original
naive-parse pass, so that bug never actually touched this metric):** 100%
(2001, trivial) -> 89% (2002) -> a real, steady decline through the 2000s,
settling to a floor around 30-34% from the mid-2010s onward, still holding
through 2025 (with noise -- 2021 and 2024 both spike back over 40%). Read
together with the U-shape and rank-familiarity charts, all three point the
same direction: the list "matures" over roughly its first decade, then
settles into a steady state rather than continuing to trend in any
direction.

**Recurring dev-environment note:** hit the same "every chart including
unrelated ones goes blank, zero errors" symptom again while building this
chart -- same fix (`preview_stop`, close tab, `preview_start`, fresh
navigate, generous wait before checking). Given this is now the second
occurrence on this same page, it may simply be a real cost of this
project's size/session length rather than something tied to any specific
edit -- treat it as an environment quirk to route around, not a signal to
keep debugging the file's code.

## Comeback-gap stat tiles (2026-09-12)

**Form: three stat tiles in a row, not a chart.** Per the `dataviz` skill's
"is it even a chart?" table, "a handful of headline numbers" is a KPI row
of stat tiles, not a plotted chart -- these three facts (median gap,
longest gap, longest span) are single values with a named example, not a
distribution worth graphing.

**Color reused the tier-1 yellow question was considered and rejected** --
unlike the turnover line chart, these tiles aren't the same "1st
appearance" concept, so they stay neutral (light text on the dark card,
no accent hue) rather than forcing an association that isn't really there.

**This round's environment failure was worse than the first two, and
definitively proved page-independent.** Every chart on the page (including
the wheel) went blank with zero console/server errors, and unlike the
first two occurrences, **three consecutive full `preview_stop` +
`preview_start` + fresh-tab cycles did not fix it** -- including one round
where the underlying node processes were killed directly (`taskkill`
equivalent) rather than just calling `preview_stop`, ruling out a leaked
zombie process as the cause. Decisive test: navigating to Framework's own
*unmodified* `/example-dashboard` template page -- completely unrelated to
any code in this project -- also rendered zero charts. That confirms the
failure is entirely in this session's dev-server/browser-pane environment,
not in this file or any code change.

**Verification fallback used instead:** with live rendering unavailable,
correctness was confirmed by fetching the real CSV directly
(`/_file/data/annual_circle_2001_2025.csv?sha=...` -- the actual
content-hashed path Framework's `FileAttachment` resolves to internally,
extracted from the page's own compiled script; a plain `/data/...` path
404s and is a dead end for this kind of manual check) and re-running the
exact `gapStats` computation in isolation. Result matched the
already-agreed numbers exactly: median 3, 14.3% back-to-back, longest gap
19 years (The Hives, 2004->2023), longest span 24 years (Mogwai,
2001->2025, 6 appearances). Committed on the strength of that
independent verification plus visual consistency with every other
successfully-rendered element on this page (same card/text-color/spacing
system), without a final live screenshot. **Worth a spot-check next time
the dev server is opened normally**, outside this session's environment.

## Searchable artist: search box + release table + wheel highlight (2026-09-12)

**Scope decision, made explicit before building:** "searchable artist
table" turned out to mean three things at once when discussed with the
user -- a search box, a results table, and wheel highlighting. Built all
three rather than picking one, because the table alone can't show *which*
dots are the artist's without forcing a manual hover-hunt across up to 25
spokes, and the wheel-highlight alone can't show exact years/ranks/album
titles. Each piece covers what the other can't.

**Search mechanics: substring match + suggestion list, not fuzzy
matching.** With ~1,094 distinct artists, a plain case-insensitive
`.includes()` filter capped at 8 suggestions handles real usage without
the complexity (and failure modes) of a fuzzy/typo-tolerant algorithm.
Picking from the filtered list rather than free-text-submit also sidesteps
ambiguity -- the selected value is always a real `artist_id`, never a
guess at what the user meant.

**Hand-built widget, not a form-input library** -- same reasoning as the
year checkboxes: this sandbox can't reach the CDN Framework would fetch
`Inputs` from. Consistent with that precedent, `artistSearch()` is a plain
`<input>` + a floating suggestion `<div>`, exposing a `.value` getter and
dispatching a synthetic `input` event on selection/clear so `view()` picks
it up the same way it would an `Inputs` element.

**Wheel highlight uses two independent visual channels, not one.** The
existing year-checkbox dimming already claims *opacity* to mean "which
years are in scope." Reusing opacity for "which artist is selected" would
either conflict with that or require picking a precedence rule. Instead,
a matched artist's dots get **enlarged (1.8x radius) and a light stroke
ring**, layered independently of the opacity channel -- and their opacity
is explicitly forced to 1 regardless of the year checkboxes, since a
reader who searched for an artist should be able to find every one of
their dots immediately, not have some silently dimmed because that year
happened to be unchecked.

**Results table lives on the light page, not a dark card** -- a table of
text rows is closer to structured prose than to a "visual" in the sense
the dark-surface standing rule was written for (radial/spoke charts that
read as an instrument). Uses the page's own theme CSS variables
(`--theme-foreground`, `--theme-foreground-faint`, etc.) rather than
hardcoded hex, which is the *opposite* of the dark-card rule and correct
here specifically because this content sits on the light page, not inside
`#1a1a19`.

**Verified live** (environment had broken again per the pattern above; one
more `preview_stop`/close-tab/`preview_start` cycle recovered it): typing
"wilco" surfaces a single "Wilco" suggestion; selecting it shows a table
headed "Wilco -- 11 appearances (10+ albums across all 25 years)" with all
11 rows correct (including Yankee Hotel Foxtrot at #1 in 2002), and
enlarges/rings exactly the matching dots on the wheel. Clear correctly
resets both the input and the wheel to its unhighlighted state.

### Follow-up (2026-09-12): grey out non-matching dots on artist select, plus year select-all/clear-all

**User's first-use feedback:** selecting an artist enlarged/ringed their
dots but left every other dot at normal opacity (governed only by the year
checkboxes, which default to all-checked) -- the intended "just show me
Wilco" effect didn't fully land, since 2,311 other dots stayed just as
bright. Fixed by making dot opacity artist-aware: **when an artist is
selected, opacity is `1` for their dots and `dimOpacity` for literally
everything else, full stop** -- the year-checkbox opacity logic is bypassed
entirely while a search is active, rather than trying to combine both
rules. Year labels were deliberately left untouched (out of scope of what
was asked, and they're already a muted grey that doesn't compete visually
with bright dimmed-vs-highlighted dots).

**Also added, per explicit request:** "Select all" / "Clear all" buttons
above the year checkboxes -- both just toggle every checkbox's `.checked`
and dispatch one synthetic `input` event on the container, same mechanism
the artist-search widget already uses to notify `view()` of a programmatic
change.

**User also flagged, not yet actioned:** general uncertainty about whether
checkboxes are the right control for 25 years long-term (echoes the
"may change my mind" note from the year-filter's original build) -- select
all/clear all is a stopgap, not a resolution. Worth revisiting the
checkbox-vs-slider-vs-something-else question together if it comes up
again, rather than assuming these two buttons close it out.

**Verified live:** searching "Wilco" now shows exactly the enlarged/ringed
dots at full brightness with every other dot visibly dimmed; "Clear all"
unchecks all 25 year boxes and dims every dot on the wheel; "Select all"
restores all 25 and full brightness.

### Correction: artist search should intersect with the year filter, not override it (2026-09-12)

**User's real-use catch:** with only 2002 checked, searching "Wilco" showed
*all* of Wilco's dots across every year, not just the 2002 one -- the
initial "grey out everything else" fix (above) still had artist-selected
dots forced to full opacity/size/ring unconditionally, which reads as
"search overrides the year filter" rather than "search narrows within it."
The user's expected model was **AND, not override**: checking only 2002
and searching Wilco should show exactly her one 2002 dot highlighted,
and checking 2011 too should reveal a second highlighted dot -- the two
filters compose.

**Fix:** every part of the highlight treatment (opacity, enlarged radius,
stroke ring) now requires `d.artist_id === selectedArtist &&
selected.has(d.list_year)` together, not artist-match alone. A dot for the
right artist but a currently-unchecked year is just another dimmed dot,
identical to the treatment any other out-of-scope dot gets.

**Verified live:** checked 2002 only, searched Wilco -- exactly one
enlarged/ringed dot near the 2002 spoke, everything else (including
Wilco's other 10 appearances) dimmed like the rest of the wheel.

### Rank-order reading-order key (2026-09-12)

**Problem raised directly by the user:** the wedge/grid layout's snaking
column order (rank increases outward, but alternates left-to-right then
right-to-left each band) isn't self-evident just from looking at the
wheel -- unlike a single radial line, there's no obvious "this is the
order" cue.

**User's proposed fix, verified against the actual geometry before
building:** two stacked rows of small numbered circles -- bottom row
1-5 left to right, row above it 10-6 left to right -- light grey,
tucked in a corner, wordless (matching the hub arrow's own style).
Checked this against the real `xy()` band/column math before assuming it
was right: band 0 (ranks 1-5) is left-to-right by construction, and band 1
(ranks 6-10) reverses (`col = columns - 1 - rawCol`), which resolves to
physical left-to-right reading order 10,9,8,7,6 -- exactly matching the
user's proposal. Nothing needed to change about the proposed numbers.

**Placement: bottom-right corner of the SVG canvas, not tied to the
data gap.** The wheel's own 30-degree gap sits lower-left/west (a
consequence of the -90-degree start angle), but the key doesn't need to
respect that -- a circle inscribed in a square canvas leaves all four
corners empty regardless of where the data gap falls, so bottom-right
(a corner, not an edge) stays clear of spokes and dots at any zoom level.

**Verified via DOM inspection** (not just visual glance, since the glyphs
render at only 8px): the ten `<text>` nodes read exactly `1,2,3,4,5` at
the lower y-coordinate and `10,9,8,7,6` at the row above it, left to
right by x-coordinate -- confirms both the content and the row order are
correct, independent of how legible they are at a quick glance.

### Follow-up (2026-09-12): key was too small to read; added a radial reading-direction cue too

**User's real-use feedback:** the reading-order key (previous entry) was
technically correct per the DOM check, but at 8px glyphs and `#6b6a64`
(the same muted grey as the hub arrow) it was "very small" and "barely
readable" in practice -- a reminder that a DOM-verified value isn't the
same claim as "legible at normal viewing size," and both need checking.
**Fix:** nearly doubled the key's scale (`keyR` 6->11, `keyGap` 18->28,
font-size 8->12) and switched its color from the wordless-arrow grey to
`#c9c8c3` -- the same color already used for the tier-name legend text
below the chart -- so it reads as clearly as any other on-chart label
rather than as a faint afterthought.

**Second addition, same conversation:** a straight radial arrow --
mirroring the existing curved hub arrow's style and construction (`polar()`
helper, same arrowhead math) -- placed 8 degrees counterclockwise of the
2001 spoke, squarely inside the already-empty 30-degree year gap. It
points outward from hub to rim, making explicit what the curved arrow
doesn't: rank increases from center to edge, not just "read the years
clockwise." Deliberately kept at the *original* muted grey (`#6b6a64`),
not the brightened key color -- it's chrome explaining the layout
mechanic, same family as the curved arrow, not a legend needing to compete
for attention the way the numbered key does.

**Verified live:** both the enlarged/brightened key and the new radial
arrow render correctly in the empty gap beside 2001, with no overlap with
dots, labels, or the existing curved arrow.

### Second follow-up (2026-09-12): consolidated into one top-right "how to read this" cluster

**User's real-use feedback, again from actually looking at it:** two
problems with the previous placement. First, the bottom-right corner
(where the numbered key lived) sits close to the 2014 spoke, which is near
the bottom of the wheel -- and 2014's own snaking bands already show a
visually similar alternating pattern at that scale, inviting the reader to
mistake the *legend* for more *data*. Second, having the radial arrow next
to 2001 and the numbered key at the opposite corner split one coherent
idea ("here's how to read this chart") into two disconnected UI elements
in unrelated corners.

**Fix: moved everything into a single cluster in the top-right corner.**
The radial arrow that lived next to 2001 is gone entirely -- rebuilt as a
short vertical arrow standing directly beside the numbered key, pointing
from the "1-5" row up to the "10-9-8-7-6" row, recolored from the muted
`#6b6a64` to the same bright `#c9c8c3` as the key itself, so the whole
cluster reads as one idea in one color rather than two separate hints in
two different chrome tones. The **hub's original curved arrow stays
exactly where and what it was** -- clockwise year direction is a
different, orthogonal piece of information from "how rank snakes within a
spoke," and conflating the two into one cluster would have been a step
backward.

**Row order preserved through the move:** 1-5 is still the row nearer the
metaphorical hub (drawn lower, closer to where a real spoke's inner band
would be) and 10-6 is still the outward row (drawn above it) -- moving
corners didn't invert the metaphor, just relocated it somewhere it can't
be confused with real data.

**Verified live:** the numbered key, its arrow, and blank space around
both now sit together in the top-right corner with no nearby spoke's own
pattern competing with it, and the hub's curved arrow is untouched next to
2001.

### Tier-2 recolor: less saturated pink (2026-09-13)

**User's real-use feedback:** the locked tier-2 magenta (`#e67bf7`, 90% of
max chroma at its hue/lightness) looked fine as small dots on the wheel
but read as "too much pink" once it appeared as a large fill -- a full-
width bar, a stacked-chart segment, a line -- in the below-the-fold
charts. Explicitly not a rejection of the hue itself, just its intensity
at scale.

**Fix: same hue (322deg) and same lightness (0.747), chroma reduced from
90% to 60% of the gamut maximum -- `#e67bf7` -> `#d58fdf`.** Deliberately
did not touch lightness to fix this: lightness is what carries the
colorblind-safety guarantee (order recoverable via light/dark regardless
of hue), so it was left exactly where the original validated ramp put it.
Chroma is the "vividness" knob and was always the correct lever for
"this feels too loud/saturated" -- a purely cosmetic axis that doesn't
touch the ramp's safety property at all.

**Candidates considered before picking 60%:** 75% (`#dd86eb`, small
change), 60% (`#d58fdf`, chosen), 45% (`#cb98d3`, quite muted), 30%
(`#c29fc7`, risked reading as washed-out at the wheel's small dot sizes).
60% was the pick because it needed to still read clearly as its own color
family at both extremes this hue is used at -- a handful of small dots on
the wheel, and a solid full-width bar in the cohort chart -- not just look
right in one context.

**Verified live:** confirmed zero remaining references to the old hex
anywhere in the rendered page, and the on-screen tier-2 count still reads
439 (matching every prior verification of this exact metric) -- confirms
this was purely a recolor, not a data change, exactly like every other
palette pass on this page.

### Tier-2 recolor, take two: pink retired, teal locked in (2026-09-13)

**Even the desaturated rose (above) didn't land** -- user's real feedback
after living with it: still "too rosy" regardless of saturation, and the
underlying request shifted from "make the pink less loud" to "I don't
want pink at all, in a hue that still fits the wheel's purple-dominant
look." Explored via a dedicated comparison tool (7 full alternate 4-color
ramps, published as an artifact, each shown as both a small dot fan and a
large stacked bar) rather than iterating on tier 2 alone -- user's
favorite of the seven was "Cool ocean," but wanted to keep tiers 1/3/4
exactly as already locked (yellow / violet / blue-violet) rather than
adopting a whole new ramp, since that combination's "purple dominance
against black" was explicitly not something to disturb.

**Fix: tier 2 only, hue swapped to a cool teal/cyan (H=190), lightness
left at the original 0.747** (unchanged, so the validated monotonic-
lightness safety ramp needed zero rework) -- `#d58fdf` (rose) ->
`#38c4bd` (teal). Landed on teal over a same-lightness azure alternative
(`#37bde9`, H=225) after comparing both live, split down the middle of
the actual rendered wheel: teal sits 110/90 degrees from tiers 3/4 in hue
vs. azure's 75/55, so it reads as a clearly separate color rather than
blending toward the violet family the way the azure option did.

**Tiers 1, 3, and 4 are completely untouched** -- yellow `#f9d53f`,
violet `#9c57f3`, blue-violet `#5127e9`, same as since the very first
multi-hue pass. This is deliberately a one-tier fix, not a new ramp.

**Verified live:** rendered both teal and azure candidates directly in
the live wheel (not just an abstract swatch), including a literal
split-down-the-middle comparison (left half of the wheel recolored teal,
right half azure, everything else identical) to make the choice on the
real thing rather than side-by-side swatches. On-screen tier-2 count
unchanged at 439 throughout every trial.

### Tier-1 recolor: yellow to true gold (2026-09-13)

**User liked the current yellow, but was curious what a more golden/orange
option would look like** once tier 2 had settled on teal -- purely
exploratory going in, not a complaint about tier 1. Computed three real
candidates at the same tier-1 lightness family: **A** (`#fbd360`, same
L=0.88, barely warmer), **B** (`#fac45a`, L=0.85, true gold), and **C**
(`#fab965`, L=0.83, amber/orange, the deepest option that still clears a
safe lightness gap above tier 2's teal). A fourth, bolder orange option
was computed and rejected before it was ever shown -- at the lightness
true orange needs to look vivid, its gap above tier 2 dropped to 0.053,
under the 0.06 colorblind-safety floor.

**Real physical constraint worth remembering for future palette work:**
orange/gold hues can't hold much chroma at high lightness in sRGB --
pushing tier 1 toward orange at its *original* lightness (0.88) just
produces pale peach, not vivid gold. Getting real "gold" required also
lowering lightness slightly (0.88 -> 0.85 for B), trading a little
brightness for real warmth. This is the same physical fact that shaped
the original locked ramp (tier 1's hue was chosen from *where yellow is
brightest*, not an arbitrary "yellow" pick) -- it just wasn't visible
again until pushing further toward orange.

**Comparison method note, for future reference:** the first attempt to
show these live re-used the browser-automation tab directly, on the
assumption the user could see the same tab -- they couldn't ("was I
supposed to see something change?"). Fell back to the artifact-based
comparison tool (already proven to work earlier in this same
conversation) instead of continuing to describe changes in a pane the
user had no visibility into. Lesson: a live edit made through this
session's own browser-automation tools is only confirmed visible to the
user via an explicit signal (they react to something specific in it) --
never assume a shared pane is being watched in real time.

**Locked: Option B, `#fac45a`.** Tiers 2/3/4 (teal, violet, blue-violet)
untouched. Also updated the turnover-rate line chart's color (`#f9d53f`
-> `#fac45a`), which reuses tier 1's hue since it represents the same
"first appearance" concept -- five total occurrences changed, verified
zero remaining references to the old yellow anywhere on the page and the
on-screen tier-1 count unchanged at 583.

**Palette closed for now, one open note:** user's own words -- "I don't
think I'm ever going to be satisfied with the palette, but this is good
for now." Explicitly not chasing this further without a fresh trigger.
One specific complaint left on the table: the ramp reads fine in the
wheel, the line chart, and the horizontal bar chart, but feels flat
("blah") in the *column* (vertical bar) charts specifically -- i.e. the
rank-vs-familiarity chart. Not diagnosed or acted on; revisit if/when the
user brings fresh inspiration from other sites/blogs, rather than
generating more candidates blind.

## Ghost embed mechanism -- tested and working (2026-09-22)

**The question:** how does an Observable Framework page (e.g.
`annual-circle`) actually end up inside a Ghost blog post. Never built or
tested before this session; `PROJECT_BRIEF.md` flagged it as a decision
that should happen before committing to Ghost hosting.

**What was tested, concretely, not just discussed:** built the real site
(`npm run build`), served `dist/` from a plain static file server to
stand in for wherever it ends up hosted, then built a mock Ghost post --
plain HTML with a light "Casper-style" reading column -- containing an
`<iframe>` pointed at the built `annual-circle.html`, cross-origin (two
different localhost ports, so the test wasn't hiding a same-origin
shortcut). Loaded it in the browser and drove it directly: toggled year
checkboxes, typed an artist name into the search box, clicked the
suggestion, confirmed the wheel highlight + release table all render and
behave identically inside the iframe as they do standalone. Full
cross-origin JS/CSV fetches (D3, the chart's own CSV data) all loaded
fine from the child origin -- confirms static-hosted Framework output
works as a normal iframe target, no special server config needed.

**The one real gap: iframe height.** A plain `<iframe>` needs a fixed
height, but this page's height changes (search results add a table,
below-the-fold content is long). Solved with **iframe-resizer**
(`iframe-resizer` on npm, MIT-licensed core / GPLv3 for the free "full"
build used here) -- a small, established library (not something bespoke):
a parent-side script in the Ghost post's HTML card, and a child-side
script the *embedded page itself* loads. Confirmed the child script can
be added for free via Observable Framework's own per-page frontmatter
`head:` key (`src/*.md` files can set `head`, `sidebar`, `toc`, `header`,
`footer`, `pager` individually -- checked directly against
`node_modules/@observablehq/framework/dist/frontMatter.js` rather than
assumed) -- no separate build step or extra route needed. Tested this
exact setup (parent script + injected child script) end to end: the
iframe grew and shrank correctly as the year-checkbox list, the wheel,
and then the Wilco release table (11 rows) all appeared, with no internal
scrollbar and no visible cut-off.

**No separate "chrome-free" embed page needed.** Worried the Framework
theme's sidebar/header might double up against Ghost's own site nav
inside the iframe. Checked empirically at both a narrow width (~360px,
representative of a phone-width Ghost column) and 900px (desktop) --
Framework's sidebar toggle exists in the DOM but never renders as visible
chrome at either width tested. `annual-circle.md` already has `toc:
false` set; no further `sidebar:`/`header:`/`footer:` overrides turned
out to be necessary based on this test. Revisit only if a real Ghost
embed later shows chrome bleeding through at some width not tested here.

**One real warning surfaced, not yet a problem:** iframe-resizer's own
console output flagged that the Framework theme sets
`max-width: var(--observablehq-max-width)` on `<body>`, which it says
"may cause issues" with its own width detection. Did not visibly break
anything in this test (height-only resizing, not width), but worth a
second look once this is tested against a real Ghost instance rather
than a mock post shell.

**Recommended concrete setup (not yet locked -- hosting choice specifically
still open):**
1. Host the Framework `dist/` build as a static site. GitHub Pages is the
   natural fit -- same public repo already used for the "open source to a
   point" data-sharing model, no new account/service to stand up.
2. Add the iframe-resizer child script to `annual-circle.md`'s `head:`
   frontmatter (or site-wide via `observablehq.config.js`'s `head`, so
   every future chart page gets it automatically).
3. Each Ghost post that embeds a chart uses an HTML card: an `<iframe>`
   pointed at that page's hosted URL, plus the iframe-resizer parent
   script once per post (or once sitewide via a Ghost code-injection
   snippet, cleaner if there ends up being more than one embedded chart
   per post).

**Hosting picked and live (2026-09-22): GitHub Pages.** Went with the
recommendation above rather than exploring alternatives further -- same
public repo already used for the "open source to a point" model, zero new
accounts. Set up as `.github/workflows/deploy-pages.yml`: builds with
`npm ci && npm run build` and deploys `dist/` via
`actions/upload-pages-artifact` + `actions/deploy-pages` on every push to
`main`. Live at `https://john-gregson-16.github.io/kexp-best-of-data-project/`.

One real setup snag, worth remembering if this ever needs to be redone on
another repo: the very first time a repo's Pages site is created, the
workflow's own `GITHUB_TOKEN` cannot create it via API -- tried
`actions/configure-pages`'s `enablement: true` explicitly and still got
`Resource not accessible by integration` on the "create Pages site" call.
This is a hard one-time gate GitHub puts on the owner, not a config
mistake: the repo owner has to flip Settings -> Pages -> "Build and
deployment" -> Source to **GitHub Actions** once, by hand, before any
workflow can deploy. After that one manual step, the push-triggered
workflow deployed successfully on the next run with no other changes.

Also confirmed while setting this up: Framework's own build output uses
fully relative asset paths throughout (`./_observablehq/...`,
`./_file/...`, `FileAttachment("./data/...")`) -- tried adding a `base`
config for the GitHub Pages project-site subpath first, on the assumption
it would be needed, and it produced zero difference in the built HTML
(grepped for it, found nothing). Removed it rather than leave dead
config in the repo. Framework's `base` option exists for something else
that wasn't exercised here (possibly absolute canonical/og URLs or a
generated sitemap, neither of which this project uses yet) -- worth
re-checking if either of those gets added later.

## Ghost: running locally first, free (2026-09-23)

**Decision:** install Ghost locally via `ghost-cli` (SQLite, no Docker,
no database server) rather than paying for Ghost(Pro) or a VPS before
there's any real content to publish. Zero cost, fully reversible, lets
the actual site/theme/embed get built out now. Self-hosted vs. Ghost(Pro)
for the real public launch is still an open decision -- this doesn't
answer that, it just unblocks building in the meantime.

**Lives outside this repo, on purpose:** the Ghost install is at
`D:\DigMeOutliers-Ghost`, a sibling directory to `D:\_KEXP_Blog`, not
inside it and not committed to the public GitHub repo. Matches the
existing "open source to a point" data-sharing model -- the public repo
is Framework site/viz code only; Ghost's own database, theme, and any
draft post content are the owner's, not meant to be public by default.

**Node version snag, worth remembering:** Ghost v6.65.0 requires Node
`^22.23.1 || ^24.20.0`. The system Node was v24.19.0 -- one patch version
short. winget's own Node.js package listing was stale and reported no
upgrade available even though nodejs.org already had v24.21.0 (LTS
"Krypton") published. Tried the official MSI installer for v24.21.0
first; it failed (exit 1603) because this session can't pass the UAC
elevation prompt Node's installer requires. Solved by using Node's
portable Windows zip build instead -- extracted to `D:\tools\node-v24.21.0-win-x64`,
used only for Ghost, system Node left untouched at v24.19.0 (still fine
for the Framework project, which only needs Node >=18).

Because of that, every `ghost` CLI command in this folder needs the
portable Node ahead of the system one on `PATH`. Wrote
`D:\DigMeOutliers-Ghost\ghost.bat` (same pattern as this repo's own
`dev.bat`) so `ghost.bat start` / `stop` / `status` / `restart` / `log`
just work without having to remember that path each time. One practical
consequence: Ghost's local process manager is Ghost-CLI's own "local"
manager, not a Windows service -- it does not survive a reboot. After
restarting the machine, `ghost.bat start` needs to be run again to bring
the site back up.

**One-time setup step deliberately left to the owner:** Ghost's own
first-run admin account creation (site title, name, email, password) at
`http://localhost:2368/ghost/` was not filled in on the owner's behalf --
account/password creation stays a manual, owner-only step even for a
local instance.
