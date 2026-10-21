# Site Plans: employer portfolio and gtmdemand.com

**Status date:** 20 October 2026
**Replaces:** the earlier single plan, `NEW_CHAT_BRIEF.md` and `PAGE_SPLIT_MAP.md` where they conflict.

**Direction (agreed 20 October 2026):**

| Site | Home | Audience | Job |
|---|---|---|---|
| **A. Portfolio** | `ubhudia-portfolio.pages.dev` (Cloudflare Pages from this private repo) | Employers and recruiters, by link | Proof of work for job applications |
| **B. Agency site** | `gtmdemand.com` (purchased) | Buyers of fractional CMO and agency services | Case studies, services, rates, inbound enquiries |

The original audit and growth plan chat was not in the upload. Both plans are built from the brief, the split map, the live `index.html` and the decisions in this session.

---

## Decisions and findings (20 October 2026)

| # | Item | Outcome |
|---|---|---|
| 1 | Voice for the agency site | **"We"**. Say associates and partners are drawn in as needed, and do not claim a fixed team size |
| 2 | Past employer logos on the agency site | **Text only.** Names in copy with "worked at" framing, no logos |
| 3 | Case study depth on the agency site | **Less.** Headline, results table and one paragraph public. Problem, method and "what transfers" on the call |
| 4 | Agency repo | `ubhudia/gtmdemand` (private, empty, created 20 Oct) |
| 5 | MasterCV and the rebuild files | Rebuilt from the MasterCV Claude Doc (rev 408). `tools/build_v6.py` and `tools/Portfolio_Case_Study_Review_v6.xlsx` are in this repo, excluded from Pages. Pending your review |
| 6 | **`ubhudia.github.io/portfolio/` returns 404** | **Cause: the repo is private.** `ubhudia/portfolio` has `has_pages: false` and its last Pages build was on 2 September, before it was made private. GitHub Pages on a private repo needs a paid plan, so Pages switched off. `gtmdemand` is private too and will hit the same limit |
| 7 | **Hosting decision (21 Oct 2026)** | **Portfolio on Cloudflare Pages** (free, repo stays private). The agency site `gtmdemand.com` is a separate project with its own repo and a different purpose: case studies, services, fractional and agency enquiries |
| 8 | Portfolio URL | `https://ubhudia-portfolio.pages.dev/`. `index.html` and `privacy.html` already point to it. The old `ubhudia.github.io/portfolio` address stays dead, so update any CV, LinkedIn or application link |

### Fix for the 404 (options considered; Cloudflare Pages chosen)

| Option | Pros | Cons | Cost |
|---|---|---|---|
| **A. GitHub Pro** | Keeps private repos, same URL, no migration. Covers `gtmdemand` too | Pages from a private repo is still a public site | About $4 a month (check current price) |
| B. Make `portfolio` public again | Free, restores the site | Source, README and full history become public, including old README text with modelled £ figures | Free |
| C. Host on Cloudflare Pages or Netlify from the private repo | Free, repo stays private, can add a password | New URL and setup. GitHub Pages `CNAME` for `gtmdemand.com` moves too | Free |

Recommendation: **A**. One upgrade fixes both sites and keeps the repos private.

---

# Plan A: Employer portfolio

## A1. State after this change

| # | Item | State |
|---|---|---|
| 1 | Search visibility | `noindex, nofollow` on `index.html` and `privacy.html` |
| 2 | `sitemap.xml`, `robots.txt` | Removed. A sitemap of noindex pages contradicts itself, and `robots.txt` inside `/portfolio/` was never read |
| 3 | Working files | `_config.yml` excludes README, planning notes, scripts, spreadsheets and MasterCV from Pages |
| 4 | Footer | "© 2026 Umesh Bhudia" on both pages |
| 5 | Content | Unchanged: 64 cards, 16 tags, 6 featured studies, 12-question FAQ, cal.com 30 minute booking |

**noindex hides the site from search only.** Anyone with the link can open it. To restrict it to people with a password, use Cloudflare Access or a Netlify password.

Do not add a `robots.txt` Disallow. Crawlers must fetch the page to see the noindex.

## A2. To do

| # | Task | Owner | Effort |
|---|---|---|---|
| 0 | **Create the Cloudflare Pages project** (steps in the README). Until then the site is offline | You | Low |
| 1 | Merge `claude/busy-hawking-rik0mj` into `main` so noindex and the exclude list go live. On GitHub: open the repo, click "Compare & pull request" on the banner for that branch, click "Create pull request", then "Merge pull request" | You | Low |
| 2 | After deploy, open `/portfolio/README.md` in a browser. It should 404. If it loads, the exclude list is not applied and the modelled £ figures are public | You | Low |
| 3 | Save the live URL to the Wayback Machine: `https://web.archive.org/save/https://ubhudia.github.io/portfolio/` (dated proof you wrote it first). Repeat after big updates | You | Low |
| 4 | Google may take weeks to drop existing listings. For a faster removal, use Search Console's Removals tool, or check `site:ubhudia.github.io/portfolio` | You | Low |
| 5 | Decide on the cut-down from the old action 4 (thin the featured studies): **dropped for this site**, since employers want the detail | Done | |

## A3. What no longer applies to this site

The Jekyll split, 22 URLs, per-page SEO, FAQ schema placement and the sitemap move to Plan B. The FAQ and schema on this site are harmless and can stay.

## A4. Keep true

1. No invented metrics. Every figure traces to MasterCV.
2. Modelled pipeline figures (Hays £6.0m, Randstad £2.5m, KSeye £1.6m) stay off the site and out of any served file.
3. Privacy policy claims (no cookies, no analytics, no forms) must stay true.
4. The 64 cards stay static HTML generated from `CASES`. Logos are hydrated from `LOGO_MAP` once.

---

# Plan B: gtmdemand.com agency site

## B1. Decisions to make first

| # | Decision | Options | Recommendation |
|---|---|---|---|
| 1 | **Voice** | "I" (solo practitioner) or "we" (agency) | Match reality. If it is you alone, use "I" and say associates are drawn in as needed. Claiming a team that does not exist is a trust and legal risk |
| 2 | **Where your name sits** | Prominent, or small in About | Prominent. Buyers hire the person, and "umesh bhudia" stays the brand search term |
| 3 | **Repo** | New repo for `gtmdemand.com`, or a branch of this one | New repo. Keeps A noindex and B indexed with no cross-contamination |
| 4 | **Case study depth** | Full detail, or headline + results table + one paragraph with the rest on the call | Open. Full detail ranks better. Cut-down reduces copyable method text. Your call at build time |
| 5 | **Client logos** | Show past employer logos, or names only | Check. Hays, Randstad, M&S, Cigna and others as public logos can imply endorsement of an agency. Names in text with "worked at" framing is safer |
| 6 | **Positioning terms** | Does "GTM Demand" lead with go-to-market, demand generation, or both | Both. "GTM" is the thinnest tag on this site (3 cards), so the wording needs support from the demand and growth cards |

## B2. Structure: 22 URLs as a starting point

Revise after decision 1 and 6. The current content maps as follows.

| URL | Source in `index.html` |
|---|---|
| `/` | Hero, ticker, teasers |
| `/about/` | `#summary` |
| `/services/` + 4 children (demand generation, ABM, MarTech, interim CMO) | `#services`, 4 `details.sdeep` |
| `/case-studies/` + 6 children | `#work`, `#skills`, `#filters`, 64 cards, 6 `details.fcase` |
| `/fractional-cmo/` | `#model`, `#approach`, `#faq` |
| `/rates/` | `#rates` |
| `/insights/` + 4 children | `#insights`, 4 `details.insight` |
| `/privacy/` | `privacy.html` |

Count: 1 + 1 + 5 + 7 + 1 + 1 + 5 + 1 = **22**.

Anchor conversions (9 links, 6 distinct): `#rates` x3, `#work` x2, `#services`, `#model`, `#insights`, `#faq` (becomes `/fractional-cmo/#faq`). `#filters` stays on `/case-studies/`.

Target keywords from `PAGE_SPLIT_MAP.md` stay, with "gtm demand" and "go-to-market" added to `/` and `/services/`.

## B3. Steps

| Step | Action | Check |
|---|---|---|
| 1 | Repo `ubhudia/gtmdemand` exists (private). Host on **Cloudflare Pages** too, so the repo stays private without a GitHub plan. Add `gtmdemand.com` to Cloudflare (free plan), change the nameservers at the registrar to the two Cloudflare gives you, then add the domain under the Pages project's Custom domains | Site loads on HTTPS at `gtmdemand.com` |
| 2 | Extract the 20 base64 images to `/assets/` with real extensions (12 PNG, 2 JPEG, 1 WebP, plus nav logo and 3 icons). Resize logos to 192px max long edge | No `data:image` in HTML. HTML under 0.3MB |
| 3 | Jekyll scaffold: `_config.yml` with `url: https://gtmdemand.com` and `baseurl: ""`, layouts, header and footer includes, 6 nav links. Cloudflare Pages preset for Jekyll: build command `jekyll build`, output directory `_site` | Every link and asset resolves |
| 4 | Split by `data-page`. Keep 64 cards pre-rendered | 22 pages build |
| 5 | Convert 9 anchors. Replace the 13 hard-coded `ubhudia.github.io/portfolio` URLs with `{{ site.url }}{{ page.url }}` | `grep 'href="#'` and `grep github.io` return nothing |
| 6 | Per-page `<title>`, description, self-referencing canonical, Open Graph | No two titles match |
| 7 | Schema: `Person` and `ProfessionalService` in the layout (update `ProfessionalService` name and URL for GTM Demand). `FAQPage` on `/fractional-cmo/` only, 12 questions matching the visible text | JSON-LD parses |
| 8 | Add `robots.txt` at the domain root and a 22-URL `sitemap.xml`. Verify in Search Console and Bing Webmaster Tools, submit the sitemap | Both consoles show the sitemap read |
| 9 | Add analytics only with a matching privacy policy change (see B4) | Policy and tag go in the same commit |
| 10 | GitHub Actions: rebuild when `Portfolio_Case_Study_Review_v*.xlsx` changes | Needs `retag.py`, `apply_retag.py`, `make_v5.py` and the spreadsheet |
| 11 | Mailbox on the domain (for example `umesh@gtmdemand.com`) | Add SPF, DKIM and DMARC records |

Duplicate content is not a risk between the sites, because Plan A is noindex.

## B4. Rules

1. 64 cards stay pre-rendered. Never client-side render.
2. No base64 images after Step 2.
3. `FAQPage` schema on one page only.
4. No invented metrics. Modelled figures stay off the site.
5. Build by script, not by hand.
6. **Privacy policy must stay true.** Analytics, a request form for gated case studies, or the diagnostic lead magnet each add a processor and a lawful basis. Update sections 1, 2 and 5 in the same commit.
7. Verify before delivering: balanced tags, 64 cards, no `data:image`, JSON-LD parses, headless render with zero errors, filter changes the count, 22 pages in the sitemap.

## B5. Open items

| # | Item | Next step |
|---|---|---|
| 1 | MasterCV §4.2 lacks the 33% KSeye figure | Add the line to `MasterCV.md` |
| 2 | Missing files for the rebuild: `MasterCV.md`, v5 spreadsheet, `retag.py`, `apply_retag.py`, `make_v5.py` | Send them |
| 3 | No analytics | Choose a cookieless tool and update the policy |
| 4 | No lead magnet | Build after launch; adds a form |
| 5 | Rates (£750 day, £3,250 / £6,000 / £4,500) live on the current page | Confirm before `/rates/` goes public as an agency price list |
| 6 | Family Brands has no logo | Send the file, or leave the mono badge |
| 7 | Wording on shared work | Check the case studies say what you personally led |

## B6. Protecting wording on the agency site

The repo is private and the site will be public. Public text can be copied by anyone, so use detection rather than hiding:

1. Add one short, true, unusual sentence to each featured case study, with no new claims. Set Google Alerts on those phrases.
2. Save each release to the Wayback Machine and keep the copyright line with the year.
3. Optionally block AI training bots (GPTBot, CCBot, Google-Extended) in `robots.txt`. Keep search and answer-engine bots allowed.
4. Decision B1.4 covers whether to thin the featured studies.
5. Do not disable right-click or obfuscate the HTML. It does not stop copying and hurts accessibility.

## B7. What I need from you

1. Answers to B1.1 to B1.6.
2. The missing files in B5.2.
3. Whether to use a new repo for B (recommended).
