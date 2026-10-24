# amazon reviews scraper: How to Pull Amazon Review Data Without Getting Blocked, Plus Real Pricing for Every Plan

An amazon reviews scraper in 2026 has one job: get review text, star ratings, dates, and reviewer details off Amazon product pages without your requests dying on a CAPTCHA page. Two routes exist. You can run your own scraper with proxies and a headless browser, or you can call a scraping API that handles the blocking for you. This guide covers both — including why the DIY route got noticeably harder this year, what ScraperAPI's structured Amazon endpoint actually returns, and what each of its seven plans costs once you factor in how credits are billed.

> **Quick picks**
> - Just testing the waters? The **7-day free trial includes 5,000 API credits with no credit card required**. That's 1,000 Amazon product-page requests at Amazon's 5-credit rate — enough to judge the data quality before paying anything. [👉 Start the free trial](https://www.scraperapi.com/?fp_ref=coupons)
> - Running a small-to-medium monitoring job? **Startup at $149/month (1,000,000 credits)** or **Business at $299/month (3,000,000 credits, country-level geotargeting)** cover most review-scraping workloads.
> - Need reviews from specific countries outside the US and EU? You need Business or above — Hobby and Startup only geotarget US & EU.

## Why most DIY amazon review scrapers broke recently

The easy paths are mostly gone. Amazon retired its public review API, and in May 2026 it changed how reviews are served: pulling them out of the raw product-page HTML stopped being reliable, and the standalone reviews URL now returns a not-found page to logged-out visitors.

Even before that change, scraping reviews meant solving three problems at once. As the ScrapingBee engineering team put it: "The hard part is not parsing the HTML. It is loading the lazy review widget reliably in a headless browser, rotating IPs so Amazon does not block you, and matching the right regional storefront."

The lazy-loading part trips up a lot of homegrown scripts. Amazon's review widget doesn't enter the DOM until the page scrolls, so a scraper that fetches and parses immediately sees an empty review section. The open-source scripts floating around GitHub work around this with scrolling logic, but they come with hard caps baked in: one popular implementation supports up to 10 pages of reviews per ASIN, and another extracts at most 100 reviews per star rating — around 500 total — because they rely on Amazon's public review pagination, which itself is limited.

That's fine for pulling the top reviews of one product. It breaks down when you want complete review history for sentiment analysis, brand monitoring, or training data.

## How ScraperAPI's Amazon endpoint handles reviews

ScraperAPI takes a different approach from selector-based scrapers: it offers a structured Amazon Product API where one GET request returns the whole product page as JSON — and that response "also returns all publicly available reviews for an Amazon product page (ASIN)," per the official documentation.

One caveat worth knowing upfront: the `reviews` array depends on what's publicly available for that particular ASIN. In ScraperAPI's own sample response, the reviews field comes back empty for the demo product, so treat the field as "whatever Amazon exposes on the product page" rather than a guaranteed full archive for every listing.

The request looks like this:


GET https://api.scraperapi.com/structured/amazon/product?api_key=API_KEY&asin=ASIN&country_code=COUNTRY_CODE&tld=TLD


What you get back in one call:

- Product name, brand, full description, feature bullets, images, product category
- Detailed product information: dimensions, model number, best sellers rank, date first available
- `ships_from` and `sold_by` data
- `aplus_present` (whether the listing has A+ content)
- `is_coupon_exists` — a boolean telling you whether the listing currently has a coupon, which is handy if you're tracking competitor promotions alongside reviews
- The `reviews` array with the publicly available reviews for that ASIN

Three parameters matter for review scraping specifically:

1. **ASIN** (required) — the product identifier from the Amazon URL, e.g. `B07FTKQ97Q`. Note that ASINs are marketplace-specific.
2. **TLD** — which Amazon marketplace to scrape. There are 21 supported: com, co.uk, ca, de, es, fr, ie, it, co.jp, co.za, in, cn, com.sg, com.mx, ae, com.br, nl, com.au, com.tr, sa, se, and pl.
3. **COUNTRY_CODE** — a two-letter geo-targeting code. If you need to scrape one marketplace from another country (say, amazon.com from Canada to capture Canadian shipping info), you must set both TLD and COUNTRY_CODE.

Output defaults to JSON; you can pass `output_format=csv` if you'd rather load results straight into a spreadsheet.

The practical difference versus a DIY scraper: no CSS selectors to maintain, no scroll scripts, no proxy pool. Amazon's DOM changes stop breaking your pipeline because the parsing happens on ScraperAPI's side.

## ScraperAPI plans and pricing (full table)

Every paid plan includes the same feature set — JS rendering, premium residential and mobile IPs, structured data APIs, DataPipeline, CAPTCHA handling. What changes is volume, concurrency, and geotargeting depth.

| Plan | Monthly price | Annual (per month) | API credits | Concurrent threads | Buy |
| --- | --- | --- | --- | --- | --- |
| Hobby | $49 | $44.10 | 100,000 | 20 | [ Get Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | $149 | $134.10 | 1,000,000 | 50 | [ Get Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | $299 | $269.10 | 3,000,000 | 100 | [ Get Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | $475 | $427.50 | 5,000,000 | 200 | [ Get Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | $975 | $877.50 | 10,500,000 | 300 | [ Get Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | $1,975 | $1,777.50 | 21,500,000 | 500 | [ Get Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | Custom | Custom | 22,000,000+ | 500+ | [ Talk to sales](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

Geotargeting and analytics differ by tier: **Hobby and Startup are limited to US & EU geotargeting and only 30 days of analytics dashboard history**. Business and above get country-level geotargeting and unlimited analytics history. Those two restrictions are easy to miss and they're the main reason to skip the cheaper tiers if your review monitoring spans marketplaces in Asia or South America.

Annual billing saves 10% across the board. There's also a free plan with 1,000 credits (capped at 5 concurrent connections) that you can top up by contacting support if you need more test credits.

## The real cost: credits, not requests

Here's the part most pricing comparisons gloss over. ScraperAPI bills in credits, and not all pages cost the same: a standard page costs 1 credit, but **Amazon costs 5 credits per request**. Sites behind Cloudflare, Datadome, or PerimeterX protection add 10 credits per request when the bypass succeeds.

So the actual throughput math for review scraping looks like this (based on monthly pricing; annual billing drops each figure by another 10%):

| Plan | Credits | ÷ 5 per Amazon request | Amazon pages/month | Effective cost per Amazon request |
| --- | --- | --- | --- | --- |
| Hobby | 100,000 | ~20,000 | ~20,000 | $0.00245 |
| Startup | 1,000,000 | ~200,000 | ~200,000 | $0.000745 |
| Business | 3,000,000 | ~600,000 | ~600,000 | ~$0.0005 |
| Scaling | 5,000,000 | ~1,000,000 | ~1,000,000 | ~$0.000475 |

Two things worth knowing before you commit to a tier:

- **Running out of credits** pauses scraping by default until renewal. On Hobby through Professional you can enable auto-upgrade, which bumps you to the next plan automatically when credits run out. On Scaling and above, you can instead use pay-as-you-go at a fixed per-credit rate with a monthly cap you set. Hobby, Startup, and Business have no PAYG option — you either upgrade or wait. Annual plans can also renew early for a fresh batch of credits if you burn through yours ahead of schedule.
- **Exceeding concurrency** doesn't cost anything: the API returns a 429 and the request is turned away rather than queued, so you retry within your thread limit.

You can check the exact credit cost of any URL with the Domain Cost Estimator in the dashboard, and set a `max_cost` per request so a single scrape can't burn through your balance.

## How it compares to the alternatives

If you're shopping for an amazon reviews scraper, you're likely weighing three categories. Here's the verified picture, information only:

| Option | How you get reviews | Cost structure | Notes |
| --- | --- | --- | --- |
| **ScraperAPI** | Structured Amazon API returns reviews in one call, no selectors needed | Credit-based; Amazon = 5 credits/request; free trial 5,000 credits / 7 days | 21 marketplaces, geo-targeting, CSV or JSON output |
| **ScrapingBee** | API renders the page; you write CSS/XPath extraction rules and a js_scenario to scroll the lazy review widget | Credit-based; 1,000 free credits | Requires Python 3.8+, you maintain the selectors yourself |
| **Apify actor (Amazon Reviews Scraper Pro)** | Pre-built actor with star-rating filters and sorting | Pay-per-run; examples: 100 reviews ≈ $0.25, 5,000 reviews ≈ $12.50 | 19 Amazon domains, batch up to 500 products per run |
| **Open-source GitHub scripts** | You run everything: proxies, browser, parsing | Free, but you pay in proxy bills and maintenance | Hard caps: ~10 review pages per ASIN or ~500 reviews; breaks when Amazon changes the DOM |

Fair read of the trade-offs: the Apify actor is cheaper per review for small, one-off jobs. ScrapingBee gives you more extraction flexibility if you enjoy writing selectors. ScraperAPI's edge is that reviews arrive bundled with full product context — pricing data, BSR, coupon status, seller info — in a single structured response, which matters when the reviews are one input to a larger pricing or competitor-tracking pipeline rather than the whole job.

## Who shouldn't pay for this

A structured scraping API is overkill in a few cases, and it's cheaper to say so upfront:

- **You need the top 10–20 reviews of one product, once.** A free GitHub script or even copy-paste covers that. Don't buy a plan for this.
- **Your volume is tiny and recurring.** If you're pulling a handful of ASINs weekly, the free plan's 1,000 credits (200 Amazon requests) plus trial credits might sustain you for a while.
- **You need reviewer profile scraping or review images at scale.** ScraperAPI's Amazon endpoint returns the reviews attached to a product page; the Apify actor's feature set (reviewer profile URLs, review image downloads) goes deeper on review-specific fields.

## FAQ

**How many reviews can I pull per product?**
The endpoint returns all publicly available reviews for the ASIN in the product response. There's no published per-ASIN cap on the structured API itself — the ~500-review ceilings you see elsewhere belong to scripts that rely on Amazon's public pagination. Keep in mind the field only reflects what Amazon currently exposes publicly on that listing.

**Will I get blocked?**
Blocking is handled as part of the base product on every plan: rotating proxy pools, CAPTCHA and anti-bot detection, custom session and header support, and automatic retries are all included. For Amazon specifically, each request draws 5 credits, which covers that infrastructure.

**Does geotargeting change the reviews I see?**
Yes. Amazon shows different review content by country, so a request through a US IP and one through a German IP can return different reviews for the same ASIN. Set `country_code` to match the market whose reviews you actually want, and remember Hobby/Startup geotargeting is limited to US & EU.

**Can I get data back as CSV?**
Yes — pass `output_format=csv` on structured data endpoints. JSON is the default.

**What happens when I hit my limits?**
Two different limits, two different behaviors. Go over your concurrent thread count and the API returns a 429 — the request is refused, not queued, and there's no extra charge. Run out of credits and scraping pauses until renewal unless you've enabled auto-upgrade (Hobby through Professional) or pay-as-you-go (Scaling and above).

## Before you pick a plan

Match the tier to what you're actually scraping:

- **Evaluating or building a prototype** → start with the free trial (5,000 credits, 7 days, no card). One thousand Amazon product requests tells you everything you need about data quality. [👉 Claim the free trial](https://www.scraperapi.com/?fp_ref=coupons)
- **One marketplace, US or EU, under ~200K Amazon pages a month** → Startup at $149/month is the fit; Hobby's 20,000-request ceiling runs out fast on any recurring schedule.
- **Multi-country review monitoring or seller tools in production** → Business at $299/month, because country-level geotargeting and unlimited analytics history only start there.
- **Millions of requests with predictable spend** → Scaling or Professional, where pay-as-you-go keeps you running past your credit limit instead of pausing mid-job.

One last check before checkout: confirm your target marketplaces against the 21 supported TLDs, and if you're on monthly billing, decide whether auto-upgrade or PAYG fits your workflow — turning the wrong one on (or leaving both off) is the most common way people end up either overspending or with a paused scraper mid-project. [👉 Compare all plans and current annual pricing](https://www.scraperapi.com/pricing/?fp_ref=coupons)
