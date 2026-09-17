# Current Rotation — Project Brief

Origin: derived from the data pipeline at `D:\_Audio_KEXP` (KEXP listener "Best Of"
list play-history analysis). That project stays entirely separate and private —
this brief exists so a fresh session here doesn't need to re-derive any of the
decisions below.

## Name

**Current Rotation** — locked 2026-09-17, after an extended naming pass (see
"Naming, in full" below for the path that led here). Deliberately does not use
the owner's real name, keeping the separation from the Microsoft/GitHub work
identity intact.

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
  writerly pun would. **Locked.**

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

## Status (as of 2026-09-17)

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
- The Observable-Framework-into-Ghost embedding mechanism has never actually
  been built or tested.
- No domain name registered yet.
- No narrative/blog-post text written yet to accompany the wheel — the
  chart exists as a standalone page, not yet wired into any site navigation
  or framed as an actual post.
- Rollout shape undecided: several topics at launch vs. a single polished
  piece first. Current leaning (2026-09-13 conversation): ship this one
  piece well, with real commentary, rather than spreading thin across
  several at once — but not finalized.
