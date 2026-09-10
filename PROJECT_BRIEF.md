# KEXP Blog — Project Brief

Origin: derived from the data pipeline at `D:\_Audio_KEXP` (KEXP listener "Best Of"
list play-history analysis). That project stays entirely separate and private —
this brief exists so a fresh session here doesn't need to re-derive any of the
decisions below.

## What this is

A personal blog sharing narrative write-ups + interactive data visualizations
built from the KEXP pipeline's analysis (e.g. cross-list percentile comparisons,
stability/volatility of albums across KEXP's "Best Of" lists, genre trends).
Not a business — no monetization planned, free for anyone to read/subscribe.

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
  Ghost(Pro) starts at $15/mo managed. Not yet decided.
- **Observable Framework** for the interactive visualizations specifically,
  embedded into Ghost posts via iframe — same pattern as how a Tableau Public
  viz gets embedded into any blog. Chosen over Tableau Public/Flourish/
  Datawrapper because those tools make the *entire* uploaded dataset viewable/
  downloadable through their own UI as a structural feature, not a setting —
  conflicts with the "open source to a point" requirement below. Observable
  Framework's data loaders can be written in Python (matching the existing
  pipeline skillset), and since you write the actual code, you control exactly
  what data ships with each chart.
- Aesthetic target: NOT the default sterile/academic look of generic Observable
  Plot examples — closer to The Pudding's whimsical, illustrated "visual essay"
  style. Confirmed achievable on the same D3 foundation Observable sits on;
  it's a matter of custom design effort, not a tooling limit. Better gallery
  to reference for calibration: observablehq.com/top (curated "most popular"),
  not the generic docs/tutorial examples.
- Power BI's "Publish to Web" was considered and ruled out — it exposes the
  entire underlying data model (hidden tables, excluded columns), not just
  what's visibly shown, which directly conflicts with the data-sharing model
  below. Also: Microsoft blocks new Publish-to-Web embed codes by default as
  of 2026 (admin approval required).

## Data-sharing model: "open source to a point"

- This public repo (GitHub: **github.com/MrFadedGlory**, kept deliberately
  separate from the owner's Microsoft work identity/accounts) will hold ONLY:
  the Observable Framework site/visualization code, and small, specific,
  curated data exports for each individual published post (e.g. the ~50-row
  table actually behind one chart).
- It will NOT hold: the Python pipeline scripts, `GOVERNANCE_RULES.md`/
  `PARKING_LOT.md`/`CLEANUP_LOG.md` (considered the owner's IP), the full
  `fact_play.csv` or star-schema exports, or the whitelist/override/curation
  logic. `D:\_Audio_KEXP` stays 100% private and untouched by any of this.

## Cadence & distribution

- New posts roughly 1-2x/month, paced by available time/learning curve —
  casual, not a publishing schedule commitment.
- First post shared via KEXP's Discord, the KEXP subreddit, and personal KEXP
  contacts; organic from there.

## Status

- `D:\_KEXP_Blog` created and git-initialized 2026-08-23. Nothing scaffolded
  yet — Observable Framework setup is the next real step, blocked on Node.js
  not being installed in this environment (needs the LTS installer from
  nodejs.org before `npm create @observablehq/framework` can run).
