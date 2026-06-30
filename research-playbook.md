# Coming-Soon Coffee Shop Research Playbook

Purpose: keep `data/coming-soon-leads.json` current with real, sourced leads on
coffee shops that are rumored, confirmed, opening soon, or very recently opened
(within ~60 days) across Charlotte/Metrolina (Charlotte, Concord, Matthews,
Davidson, Huntersville, Cornelius, Mooresville, Rock Hill SC, Gastonia, Belmont,
Indian Trail, Fort Mill SC, and similar surrounding towns). This file is the SOP
a future Claude session runs weekly using only WebSearch / WebFetch (no logins,
no APIs). Follow it top to bottom each run.

## 0. Before you start

- Read the current `data/coming-soon-leads.json` in full. Build a mental (or
  scratch) list of existing `id`s, names, and cities so you can dedupe as you go.
- Note today's date — you'll need it for `firstDetected` on new entries and for
  evaluating whether existing `expectedOpening` windows have passed.

## 1. Source types to check every run

### (a) Local news outlets (highest signal-to-noise; check first)
These are the outlets that produced real, citable results during seeding —
prioritize them:
- **Axios Charlotte** (axios.com/local/charlotte) — best single source found;
  covers openings, leases, and grand openings with real addresses and dates.
- **What Now Charlotte** (whatnow.com/charlotte) — reliably covers restaurant/
  cafe openings with permit-level detail (square footage, addresses).
- **Charlotte Business Journal** (bizjournals.com/charlotte) — good for lease
  signings and commercial real estate angles; may be paywalled, rely on
  WebSearch snippets and secondary coverage (e.g. What Now Charlotte often
  republishes CBJ leads) if WebFetch is blocked.
- **QCity Metro** (qcitymetro.com) — strong for Black-owned business coverage
  and Uptown/community-hub openings.
- **Charlotte Observer** (charlotteobserver.com) — general coverage; may be
  paywalled. Search via WebSearch for snippet text rather than relying on
  WebFetch succeeding.
- **Hoodline** (hoodline.com/charlotte or city-specific) — picks up smaller/
  neighborhood openings other outlets miss.
- **Post and Courier — York County bureau** (postandcourier.com/york-county) —
  the single best source for Rock Hill / Fort Mill / Clover / SC-side leads;
  they ran a "Coffee Trail" roundup article worth checking for a follow-up.
- **Eater Charlotte** — check if still active; was largely dormant/folded at
  last check, so don't rely on it, but a quick search costs nothing.
- Town-specific Patch sites and city economic development "coming soon" pages
  exist for some towns (e.g. `choosegastonia.com/projects` for Gastonia) —
  search `"[town name] economic development coming soon"` to find each town's
  equivalent if you haven't already.
- **Charlotte Agenda / Axios Charlotte successor content** — Charlotte Agenda
  was absorbed into Axios Charlotte; just use Axios Charlotte going forward.

Known WebFetch caveat: Axios.com URLs returned HTTP 403 to WebFetch during
seeding even though they appear fine in WebSearch result snippets/summaries.
If a direct WebFetch on an axios.com (or other) URL 403s, don't waste retries —
rely on the WebSearch result summary text itself (it often contains enough
detail), or look for a secondary outlet (What Now Charlotte, Hoodline, etc.)
that republished/covered the same story and fetch that instead.

### (b) Social media chatter (no API access — use search-engine proxying)
There is no Reddit/Instagram/X API available. Realistic approach:
- Run WebSearch queries like `site:reddit.com charlotte new coffee shop` or
  `site:reddit.com "coming soon" coffee [city name]`. Note: during seeding,
  `site:reddit.com` queries returned **no results** — Reddit content is poorly
  indexed by this search tool. Don't burn more than 1-2 queries per run on
  direct Reddit site-search; treat it as a low-yield channel.
- Better proxy: search `reddit.com r/Charlotte coffee shop opening` (without
  the `site:` operator) or `r/CLT new coffee shop` — sometimes surfaces
  aggregator/snippet results even when `site:` filtering doesn't.
- For Instagram/TikTok/X, search by likely phrasing instead of platform:
  `"coming soon" coffee Charlotte instagram`, `[shop name] Charlotte opening
  TikTok`. Viral/TikTok-native brands (e.g. La La Land Kind Cafe) are
  genuinely discoverable this way since press picks up the TikTok angle.
- Honest fallback: if a shop's only signal is social buzz with no press pickup,
  log it as `status: rumored`, `confidence: low`, and note in `notes` that it's
  social-only and unverified — don't fabricate corroboration.

### (c) Permits / licensing / commercial real estate signal
- **Mecklenburg County** permit/license lookups are not realistically
  query-able via plain web search (they sit behind county portal search forms,
  not indexed pages). Manual-check fallback: note in the entry that a
  Mecklenburg County permit search at the county's LUESA/permit portal would
  be needed for verification, but don't claim to have checked it — rely
  instead on news articles that already reference "permits indicate..." (this
  worked well for the 7 Brew Concord lead, sourced from What Now Charlotte
  citing permit filings).
- **Surrounding counties** (Cabarrus, Union, Iredell, Gaston, York SC) — same
  limitation. `explorecabarrus.com` runs a "What's New & Coming Soon" page for
  Cabarrus County (Concord/Kannapolis area) that IS a searchable, useful page —
  search `"[county name] what's new coming soon"` for each surrounding county
  to find similar curated pages.
- **NC ABC permit applications** — only relevant for coffee+alcohol concepts
  (e.g. Plot Twist). Not realistically searchable directly without the ABC
  portal; rely on press coverage that mentions liquor license / ABC permit
  status instead.
- **Commercial real estate leasing announcements** — search
  `"[city] retail space leased coffee 2026"` or check CommercialCafe/LoopNet
  listing pages that sometimes get indexed; treat as low-yield, occasional-hit
  source, not a reliable weekly channel.
- City-specific economic development "coming soon"/"projects" pages (example:
  `choosegastonia.com/projects`) are worth a direct WebFetch each run for towns
  that have them — check whether Concord, Matthews, Huntersville, Mooresville,
  Cornelius, Indian Trail, Gastonia, Belmont, Fort Mill, and Rock Hill each
  have an equivalent and bookmark the working URLs as you find them.

## 2. Search query templates to run every cycle

Run these substituting in the current year. Treat each as a starting point —
follow promising result links with WebFetch.

General / Charlotte core:
- `coffee shop coming soon Charlotte NC [year]`
- `new coffee shop opening Charlotte [year]`
- `coffee shop coming soon Charlotte Agenda OR QCity Metro [year]`
- `Charlotte Business Journal new coffee shop lease [year]`
- `"coming soon" coffee Charlotte`

Per surrounding town (run individually or paired, e.g. two towns per query):
- `coffee shop coming soon Concord NC [year]`
- `coffee shop coming soon Matthews NC [year]`
- `coffee shop coming soon Huntersville NC [year]`
- `coffee shop coming soon Davidson NC [year]`
- `coffee shop coming soon Cornelius NC [year]`
- `coffee shop coming soon Mooresville NC [year]`
- `coffee shop coming soon Indian Trail NC [year]`
- `coffee shop coming soon Gastonia NC [year]`
- `coffee shop coming soon Belmont NC [year]`
- `coffee shop coming soon Rock Hill SC [year]`
- `coffee shop coming soon Fort Mill SC [year]`

Social proxy:
- `site:reddit.com charlotte new coffee shop opening [year]` (low yield, run
  once, don't over-invest)
- `reddit.com r/Charlotte coffee shop opening`
- `"coming soon" coffee shop Charlotte instagram OR tiktok`

Permit/county pages:
- `"[county name] what's new coming soon" coffee`
- `"[city name] economic development" coffee coming soon`

Chain-tracking (worth an occasional check since regional chains like 7 Brew,
Dutch Bros, Scooter's, Ellianos, Bad Ass Coffee of Hawaii are actively
expanding into the Metrolina area and generate real review interest on first
local opening):
- `7 Brew OR Dutch Bros OR Scooter's Coffee new location Charlotte metro [year]`
- `Ellianos Coffee OR "Bad Ass Coffee of Hawaii" South Carolina expansion [year]`

## 3. Deduplication rules

- Before adding a new entry, compare candidate `name` + `city` against all
  existing entries in `coming-soon-leads.json`.
- Exact match (case-insensitive) on name+city → do not create a new entry;
  update the existing one instead (see Section 4).
- Fuzzy match: if the candidate name shares a strong root with an existing
  entry (e.g. "Burr & Berry" vs "Burr and Berry Coffee", "7 Brew" vs "7 Brew
  Coffee"), treat as the same shop. Also watch for brand sub-names (e.g.
  "Waterbean Coffee" vs "Waterbean Coffee Exquisite" — same parent brand, but
  if it's a genuinely distinct new location/address, it's a new entry; just
  make the city/neighborhood and notes clearly distinguish them).
- If a candidate is the same shop but a different specific location (a chain
  opening a 2nd/3rd/4th location in a different city or neighborhood), create
  a **new** entry with a distinct `id` (e.g. append the street or location
  qualifier to the slug) rather than overwriting the existing one — each
  physical location is a separate review opportunity.
- `id` format is `kebab-case-name-and-city`; if a collision would occur, add a
  differentiator (street name, neighborhood, or "2nd-location") to keep ids
  unique.

## 4. Rules for updating status over time

Status ladder: `rumored` → `confirmed` → `opening_soon` → `opened` → `reviewed`.

- **rumored → confirmed**: upgrade when a second independent, credible source
  (different outlet or an official source like the business's own social
  account / a permit filing) corroborates the original rumor. Add the new
  source to the `sources` array rather than replacing the old one.
- **confirmed → opening_soon**: upgrade when a specific opening date or narrow
  window (e.g. "early April 2026", "targeting March") is reported, or signage/
  hiring posts appear.
- **opening_soon → opened**: upgrade as soon as a source confirms the doors
  are actually open (grand opening coverage, the business's own "we're open"
  post, or a dated article describing it in the present tense as operating).
  Update `expectedOpening` to read "Opened [date]" once confirmed.
- **opened → reviewed**: this transition is NOT made by the research process —
  it's set externally by Caleb/the dashboard once he's actually published a
  review. Never set `reviewed: true` or `status: reviewed` yourself during a
  research run; leave that field alone on existing entries unless explicitly
  told otherwise.
- **Stale `opening_soon` / `confirmed` entries**: if today's date is past the
  `expectedOpening` window and you find no new corroborating signal during
  this run, do **not** silently delete the entry. Instead:
  - Do one targeted follow-up search (`"[shop name]" "[city]" opened OR
    delayed OR closed [year]`) to check for an update.
  - If still no new information, leave the entry in place but prepend a flag
    to its `notes` field, e.g. `"STALE AS OF [today's date]: expected opening
    window has passed with no further signal found — needs manual check."`
  - Only remove an entry if you find a credible source confirming the project
    was cancelled/fell through.
- Always append new source objects rather than deleting old ones, so the
  history of how confidence built up is preserved in the file.

## 5. Confidence scoring guidance

- **high**: multiple independent credible sources (2+ established local news
  outlets, or 1 news outlet + the business's own official social/website
  confirming the same facts), with a specific address and either a firm date
  or confirmed "opened" status. Example: Plot Twist (4 sources, opened and
  confirmed via multiple outlets).
- **medium**: a single credible, named local news outlet with specific details
  (address, square footage, named owner) but either no second source yet or
  the opening date is vague/unconfirmed. Example: 7 Brew Concord (permit-based
  reporting, one outlet, no firm date).
- **low**: a single mention in a roundup/listicle-style article with minimal
  detail (no address, vague timeline), social-only buzz with no press pickup,
  or information that's old enough that current status is genuinely unknown.
  Example: Ellianos Coffee Clover (single-source roundup mention, no address
  or date).
- When in doubt between two levels, round down — false negatives (missing a
  real opening) are cheaper than false positives (Caleb showing up to review a
  shop that isn't actually open yet).

## 6. Required housekeeping every run

- Always bump `lastUpdated` to the current ISO 8601 timestamp at the top of
  the JSON file, even if you only changed/flagged one entry.
- Keep the JSON valid — validate with a quick parse before finishing (e.g.
  `python3 -c "import json; json.load(open('data/coming-soon-leads.json'))"`)
  if a shell is available; if not, carefully re-read the file for syntax
  errors before considering the run done.
- Do not touch any HTML/dashboard files — this file (`coming-soon-leads.json`)
  is the only deliverable of a research run.
- At the end of each run, leave a short mental/scratch tally (not necessarily
  written anywhere permanent) of: how many new entries added, how many
  existing entries upgraded in status, how many flagged as stale, and which
  towns returned thin/no results — so the human reviewing the run knows where
  coverage is weak (this mirrors the honesty requirement from the original
  seeding pass: several towns, e.g. Davidson, Huntersville, Indian Trail,
  Belmont, returned little to no "coming soon" signal as of the June 2026
  seed — don't force entries into existence to fill that gap).
