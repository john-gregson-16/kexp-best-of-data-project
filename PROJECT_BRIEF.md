# Dig Me Out(liers & Trends) — Project Brief

Origin: derived from the data pipeline at `D:\_Audio_KEXP` (KEXP listener "Best Of"
list play-history analysis). That project stays entirely separate and private —
this brief exists so a fresh session here doesn't need to re-derive any of the
decisions below.

## Name

**Dig Me Out(liers & Trends)** — locked 2026-09-21, after an extended naming
pass (see "Naming, in full" below for the full path, including one prior
locked-then-reconsidered name, Current Rotation). Deliberately does not use
the owner's real name, keeping the separation from the Microsoft/GitHub work
identity intact.

**Two renderings, on purpose:**
- **Stylized/full form** — `Dig Me Out(liers & Trends)` — for the site header,
  page title, and anywhere the visual trick (a smaller/lighter "(liers &
  Trends)" hanging off the album title) can actually render.
- **Plain-text form** — `Dig Me Outliers` — for the domain, social handles,
  and anywhere parentheses/ampersands don't work. Still carries the whole
  pun; "& Trends" is the stylized version's flourish, not load-bearing.

Riffs on *Dig Me Out*, the 1997 Sleater-Kinney album — a real favorite of the
owner's, not a generic pop-culture reference. "Outliers" and "Trends" aren't
decorative data-flavor words either: they're literally what the KEXP Annual
Best-Of analysis does (the tier cutoffs were found via outlier-cliff
detection; half the below-the-fold charts are trend analysis over 25 years) —
the two halves of the pun are both true, not just clever.

## What this is

A personal blog sharing narrative write-ups + interactive data visualizations,
music-focused with KEXP as a throughline rather than the whole identity —
explicitly leaves room for concert write-ups, personal collection/album
reviews, and the occasional pop-culture tangent. Not KEXP-branded, not a
business — no monetization planned, free for anyone to read/subscribe.
Primary audience: KEXP listeners, with likely crossover to modern-music
listeners more broadly. Tone target: warm, personal, unpretentious — the
owner's own reference point is the Instagram account
`whatsongrandpasturntable` (short videos of someone listening through their
grandfather's record collection) — small-scale and sincere, not trying to
sound like an authority.

## Naming, in full

Wanted the KEXP "Heavy/Medium/Light rotation" concept (a real internal KEXP
practice — the station's music director assigns DJs a quota from each
category every year) as a throughline without literally branding the blog
KEXP, and without using the owner's real name if avoidable.

Considered and checked for collisions along the way:
- **Rotation** (plain) — real collision: an active WordPress blog with a very
  similar shape (rotationworld.wordpress.com), plus several podcasts.
- **In Rotation** — a real EDM record label (Insomniac Music Group) and an
  existing "Regular Rotation" newsletter use this territory.
- **Deep Cuts** — exists as a music-marketing-industry newsletter (different
  audience/purpose, least direct collision of the early candidates).
- **Liner Notes** — most crowded: an existing chorus.fm newsletter is nearly
  the exact same concept (personal music newsletter + weekly playlist).
- **Double-Click** — rejected outright: it's the owner's own real-life work
  vocabulary (explicitly flagged as a phrase used at work), which cuts
  against the whole point of keeping this identity separate — a distinctive
  phrase a colleague would recognize is arguably a subtler tell than a real
  name. Also collides with Google's old "DoubleClick" ad-tech brand and an
  existing general-interest newsletter at doubleclick.blog.
- **Extended Rotation** / **Second Rotation** — both checked clean (no
  collisions found), both real finalists for a few days. Ultimately set
  aside: they describe *how you engage* with a topic (going deeper, a repeat
  listen), not *whose* rotation this is or that its subjects change over
  time, which is what the owner actually wanted the name to carry.
- **Current Rotation** — checked clean (no exact-match blog/newsletter
  found). "What's in my current rotation" is already a natural, commonly-used
  phrase for whatever someone's into *right now*, independent of medium —
  captures "topics come and go" directly, without needing explanation, and
  matches the unpretentious grandpa's-turntable register better than a more
  writerly pun would. **Locked 2026-09-17, then reconsidered 2026-09-20**:
  owner realized 89.3 The Current (KCMP, Minneapolis) is a real, prominent
  public radio station explicitly modeled on stations like KEXP itself
  (non-commercial, listener-supported, indie/alternative format) — close
  enough to a direct KEXP peer that "Current Rotation" risked reading as
  affiliated with a competing station. Confirmed via search before agreeing
  the concern was well-founded, not just a hunch.
- **Deep Cuts, revisited** — the first collision check (against Rotation/
  Liner Notes) undersold how generic the phrase actually is; a deeper pass
  found a live site (deepcuts.net), a blog running an actual "Deep Cuts"
  section (deepcut.co), and several social accounts. The specific stylized
  handle `deep_cuts` had no exact collision, but the underlying phrase
  carries more SEO noise than it first appeared to.
- **Signal and Noise** — re-surfaced (first proposed much earlier in the
  naming search) specifically for its music+data double meaning (radio
  signal / statistical signal-vs-noise). Rejected on a proper check: a real,
  respected decades-old quarterly journal called *Signal to Noise Magazine*
  covers almost the exact same space (experimental/improvised music
  criticism) — a direct hit, not generic noise, plus a well-known unrelated
  tech blog (Signal v. Noise) adding further confusion.
- **The Sound and the Theory** — a pun on Faulkner's *The Sound and the
  Fury* (itself from Macbeth), swapping in a near-rhyme that's also real
  music vocabulary (music theory) and doubles as "an analytical take."
  Checked clean, genuinely in the running, but set aside once **Dig Me
  Out(liers & Trends)** came together as more personally specific — a
  favorite band's own album, not a public-domain literary reference.
- **Dig Me Out(liers & Trends)** — checked clean (bare "Dig Me Out" only
  surfaces the actual Sleater-Kinney album and reviews of it; "Dig Me
  Outliers" / "digmeoutliers" returned zero results anywhere). **Locked.**

## Content taxonomy (tags)

Two separate, non-competing tag systems — deliberately not combined into one,
since double-tagging every post with both would mostly repeat the same signal
(a Heavy post is very likely also long, a Light post very likely short).

- **Heavy / Medium / Light** — the real browsable taxonomy, prominent in
  navigation. Mirrors KEXP's own rotation-tier concept directly: Heavy = the
  flagship deep-dive pieces (e.g. the KEXP Annual Best-Of analysis), Medium =
  regular takes/reviews/concert write-ups, Light = short asides, pop-culture
  tangents, anything not needing the full treatment. This is *editorial
  weight*, not length.
- **LP / EP / Single** — a quiet, automatic read-time badge shown on every
  post (styled like "12 min read," not featured in nav), computed from word
  count rather than an author's subjective call: Single < ~4 min read, EP
  ~4-10 min, LP 10+ min. Still a real Ghost tag under the hood (so it's
  filterable/has its own archive page if a reader wants "just show me the
  Singles"), just not promoted as a primary navigation category. Chosen
  specifically *not* to compete with Heavy/Medium/Light — one says what kind
  of post this is, the other says how long it'll take, and keeping them
  separate avoids clutter.

## Platform decision

- **Ghost** for the actual site/hub: landing page explaining the project, an
  "about" page covering methodology/rules/exceptions (a public-facing version
  of some of the pipeline's own documented decisions), and the posts themselves.
  Chosen over pure code-first (Astro etc.) because Ghost natively covers every
  concrete requirement without plugins: free member signup with automatic email
  notification on new posts, and native comments (with reply notifications) —
  confirmed both are built-in as of 2026, not something to bolt on.
  Self-hosted (open-source Ghost core) vs. Ghost(Pro) hosted is still open —
  self-hosted runs ~$15-30/mo (VPS+domain+email) with more maintenance burden;
  Ghost(Pro) starts at $15/mo managed. **Not yet decided — not yet set up at
  all.** How the Observable Framework site technically plugs into a Ghost post
  (iframe embed vs. something else) is worth deciding before committing to a
  hosting choice, since it may affect which fits better.
- **Observable Framework** for the interactive visualizations specifically,
  intended to be embedded into Ghost posts via iframe — same pattern as how a
  Tableau Public viz gets embedded into any blog (exact embed mechanism not
  yet built/tested). Chosen over Tableau Public/Flourish/Datawrapper because
  those tools make the *entire* uploaded dataset viewable/downloadable
  through their own UI as a structural feature, not a setting — conflicts
  with the "open source to a point" requirement below. Observable
  Framework's data loaders can be written in Python (matching the existing
  pipeline skillset), and since you write the actual code, you control
  exactly what data ships with each chart.
- Power BI's "Publish to Web" was considered and ruled out — it exposes the
  entire underlying data model (hidden tables, excluded columns), not just
  what's visibly shown, which directly conflicts with the data-sharing model
  below. Also: Microsoft blocks new Publish-to-Web embed codes by default as
  of 2026 (admin approval required).

## Data-sharing model: "open source to a point"

- This public repo (GitHub: **github.com/john-gregson-16**, on a dedicated
  Gmail, kept deliberately separate from the owner's Microsoft work
  identity/accounts) holds ONLY: the Observable Framework site/visualization
  code, and small, specific, curated data exports for each individual
  published post (e.g. the ~2,300-row table actually behind the annual-circle
  wheel).
- It does NOT hold: the Python pipeline scripts, `GOVERNANCE_RULES.md`/
  `PARKING_LOT.md`/`CLEANUP_LOG.md` (considered the owner's IP), the full
  `fact_play.csv` or star-schema exports, or the whitelist/override/curation
  logic. `D:\_Audio_KEXP` stays 100% private and untouched by any of this.

## Cadence & distribution

- New posts roughly 1-2x/month, paced by available time/learning curve —
  casual, not a publishing schedule commitment.
- First post shared via KEXP's Discord, the KEXP subreddit, and personal KEXP
  contacts; organic from there.

## Status (as of 2026-09-21)

**Built and working, this repo:**
- Full Observable Framework scaffold, Node installed, dev server running.
- First visual live at `src/annual-circle.md`: the 25-year Annual Best-Of
  radial "wheel" chart, plus four below-the-fold key-findings charts (cohort
  sizes, year-composition U-shape, rank-vs-familiarity, turnover rate),
  three comeback-gap stat tiles, a searchable-artist feature (search box +
  release table + wheel highlight), and a year checkbox filter with
  select-all/clear-all. Color palette locked (gold/teal/violet/blue-violet,
  see `DESIGN_DECISIONS.md` for the full history of how it got there).
- Every design decision along the way logged in `DESIGN_DECISIONS.md` —
  that file is the detailed record; this brief stays high-level.

**Not yet started:**
- Ghost is not set up at all (self-hosted vs. Ghost(Pro) undecided, per
  above).
- The Observable-Framework-into-Ghost embedding mechanism has been tested
  end-to-end against a mock Ghost post (2026-09-22) and works: static-hosted
  Framework build + cross-origin `<iframe>` + the `iframe-resizer` library for
  auto-height, no chrome-free "embed" page needed. Full write-up in
  `DESIGN_DECISIONS.md`. Still open: re-confirming against a real Ghost
  instance once one exists.
- No domain name registered yet.

**Built and working, hosting (2026-09-22):**
- GitHub Pages is live at
  `https://john-gregson-16.github.io/kexp-best-of-data-project/` (project
  site, no custom domain), deployed automatically by
  `.github/workflows/deploy-pages.yml` on every push to `main`. This is the
  static-hosting half of the embed mechanism above -- a real Ghost post's
  `<iframe>` would point at a URL under this domain, e.g.
  `.../annual-circle`.
- No narrative/blog-post text written yet to accompany the wheel — the
  chart exists as a standalone page, not yet wired into any site navigation
  or framed as an actual post.
- Rollout shape undecided: several topics at launch vs. a single polished
  piece first. Current leaning (2026-09-13 conversation): ship this one
  piece well, with real commentary, rather than spreading thin across
  several at once — but not finalized.
