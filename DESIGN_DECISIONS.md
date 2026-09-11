# Design decisions

Mirrors `D:\_Audio_KEXP\GOVERNANCE_RULES.md`'s habit for the pipeline project:
every subjective call with real output impact gets written down with the
reasoning and the numbers behind it, not just the conclusion. Scoped to this
blog/visualization project specifically — pipeline-side decisions stay in
`D:\_Audio_KEXP`, not duplicated here.

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

23 of 25 years have exactly 91 entries. Three real outliers: **2012 has 120**,
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

| Tier | Meaning | Hex | Dot diameter |
|---|---|---|---|
| 1 | 1 album in 25 years | `#45c5c5` | 11px |
| 2 | 2 albums in 25 years | `#009090` | 14px |
| 3 | 3-9 albums in 25 years | `#005e5f` | 18px |
| 4 | 10+ albums in 25 years | `#002f32` | 24px |

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

Real distribution (1,095 distinct artists across all 25 Annual lists,
queried from `dim_list_entries.xlsx` joined through `dim_artist_release.xlsx`
for canonical artist IDs):

| Albums (25-yr total) | Artists | % of all artists |
|---|---|---|
| 1 | 585 | 53.4% |
| 2 | 218 | 19.9% |
| 3-9 | 289 | 26.4% |
| 10+ | 3 | 0.3% |

**User's own real finding, worth carrying into the write-up:** 53.4% of
every artist who has ever made an Annual list has exactly *one* album on it,
ever — "one and done" is the majority case, not an edge case.

**Quartiles don't work here, confirmed with the real numbers, not assumed:**
the "1 album" cohort alone (585) already exceeds two quartiles' worth of the
1,095-artist population before any cutoff is drawn — no split point can
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

- Dark-mode version of the teal ramp (needs its own validated steps against
  the dark surface — not the same ramp auto-flipped).
- Whether the "3-9" and "10+" tier boundary should be re-examined once the
  actual chart is built and visually reviewed (per the dataviz skill's own
  step 7: render it and look at it before calling any of this final).
