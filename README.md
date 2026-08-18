# Read Me First — Umesh Bhudia Portfolio System

This explains how the portfolio HTML site and the review spreadsheet work together, so a Claude session with no memory of building them can pick up maintenance correctly.

**Last structural change: August 2026 — repositioned from CV portfolio to fractional CMO service site.** See "What changed in August 2026" at the end.

## What the files are

| File | What it is |
|---|---|
| `umesh-bhudia-portfolio.html` | The live site. Single self-contained HTML file — CSS, JS, and all logo images (as base64 data URIs) are embedded inline. No external dependencies except Google Fonts. Deployed via GitHub Pages at `https://ubhudia.github.io/portfolio/` (Umesh replaces `index.html` in that repo with this file to publish updates). |
| `og-image.png` | 1200x630 social share image. **Must be uploaded to the same repo folder** so `https://ubhudia.github.io/portfolio/og-image.png` resolves, or every LinkedIn and WhatsApp share renders as a grey box. |
| `sitemap.xml` | Goes in the `/portfolio/` folder alongside `index.html`. |
| `robots.txt` | Goes at the **root** of the `ubhudia.github.io` repo, not in `/portfolio/`. robots.txt is only read from the domain root. |
| `build_site.py` | The script that applied the August 2026 restructure. Kept for reference and for the card-generation logic. |
| `Portfolio_Case_Study_Review_vN.xlsx` | The editing tool. Umesh reviews and edits case study content and tags here (spreadsheets are far easier to bulk-edit than 60+ HTML cards), then sends it back for the changes to be applied to the HTML. |

**Critical: the HTML file is the source of truth for current content**, not any conversation history or prior working files — those don't persist between chats. Always read the live HTML's `CASES` array to see what currently exists before making changes.

## Site structure (in the HTML)

Sections, top to bottom:

1. Sticky nav — logo + name + links (Services, Case Studies, Approach, Rates, FAQ) + BOOK A CALL CTA
2. Hero — fractional CMO positioning headline + KPI ticker
3. `#summary` — The Problem (6 symptom cards) + The short version + Who I work with (ICP) + honest "not the right fit" note
4. `#services` — 4 service cards: Demand Generation, ABM, MarTech & RevOps, Interim CMO
5. `#skills` — Expertise grid (15 skill cards, doubles as filter reference)
6. `#work` — **Featured Work** (6 long-form `<details>` case studies) then the filter chip bar and the 62-card grid
7. `#approach` — How an engagement runs (4-phase table) + fractional vs agency comparison
8. `#rates` — £750 day rate, retainer table, what's included, permanent CMO cost comparison
9. `#faq` — 12 questions in `<details>` elements (mirrored in FAQPage schema)
10. `#contact` footer

Design system: dark analytics-dashboard aesthetic (`--bg:#0E1520`, `--accent:#E8A33D` amber, `--teal:#4FD1C5`), fonts are Space Grotesk (display), IBM Plex Sans (body), IBM Plex Mono (data/labels/tags). Keep new UI additions consistent with this — don't introduce a different visual style. Reusable components added in Aug 2026: `.data-table`, `.service-card`, `details.fcase`, `details.faq`, `.problem-list`, `.rate-hero`, `.honest`.

## Where the data lives in the HTML

Search for `const CASES = [` in the `<script>` block near the bottom. Each entry:

```js
{company:'Hays', mono:'HY', logo:'hays', role:'Global Head of Growth', duration:'2020–2023',
  headline:'+39% MQLs · +17% revenue', tags:['strategy','brand'],
  narrative:'...'},
```

- `mono` — two-letter fallback badge, always present.
- `logo` — optional key into `LOGO_MAP` (a separate object, also in the script block, holding each logo's base64 data URI **once**). Only set `logo` if that company has a real uploaded logo; omit the field entirely otherwise (falls back to the mono badge).
- `tags` — array of skill keys (see taxonomy below). Most cards carry 1-3.

`const SKILLS = [...]` (just above CASES) defines the 15 filter/expertise categories — each has `key`, `label`, `desc`.

## ⚠️ Case cards are now PRE-RENDERED — read this before editing

**This changed in August 2026 and it is the most important thing in this file.**

Previously the 62 cards were built client-side from `CASES` via `innerHTML`. That meant crawlers and AI answer engines fetching the page saw the empty-state message and none of the content. All 62 results were invisible to search.

Now the cards exist as **static HTML** in the document, inside `#caseGrid`, between these markers:

```html
<!-- CASE-CARDS:START — generated from the CASES array by build_site.py. Do not edit by hand. -->
...62 <article class="case-card" data-tags="..."> blocks...
<!-- CASE-CARDS:END -->
```

Consequences for maintenance:

- **`CASES` is still the single source of truth.** Do not edit the static cards by hand.
- After any change to `CASES`, **regenerate the static block**. Use the `card_html()` / `CASES` logic in `build_site.py` (section 7) as the generator, and replace everything between the two markers via `re.sub`.
- The static cards carry the **mono badge only**. Logos are hydrated at runtime by the `HYDRATE LOGOS` block in the JS, which reads `data-logo` and `data-company` off `.logo-badge` and pulls the data URI from `LOGO_MAP`. This is deliberate: it keeps each base64 string in the file exactly once. **Never inline a logo into a card** — 62 cards each carrying base64 is how the file went from ~20KB to ~3.8MB of JS once before.
- `renderCases()` now toggles a `hidden-card` class on the existing DOM nodes. It does not rebuild them. Tag buttons are bound once at load because they are static.
- Card headlines are `<h3>`, cards are `<article>`. Keep it that way — it's the heading hierarchy search engines read.

## Featured Work section — hand-maintained

The six long-form case studies at the top of `#work` (Hays, Randstad, KSeye, M&S, Cigna, We Are Aspire) are **not** generated from `CASES`. They live in the `FEATURED` list in `build_site.py` and as static `<details class="fcase">` blocks in the HTML.

They are separate from the spreadsheet workflow. If Umesh edits case content in the spreadsheet, that affects the 62 cards only. If a figure changes in a featured study, edit that `<details>` block directly — and check whether the same figure appears in a 62-card entry too.

Structure of each: `summary` (company + headline) then `.fcase-body` with Context, The problem, What I did, Results (a `.data-table`), What transfers.

## Structured data — keep it in sync

There is a JSON-LD block before `</body>` containing `Person`, `ProfessionalService` and `FAQPage`.

**The `FAQPage` entities must match the visible `#faq` section exactly.** Google treats FAQ schema that doesn't match on-page content as a violation. If you add, remove or reword a question in `#faq`, update the JSON-LD in the same edit. There are currently 12.

Validate after any change: `https://search.google.com/test/rich-results`

## Current tag taxonomy (15 tags)

`strategy` (Strategy & Leadership) · `gtm` (Go-To-Market Strategy) · `branding` (Brand & Positioning) · `demand` (Demand Generation) · `abm` (ABM) · `crm` (CRM) · `martech` (MarTech & Ops) · `performance` (Performance & Paid Media) · `ai` (AI & Automation) · `digital` (Digital Transformation & CX) · `data` (Data & Attribution) · `agency` (Agency & Vendor Mgmt) · `pnl` (P&L & Budget Mgmt) · `content` (Content Marketing) · `seo` (SEO / AEO)

This list has changed once already (started at 8 tags, expanded to 15). If Umesh asks to add/split/remove a tag again, update it in **four** places: the `SKILLS` array, every affected `CASES` entry's `tags`, **the pre-rendered card block's `data-tags` and tag buttons** (regenerate it), and the spreadsheet's tag columns + Tag Reference tab.

## Spreadsheet structure

Four tabs: **Read Me First** (instructions for Umesh), **Dashboard** (tag/company/metric-type coverage — flags anything ≤2 cards in red), **Case Studies** (one row per card), **Tag Reference** (what each skill column covers).

Case Studies columns: `ID` (stable — matches card position in the HTML CASES array, 1-indexed, never renumber existing rows), `Company`, `Role`, `Duration`, `Headline`, `Narrative`, then one column per skill tag (mark `X`), then `Master CV Source`, `Metric Type`, `Logo Status`, `Verified vs Master CV`, `Delete?`.

## Workflow: applying a returned spreadsheet to the HTML

1. **Read the current HTML's CASES array first** — this is ground truth, not any prior script or memory.
2. **Read the spreadsheet.** For each row with an ID: compare against the matching HTML card (by position). Diff Company/Role/Duration/Headline/Narrative text, and diff the tag `X` marks against the card's current `tags` array.
3. **Deletions**: any row with `Y` in `Delete?` — remove that card entirely.
4. **New rows**: blank `ID` with Company through Narrative filled in — append as a new card. A row with only tag marks and no company/text is not valid — flag it back to Umesh rather than guessing.
5. **Apply text/tag edits** as given. If a card wasn't touched (no diff), leave its tags as-is — don't silently re-tag things Umesh didn't flag, but if you're doing a taxonomy-wide change (e.g. adding a new tag category to the whole set), that's a deliberate full re-tag pass, which is different from incidental drift.
6. **Rebuild programmatically, never by hand.** Write a Python script that constructs the new `CASES` and `LOGO_MAP` JS text, then use it to replace the old block in the HTML via string substitution (e.g. `re.sub` on the `const CASES = [...]` block). Do not retype large blocks (especially base64 image data) directly in your own output — this has caused real data corruption before. Base64-encode any new logo image files with a short Python/bash script that reads the local file and writes straight to disk; never hand-copy a base64 string.
7. **Regenerate the pre-rendered card block** between the `CASE-CARDS:START` / `CASE-CARDS:END` markers from the new `CASES`. If you skip this the site will display stale content, because the static cards are what render — the JS no longer builds them.
8. **After editing, verify before delivering.** Check `<script>`/`</script>` and `<style>`/`</style>` counts are balanced, plus `<section>`, `<details>`, `<article>` and `<table>`. Confirm `content.count("company:'")` and `content.count('class="case-card"')` both equal the expected case count. Check no base64 data URI appears more than once. Validate the JSON-LD parses. Render it headlessly (Playwright + Chromium are available in the sandbox) and confirm zero page errors, that logos hydrate, and that clicking a filter chip changes the visible count.
9. **Regenerate the spreadsheet to match** (same ID numbering, same tags, updated Dashboard) so the two files stay in sync for the next round.
10. Deliver the files. Remind Umesh to replace `index.html` in the GitHub Pages repo with the new HTML to actually publish it — generating the file doesn't publish it.

## Logos

`LOGO_MAP` currently has 16 real logos. Only **Family Brands** is still a mono badge — ask if Umesh has a logo for it before assuming none exists. When adding a new logo: get the image file, base64-encode it via a script (not by hand), add one `key: 'data:...'` entry to `LOGO_MAP`, and set `logo:'key'` on the relevant card(s) — don't repeat the same base64 string across multiple cards, that bloats the file badly (happened once, went from ~20KB to ~3.8MB of JS before being fixed).

## Master CV as ground truth

If asked to add new achievements or correct figures, cross-check against Umesh's `MasterCV.md` (Google Drive, folder `1. Base CVs`) rather than older archive drafts or assumption — a past inconsistency was found and fixed where a card's wording had drifted from an outdated archive source instead of the confirmed master. Never invent a metric that isn't traceable to the master CV.

**MasterCV §7.1 flags the pipeline currency figures at Hays, Randstad and KSeye (£6.0m / £2.5m / £1.6m sourced pipeline) as modelled rather than measured.** Those numbers are deliberately **not** used anywhere on the public site. Only confirmed percentage and ratio figures appear. Keep it that way — a public claim is a harder thing to walk back than a CV bullet.

## Known open items

- Family Brands has no logo yet.
- Not every card has been individually re-verified against the master CV line-by-line (`Verified vs Master CV` column) — only ones actively touched during edits have been checked.
- **No booking link yet.** All CTAs say "Book a 20 minute call" and point to `#contact`, which offers email and phone. Add a Cal.com or Calendly link and swap the CTA `href` when Umesh sets one up. This is the biggest remaining conversion gap.
- **No lead magnet.** A scored "B2B Marketing Diagnostic" was recommended as the highest-converting asset for this positioning. Not built.
- The £750 day rate and the derived retainers (£3,250 / £6,000 / £4,500 diagnostic) are live on the page. Confirm before changing.
- Page is ~1.32MB in one file. Acceptable for now given the single-file constraint, but splitting CSS/JS into separate cached assets is the next performance win if the site ever moves off the single-file model.

## What changed in August 2026

Repositioning from "here is my CV, filtered" to "here is a fractional CMO service you can buy", following a competitive audit of James Coughlan, Shanjay Damani, Will Sowerby and Steven Oakes.

**SEO and technical**
- Added meta description, canonical, robots, Open Graph and Twitter card tags. The head previously had three tags total.
- Added `Person`, `ProfessionalService` and `FAQPage` JSON-LD. None of the four audited competitors have FAQ schema, which is the main lever for being cited by AI answer engines.
- Pre-rendered all 62 case cards as static HTML. Crawler-visible word count went from roughly 330 to roughly 6,800.
- Card headlines promoted from `<div>` to `<h3>`; cards from `<div>` to `<article>`.
- Generated `og-image.png`, `sitemap.xml` and `robots.txt`.

**Positioning and content**
- Title, H1, hero and eyebrow rewritten around fractional CMO for B2B SaaS, FinTech and recruitment tech.
- "Best fit for" job-title list replaced with an ICP panel (revenue, sectors, deal shape, team, backing). The old list read as a job search, which undercuts a service offer.
- Added: The Problem section, four service cards, six long-form featured case studies, an engagement-phases table, a fractional-vs-agency comparison, a full rate card with the permanent-CMO cost teardown, and a 12-question FAQ.
- Added an explicit "where I am not the right fit" note. It is a trust signal, not a weakness — no competitor in the audited set does it.

**Accuracy fixes against MasterCV**
- `performance` skill desc: budgets "£500k to £15M+" → "£200k to £12M". The £15M+ figure was withdrawn in the MasterCV change log.
- `pnl` skill desc: same correction.
- `abm` skill desc: "enterprise pipeline from 11% to 30% of total revenue" → "marketing-sourced share of enterprise pipeline from 11% to 30%". It was never a share of total revenue.
- Ticker stat: "-47% CAC at Randstad" → "-29% blended CAC at Randstad". 47% was the top of the by-channel range being presented as the headline figure.

The 62 case cards' text and tags were **not** changed, so `Portfolio_Case_Study_Review_v4.xlsx` remains in sync and did not need regenerating.
