# Site Update Plan: single page to multi-page Jekyll

**Status date:** 8 October 2026
**Replaces:** `NEW_CHAT_BRIEF.md` and `PAGE_SPLIT_MAP.md` where they conflict with this file.
**Checked against:** the live `index.html` (1.36MB, identical to `index.html` in this repo), `privacy.html`, `build_v4.py` and `README_Portfolio_System.md`.

**Scope note.** The original audit and growth plan chat was not in the upload, only the files above. This plan is rebuilt from the brief, the split map and the current HTML. If the chat held decisions not repeated in those files, send it and I will reconcile.

---

## 1. What the latest content changed

| # | Item | Old plan said | Live site shows | Action |
|---|---|---|---|---|
| 1 | Case cards | 62 (split map, rule 6) | **64** (`kseye-05`, `weareaspir-03` added) | Use 64 everywhere. Static `filterCount` text must read 64 |
| 2 | Tags | 15 | **16** (`growth` added, 25 cards) | Update `SKILLS`, chips, spreadsheet. `gtm` is still thinnest at 3 |
| 3 | Total URLs | 21 | **22** (adds `/privacy/`) | Add `privacy.html` to sitemap and split map |
| 4 | Embedded images | "16 logos" | **20 data URIs**: 16 logos, 1 nav logo, 3 favicon/apple-touch icons | Extract all 20, not 16 |
| 5 | Logo file type | `/assets/logos/*.png` | 12 PNG, 2 JPEG (`ladbrokes`, `accessoriesdirect`), 1 WebP (`euromoney`) | Write each with its real extension. Do not force `.png` |
| 6 | Logo size | not mentioned | `ms` 3840x2160 (255KB), `pomelo` 3840x2160 (64KB), `hays` 2114x1064 (72KB), `signet` 2290x612 (78KB), shown at about 48px | Resize on extraction. Cap long edge at 192px (2x display). |
| 7 | Booking | cal.com links, leave alone | 30 minute slot, 10 links, `#contact` no longer used as CTA | Keep. Grep the URL, never hand-edit |
| 8 | Nav | Services, Case Studies, Approach, Rates, FAQ | **Services, Case Studies, How It Works, Rates, Insights, FAQ** | Header include uses the 6 current links |
| 9 | Anchor links | "roughly 20" | **9 anchors, 6 distinct** (`#rates` x3, `#work` x2, `#services`, `#model`, `#insights`, `#faq`) | Smaller job than planned. Map in section 3 |
| 10 | Hard-coded base URL | not mentioned | `ubhudia.github.io/portfolio` appears **13 times** (canonical, OG, Twitter, JSON-LD) | Replace with `{{ site.url }}{{ site.baseurl }}{{ page.url }}` |
| 11 | Privacy policy | not in plan | Standalone, claims no cookies, analytics, forms or storage | Move into layout. Keep claims true (see section 5) |
| 12 | Files named in the brief | `retag.py`, `apply_retag.py`, `make_v5.py`, `MasterCV.md`, `Portfolio_Case_Study_Review_v5.xlsx` | **Not in the upload.** Only `build_v4.py` is there | Send them before Step 7 (CI) |
| 13 | Repo `README.md` | n/a | Out of date: says 15 tags, 62 cards, "no booking link". The zip copy is current | Replace repo README with `README_Portfolio_System.md` |
| 14 | `sitemap.xml` | 1 URL | 1 URL, `lastmod` 2026-08-18, no `privacy.html` | Update now, before any split |
| 15 | `robots.txt` | root of `ubhudia.github.io` | In this repo it sits in `/portfolio/`, where crawlers ignore it | Move to the user-site repo, or to the custom domain root |
| 16 | Crawlable words | about 10,000 | **10,551** | Baseline for the post-split comparison |

---

## 2. Decision needed before any code

The two source documents disagree on order.

| Option | Sequence | Pros | Cons | Impact |
|---|---|---|---|---|
| **A. Domain first (recommended)** | Buy domain, set `CNAME`, verify in Search Console and Bing, wait 4 to 6 weeks, then split | Authority builds on the final URL. No redirects later. `baseurl` is empty, so links are simpler | Delays the 21 new pages that target specific keywords | Slower start, no rework |
| B. Split now on github.io | Start Step 1 from the brief | New keyword pages indexed sooner | Authority built on `github.io/portfolio` is lost or needs 301s on a host that cannot redirect. `baseurl: /portfolio` then has to be undone | Faster start, rework later |

The split map says "Do not split yet. Publish, buy a domain, then split". The brief's open items say the domain was deferred deliberately. The brief's Step 1 starts the split anyway. These cannot both stand.

**Recommendation: A.** It is now 7 weeks since the August publish. If the domain is bought this week, the 4 to 6 week index window ends in mid-November.

**What can happen now under either option** (no authority is split):
1. Update `sitemap.xml` and add `privacy.html`.
2. Move `robots.txt` to a location crawlers read.
3. Extract and resize logos into `/assets/logos/` and replace the hydration JS (Step 1 below). This also cuts the single page from 1.36MB to under 0.3MB of HTML plus cached image files.
4. Replace the repo README.

Item 3 changes no URL and no content, so it is safe on the current site.

**Need from you:** A or B, and the domain name if A.

---

## 3. Target structure: 22 URLs

| URL | Source in `index.html` | Anchor links that point here |
|---|---|---|
| `/` | `header.hero`, ticker, teasers | |
| `/about/` | `#summary` (`data-page="about"`) | |
| `/services/` | `#services` intro and 4 cards | `#services` |
| `/services/demand-generation/` | card 01 `details.sdeep` | |
| `/services/account-based-marketing/` | card 02 `details.sdeep` | |
| `/services/martech/` | card 03 `details.sdeep` | |
| `/services/interim-cmo/` | card 04 `details.sdeep` | |
| `/case-studies/` | `#work`, `#skills`, `#filters`, 64 static cards | `#work` |
| `/case-studies/{slug}/` x 6 | `details.fcase`: Hays, Randstad, KSeye, M&S, Cigna, We Are Aspire | |
| `/fractional-cmo/` | `#model` + `#approach` + `#faq` | `#model`, `#faq` becomes `/fractional-cmo/#faq` |
| `/rates/` | `#rates` | `#rates` |
| `/insights/` | `#insights` intro | `#insights` |
| `/insights/{slug}/` x 4 | `mql-wrong-unit`, `abm-year-one`, `brief-not-agency`, `ai-what-worked` | |
| `/privacy/` | `privacy.html` | footer link |

Count: 1 + 1 + 1 + 4 + 1 + 6 + 1 + 1 + 1 + 4 + 1 = **22**.

Two things the old map missed:
- `#skills` and `#filters` sit inside `#work` and belong on `/case-studies/`. The JS in `toggleFilter()` scrolls to `#filters`, so it must stay on the same page as the chips.
- `#approach` has no inbound anchor of its own. Merge it into `/fractional-cmo/` after `#model` and keep the id.

**Target keywords** are unchanged from `PAGE_SPLIT_MAP.md`. Add `/privacy/` with no target keyword and let it be indexed.

---

## 4. Steps, in order

| Step | Action | Safe before domain? | Check |
|---|---|---|---|
| 0 | Send the missing files (row 12 above). Confirm A or B | Yes | |
| 1 | Update `sitemap.xml` (add `privacy.html`, new `lastmod`). Move `robots.txt`. Replace repo `README.md` | Yes | `curl` both files after deploy |
| 2 | Extract 20 images to `/assets/` with real extensions. Resize logos to 192px max. Replace hydration JS with `<img src width height loading="lazy" alt>` written by the card generator. Point nav logo and 3 icons at files | Yes | 0 `data:image` left in HTML. HTML under 0.3MB. Logos render. Filter still changes count |
| 3 | Buy domain, `CNAME`, DNS, verify Search Console and Bing | Option A only | Site live on new domain over HTTPS |
| 4 | Wait 4 to 6 weeks (Option A) | | Indexed pages and impressions recorded as baseline |
| 5 | Jekyll scaffold. `baseurl` is `""` on a custom domain, `/portfolio` on github.io. **Check first** | | Every link and asset resolves |
| 6 | Split by `data-page`. Content is already written | | 22 pages build |
| 7 | Convert the 9 anchors. Replace the 13 hard-coded base URLs | | `grep 'href="#'` and `grep github.io` return nothing |
| 8 | Per-page `<title>`, description, self-referencing canonical, OG tags | | No two titles match |
| 9 | Schema: `Person` and `ProfessionalService` in layout. `FAQPage` on `/fractional-cmo/` only. Optional `Article` on the 4 insights | | JSON-LD parses. FAQ schema matches visible FAQ (12) |
| 10 | Sitemap with 22 URLs. Submit to both consoles | | |
| 11 | GitHub Actions: spreadsheet push rebuilds pages | | Needs the missing scripts. Fails the build if rules 1 to 5 below break |

---

## 5. Rules that must hold (updated)

1. **64 case cards stay pre-rendered** between `CASE-CARDS:START` and `CASE-CARDS:END`. `CASES` is the source. No client-side rendering.
2. **No duplicated image data.** After Step 2 this becomes: no base64 images anywhere.
3. **`FAQPage` schema on `/fractional-cmo/` only.** 12 questions, matching the visible text.
4. **No invented metrics.** Modelled pipeline figures (Hays £6.0m, Randstad £2.5m, KSeye £1.6m) stay off the site.
5. **Build by script, not by hand.** No retyping large blocks.
6. **Privacy policy must stay true.** It says no cookies, no analytics, no forms, no browser storage. Each of these breaks it:
   - Search Console and Bing verification via meta tag or file: safe.
   - Any analytics tag (GA4, Clarity, Plausible, pixels): update the policy in the same commit. Cookie-based tools also need a banner.
   - The B2B Marketing Diagnostic lead magnet: adds a form, a lawful basis and a processor. Update section 2 and section 5.
7. **Verify before delivering.** Balanced tags, 64 cards, no `data:image`, JSON-LD parses, headless render with zero errors, filter changes the count, 22 pages in the sitemap.

---

## 6. Gaps in the growth plan itself

The split improves ranking potential. It does not measure or convert.

| # | Gap | Why it matters | Pros of fixing | Cons | Next step |
|---|---|---|---|---|---|
| 1 | **No analytics** | After the split you cannot tell which of 22 pages earns calls | Real data for the 4 to 6 week review | Policy and possibly a banner change | Pick a cookieless tool. Update the policy in the same commit |
| 2 | **No lead magnet** | Most visitors are not ready for a call | Captures the middle of the funnel | Adds a form, a processor and policy changes | Build after the split, not during |
| 3 | **MasterCV §4.2 lacks the 33% KSeye figure** | Site holds a claim the master cannot back | Closes the only known traceability gap | None | Add the line to `MasterCV.md` |
| 4 | **GTM tag at 3 cards** | Thinnest filter chip | Stronger chip if GTM is a priority | Needs verified new content | Ask whether GTM is a priority before adding |
| 5 | **Family Brands has no logo** | One mono badge among 16 logos | Visual consistency | Needs the file | Ask for it |
| 6 | **Card verification** | Not every card checked line by line against MasterCV | Lower claim risk | Time | Run the `Verified vs Master CV` column once the new MasterCV arrives |
| 7 | **Rates unconfirmed** | £750 day, £3,250 / £6,000 / £4,500 are live | Avoids walking back a public price | None | Confirm before the `/rates/` page targets "fractional cmo cost uk" |

---

## 7. What I need from you

1. **A or B** from section 2, and the domain name if A.
2. The audit and growth plan chat, if it holds anything beyond the brief.
3. `MasterCV.md`, `Portfolio_Case_Study_Review_v5.xlsx`, `retag.py`, `apply_retag.py`, `make_v5.py`.
4. Which analytics tool, if any.
5. Whether I should start with Steps 1 and 2 now. They are safe on the current site and need no other decision.
