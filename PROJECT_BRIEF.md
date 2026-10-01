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
Listeners' Favorite analysis does (the tier cutoffs were found via outlier-cliff
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
  flagship deep-dive pieces (e.g. the KEXP Annual Listeners' Favorite
  analysis), Medium =
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
- First visual live at `src/annual-circle.md`: the 25-year Annual
  Listeners' Favorite radial "wheel" chart (renamed from "Best-Of"
  2026-09-27, see naming note below), plus four below-the-fold
  key-findings charts (cohort
  sizes, year-composition U-shape, rank-vs-familiarity, turnover rate),
  three comeback-gap stat tiles, a searchable-artist feature (search box +
  release table + wheel highlight), and a year filter -- now a dropdown
  multi-select (redesigned 2026-09-26 from the original always-visible
  checkbox grid; same select-all/clear-all, same interaction contract).
  Color palette **re-locked 2026-09-26**: gold `#d6b45b` / teal `#58cec8` /
  violet `#9c57f3` (unchanged) / blue `#2d6fbe` -- see `DESIGN_DECISIONS.md`
  for the full history of how it got there, including both the original
  lock and this re-lock.
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

**Built and working, Ghost (2026-09-23):**
- Ghost is running locally (free, no hosting decision made yet) at
  `D:\DigMeOutliers-Ghost` -- a sibling folder to this repo, not committed
  to GitHub. `http://localhost:2368/ghost/` for admin,
  `http://localhost:2368/` for the site itself. Start/stop with
  `D:\DigMeOutliers-Ghost\ghost.bat start` / `stop` / `status` (a required
  portable Node version wrapper -- see `DESIGN_DECISIONS.md` for why).
  Self-hosted vs. Ghost(Pro) for the eventual public launch is still
  undecided; this only unblocks building the site/theme/embed now.
- ~~No narrative/blog-post text written yet~~ — **done 2026-09-24.** Intro
  (the 3-artist/10+-album hook) and closing (the tier-cutoff cliff, the
  53.3% one-and-done stat) added around the embedded chart in the Ghost
  draft. Still sitting unpublished — owner wants to sit with it first.
- General sources/list-curation notes (from a Reddit post sharing the
  original list file) saved verbatim at
  `content-drafts/sources-and-curation-notes.md` — not yet placed
  anywhere on the site. Covers the whole dataset, not one chart, so it
  shouldn't be repeated per-post; leading idea is a dedicated page linked
  from About and from each data post, not yet committed to.
- **About page restructured (2026-09-25/26):** the "how the data comes
  together" section moved out of About entirely into its own new post,
  "Where this KEXP data actually comes from" — expanded with the real
  origin story (the record-store shopping-list line from the Reddit post)
  and the Annual-then-Special-lists roadmap. About is now just identity/
  scope/what's-shared, no data mechanics. The name explanation was also
  trimmed to a single aside, not a justified pun, per owner's steer ("people
  either get it or they don't"). Both pieces exist as matching drafts on
  the local instance and the Ghost(Pro) trial.
- **About page body text finalized (2026-09-27):** owner rewrote the page
  (identity/tone intro, no-ads/no-sponsorship line, the "what's shared and
  what isn't" data-sharing paragraph) and it's now synced word-for-word on
  both the local instance and the Ghost(Pro) trial, still as an unpublished
  Draft. Only remaining gap on this page: "Get in touch" is still a
  placeholder pending the contact-method decision (see below).

**Domain and hosting decision, both settled 2026-09-25:**
- `digmeoutliers.com` registered (Namecheap), ICANN email verified. DNS
  still unpointed — nothing live there yet, on purpose (holding off until
  ready).
- Ghost(Pro) chosen over self-hosted. The trial account created by
  accident earlier (`https://dig-me-out-liers-and-trends.ghost.io`) is
  the one being used -- no fresh signup. Casper theme, the real-name
  fixes (Staff full name/slug, Site title, the default "About this site"
  page), the About page, and the wheel post (intro + embed + closing)
  have all been recreated there to match the local instance exactly.
  Local instance (`D:\DigMeOutliers-Ghost`) still exists too and is kept
  in sync by hand for now -- not automated, and not yet decided whether
  it stays around once Ghost(Pro) is the real site.
- **Not yet done:** converting the Ghost(Pro) trial to a paid plan
  (owner's own billing action), pointing `digmeoutliers.com`'s DNS at
  Ghost(Pro) instead of the `.ghost.io` subdomain, and deciding where the
  Framework chart site (currently on GitHub Pages) sits relative to the
  real domain -- a subdomain is the leading idea, not committed to.
- Rollout shape undecided: several topics at launch vs. a single polished
  piece first. Current leaning (2026-09-13 conversation): ship this one
  piece well, with real commentary, rather than spreading thin across
  several at once — but not finalized.

**Content restructuring (2026-09-25/26):**
- Wheel post's full narrative text (both the Ghost post's own wrapper prose
  and the chart's separate internal "Key findings" narrative) exported to
  `content-drafts/post-25-years-of-kexp-annual-listeners-favorite-lists.md`
  and `content-drafts/chart-25-years-of-kexp-annual-listeners-favorite-lists.md`
  (renamed 2026-09-27 to match the naming decision below) for the owner's
  own editing pass.
- Naming question raised 2026-09-25/26: whether to call these "Best Of" or
  "Listeners' Favorite" lists. **Decided 2026-09-27: "Listeners' Favorite."**
  Narrative-only, no code/data change. Applied everywhere the wheel post's
  own name appears: `src/annual-circle.md` (frontmatter title, H1, intro
  sentence), the post title + intro paragraph on both Ghost instances (also
  caught and fixed a stale "use the checkboxes" reference left over from the
  2026-09-26 dropdown redesign while in there), and both content-drafts
  export files. **Not yet touched, deliberately out of scope for this
  pass:** the whole-site/repo branding still says "KEXP Best Of Data
  Project" (`observablehq.config.js`'s `title`, `README.md`, `src/index.md`
  landing page H1) and the GitHub repo/Pages URL itself
  (`kexp-best-of-data-project`) -- renaming those is a bigger, separate
  decision (the URL is already live and embedded in the Ghost iframe), not
  something to fold into a wheel-post text pass.
- **Overview post fully rewritten and pushed live, 2026-09-27** (retitled
  "KEXP listeners' favorite albums data project," was "Where this KEXP
  data actually comes from"): the real origin story (record-store
  shopping-list line), the Annual-then-Fall-Drive roadmap, a naming-note
  paragraph on KEXP's own inconsistent list naming (researched via the
  Wayback Machine -- see `DESIGN_DECISIONS.md` for the sourced history:
  "Top 903 Albums" -> "Year-End Poll" -> "Top Albums" -> "Best of/Best
  Albums of [Year]," sometimes both live at once), the three-source
  breakdown, the editorial-judgment paragraph, and the no-vote-counts
  caveat (owner confirmed the "10 albums" ballot size is accurate, not
  assumed). Synced word-for-word on both Ghost instances. The closing
  sentence names the wheel post but isn't yet a real hyperlink -- Ghost's
  internal link picker only surfaces **published** posts, and both posts
  are still drafts; link it via Ctrl+K once either post is published.

- **New "Data Notes" page, built and pushed live, 2026-09-28** (a Ghost
  Page, same structural tier as About): consolidates list-curation
  transparency in one place instead of repeating it per-post. Built from
  `content-drafts/sources-and-curation-notes.md`'s raw material, but
  substantially re-scoped for this page's actual audience/purpose rather
  than ported verbatim -- that file originally accompanied a shared Excel
  sheet, not this site. Key changes from the source material: naming
  conventions section collapsed to a single paragraph (owner now pulls
  artist/album names straight from MusicBrainz by artist ID, no manual
  normalization of their own); every item tied to a not-yet-covered
  special/Fall-Drive list (Top 903, 21st Century, Top 666, 50 Years, All
  Time) removed until those lists get their own analysis; the
  *MassEducation* item dropped entirely (owner confirmed on reflection
  it's not actually ambiguous -- St. Vincent has two genuinely distinct
  2017/2018 releases, not a same-title collision needing disambiguation).
  Structured as two explicitly separate sections per the owner's own
  framing: "Corrections -- what I believe to be real KEXP errors" vs.
  "Curation calls -- my own judgment on ambiguous entries," so readers
  (and any future KEXP staff who find the site) aren't left guessing which
  kind of note they're looking at.
  **One new corrections entry, verified against the real CSV, not just
  recalled:** four albums each appear on two different Annual lists a year
  apart, at a notably better rank the second time -- Elbow's *Cast of
  Thousands* (2003 #71 -> 2004 #39), Vampire Weekend's debut (2007 #22 ->
  2008 #2), The Head and the Heart's debut (2010 #16 -> 2011 #4), Of
  Monsters and Men's *My Head Is an Animal* (2011 #16 -> 2012 #10). Owner's
  own theory, included in the page copy: KEXP likely re-added a late-year
  release to the next year's ballot (maybe unknowingly), and voters had no
  reason to know or care it had technically already had its turn.
  **Also added:** a closing invitation for KEXP itself (or anyone with
  better information) to submit corrections via "contact me" -- currently
  unlinked, same reason as the wheel-post reference above (Ghost's link
  picker won't surface the still-draft About page either; link once
  either page is published).
- **Wheel post now references Data Notes (2026-09-28):** appended a
  sentence to the existing "Author's note" callout in
  `src/annual-circle.md` ("These lists also go through some manual
  reconciliation before they end up here -- see the Data Notes page..."),
  mirrored in the matching content-drafts export. Left as plain text, not
  a real link -- this one's a two-part blocker, not just "wait for
  publish": the chart is hosted on GitHub Pages and Data Notes lives on
  Ghost, a different domain, so it needs an absolute URL, and which Ghost
  domain (the `.ghost.io` trial vs. a future `digmeoutliers.com`) is
  itself still undecided. Fill in the real `<a href>` once both the page
  is published and the domain question is settled.

**Ghost hero cover (2026-09-26):** a violet-to-teal gradient PNG (pulled
from the two ends of the locked chart palette), uploaded as the Ghost(Pro)
trial's Publication Cover, replacing Ghost's factory-default pink. Source
files at `design-assets/cover-violet-teal.png` (locked) and
`cover-violet-blue.png` (runner-up, kept for reference). Not yet mirrored
to the local Ghost instance — lower priority than content/theme/palette
work, since it's a visual asset only. Full color-theory/generation/upload
story in `DESIGN_DECISIONS.md`.

**Still open, no owner decision yet:**
- Ghost(Pro) plan tier: Starter/Source vs. Publisher/Casper. (Trial's
  active theme is currently Casper either way -- Typography's "Theme
  default" setting will auto-follow whichever theme ends up active, no
  manual migration needed when this gets decided.)
- Contact method for the About page's "Get in touch" section -- **decided
  2026-09-27: deliberately deferred, not unresolved.** Owner's existing
  daily-checked inbox (msn.com) has their real name as the address's local
  part, so publishing it would break the identity firewall this whole
  project has maintained (same category of risk as the two real-name leaks
  Ghost's own defaults caused earlier). The already-set-up name-free
  dedicated Gmail was one live option (as-is, or with auto-forwarding into
  msn.com so replies aren't missed), but owner chose to wait instead and
  use `hello@digmeoutliers.com` once the domain's DNS/email hosting is
  sorted -- the most on-brand answer, just blocked on decisions already
  being deliberately held off (see "Domain and hosting decision" above).
  Placeholder text in the About draft stays as-is until then; not a bug,
  don't "fix" it without this context.
- **Logo: built and live (2026-10-01).** A shovel blended into a 1972
  Gibson-SG-style headstock, inside a circle that doubles as "the
  ground" -- the shovel blade deliberately breaks through the ring (ties
  back to "digging," the concept's real hook). Built entirely by the
  owner in Inkscape, traced from a real SG headstock photo, with the
  headstock's subtle top-center "mustache" notch and a curved
  neck-to-blade shoulder transition both hand-fixed after the initial
  auto-trace smoothed them away. Scale-legibility was actually tested
  (not assumed) against the owner's own stated bar ("roughly a
  headstock w/ or w/o tuners, roughly a shovel, shovel reads as breaking
  through the circle") using an HTML harness rendering the real exported
  PNG at 16-120px -- passes cleanly at 48-60px, Ghost's actual icon
  floor. **Both the black (transparent bg) and white (transparent bg)
  versions exist**; black is uploaded live as both the Publication logo
  and Publication icon on both Ghost instances (replacing the old
  Rebel-Alliance placeholder icon). The white version isn't placed
  anywhere yet -- candidate use is overlaying it on the hero cover
  gradient as a title-page treatment, not decided. Confirmed directly in
  both themes' own header code (`{{#if @site.logo}}`): a Publication
  logo replaces the site-title text, it doesn't sit alongside it -- the
  wordmark would need to be baked into the image if both were wanted
  together, which the owner decided not to do (mark only, no wordmark).
  Publication icon is a separate field (small square, favicon + Source's
  sidebar "about" avatar only, invisible on Casper).
