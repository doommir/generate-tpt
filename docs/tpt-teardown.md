# Teachers Pay Teachers (TPT): Reverse-Engineering Teardown

Snapshot date: 2026-10-30. Target: https://www.teacherspayteachers.com/

## How to read this

The cloud session that produced this file could not reach `teacherspayteachers.com`, its help center, or its seller blog. The network policy blocked them. Every claim below comes from web search results, and each one has a confidence tag:

- **[confirmed]**: TPT's own help center, its seller blog, or two or more independent reports agree.
- **[reported]**: one credible outlet (Chalkbeat, K-12 Dive, EdWeek, a vendor case study) states it.
- **[unverified]**: seller blogs, third-party tools, or details I remember from the live site. Check these against the live site before you build on them.

Section 9 lists what to re-check once the hosts are on the allowlist.

---

## 1. Snapshot

| Fact | Value | Confidence |
|---|---|---|
| Owner | IXL Learning, acquired March 2023, price undisclosed | [confirmed] |
| Catalog | 7M+ free and paid resources | [reported] (IXL press release, 2023) |
| Users | 7M+ educators, cited as 85% of US PK-12 teachers | [reported] (company figure) |
| Downloads | 1B+ all-time | [reported] |
| Seller outcomes | 300+ sellers past $1M lifetime as of 2022. The average seller earns "low single-digit thousands" | [reported] (K-12 Dive, CEO interview) |
| On-site search share | About 50% of visitors use search | [reported] (Algolia case study) |

## 2. Business model

TPT runs a two-sided marketplace. Teacher-authors upload digital resources, and TPT takes a commission on each sale. Revenue streams:

1. **Commission on sales.** TPT keeps the share that the payout rate leaves (see below).
2. **Seller membership fees.**
3. **Per-resource transaction fees** on lower-tier sellers.
4. **TPT School Access**, an annual school- or district-wide subscription that pays out to sellers when teachers use their resources. [confirmed]
5. **Easel**, TPT's interactive layer: Easel Activities (digital overlays on PDFs) and Easel Assessments. It doubles as a lightweight LMS. [confirmed]

### Seller tiers (changing now)

TPT announced a restructure in October 2026. Premium Seller is going away, and Exclusive Seller replaces it.

| Tier | Fee | Payout | Transaction fee | Constraint | Confidence |
|---|---|---|---|---|---|
| **Basic** | Free historically. One third-party site now lists $29 one-time | 55% | $0.30 per resource sold | None | Payout and fee [confirmed]. $29 [unverified] |
| **Exclusive** (new) | $59.95/year | 80% until 2027-01-01, then 77.5% | Not published in the sources I found | Sell your digital educational resources **only on TPT** | [confirmed] |
| **Premium** (retired) | $59.95/year | 80% | $0.15 on orders under $3 | None | Closed to new sign-ups 2026-10-01 [confirmed] |

Key dates [confirmed]:
- **2026-10-01**: Premium closes to new sign-ups.
- **2026-10-08**: Seller Town Hall announces the changes and the AI ban.
- **2026-12-31, 11:59pm ET**: existing Premium sellers must opt into Exclusive. Anyone who doesn't drops to Basic on 2027-01-01.
- TPT refunds unused Premium time at $5 per remaining month.

#### Exclusive vs. Basic breakeven

Annual net under each tier, with `S` = gross sales and `n` = units sold:

```
Exclusive = 0.775 * S - 59.95
Basic     = 0.55  * S - 0.30 * n
Exclusive - Basic = 0.225 * S + 0.30 * n - 59.95
```

At a $5 average price you pick up $1.425 per unit on Exclusive, so Exclusive wins past **about 42 units, or $210 gross, a year**. At $3 the breakeven is 62 units. Past that point the payout math always favors Exclusive. The real cost is exclusivity: you give up selling the same digital resources on Tes, Teach Simple, Etsy, Payhip, or your own store. This model leaves out the unconfirmed $29 Basic fee and any Exclusive transaction fee.

## 3. Content policy: the AI ban

This finding matters most for a repo named `generate-tpt`.

- **Announcement:** TPT's 2026-10-08 Seller Town Hall announced a ban on AI-generated resources. Chalkbeat reported it on 2026-10-15. A TPT spokesperson said resources "should be unique, expertly crafted, and AI-free." [confirmed]
- **Scope:** the resource itself, **covers, previews, and listings**. [reported]
- **Trigger:** Chalkbeat's August 2026 investigation found AI-generated resources on TPT with obvious errors ("an alphabet without an F"). [confirmed]
- **Enforcement:** TPT says it is building tools to detect and remove AI-generated resources, plus a buyer-facing option to report low-quality AI resources. [reported]
- **Interim guidance:** sellers should start replacing AI-generated material now and not wait for the final guidelines. [reported]
- **Still unknown:** the effective date, the exact policy text, how TPT draws the line (AI spell-check? AI clip art? AI-drafted then rewritten?), and how it handles existing listings. A Change.org petition calls it retroactive. TPT has not confirmed that.
- **Before this:** a 2025 policy reportedly required sellers to disclose "substantially AI-generated" resources. [unverified]

## 4. Information architecture and URL patterns

| Page type | Pattern | Example | Confidence |
|---|---|---|---|
| Product | `/Product/{Title-Slug}-{numericId}` | `/Product/Edmark-Read-and-Graph-9079464` | [confirmed] |
| Store | `/store/{store-slug}` | | [confirmed] |
| Legacy browse: type | `/Browse/Type-of-Resource/{Name}` | `/Browse/Type-of-Resource/Lesson-Plans-Bundled` | [unverified] (2015 link) |
| Legacy browse: price | `/Browse/Price-Range/{Name}` | `/Browse/Price-Range/Free` | [unverified] (2015 link) |
| Legacy search | `/Browse/Search:{term}` | | [unverified] |

The numeric ID at the end of the product slug serves as the stable key. TPT can rename the slug without breaking the ID.

**Browse menu facets** (from a urlscan capture of the homepage) [reported]:
- **Grade bands:** Pre-K–K, 1–2, 3–5, 6–8, 9–12, Other
- **Subjects:** ELA, Math, Science, Social Studies, Arts, World Languages, Specialty, and more
- **Formats:** Easel by TPT, Google Apps, Microsoft, PDF, Image, Video
- **Header products:** TPT School Access, Easel Activities, Easel Assessments

TPT is moving filters out of "Resource Type" and into a separate **Formats** filter bar at the top of browse pages. Older resource-type lists still appear alongside the new bar. [reported]

## 5. Listing anatomy

A buyer sees the parts in this order. Sellers report that most purchase decisions happen on the cover and the preview.

| Field | Notes | Confidence |
|---|---|---|
| Cover image | Thumbnail #1. Shows in search, the store grid, and recommendations | [confirmed] |
| Thumbnails 2–4 | Up to 3 more. They appear only on the product page | [reported] |
| Recommended thumbnail size | 1224 × 1584 px (US Letter at 144 dpi) | [unverified] |
| Preview file | A PDF or images buyers open before purchase. JPG/PNG up to 10 MB each | [unverified] |
| Title | 80-character max | [unverified] |
| Description | The first 1–2 sentences show in search results and on the store profile | [reported] |
| Tags/keywords | About 20 tags, ≤50 characters each | [unverified] |
| Grade levels | Per-grade multi-select (PreK…12th, Higher Ed, Adult Ed, Homeschool, Staff, Not Grade Specific) | [unverified] (from memory of the live site) |
| Subjects | Multi-select from TPT's subject tree | [confirmed] |
| Resource type | Worksheets, Activities, Lesson Plans, Task Cards, Centers, Assessments, and so on | [confirmed] |
| Formats included | PDF, Google Apps, Easel, Microsoft, and others | [confirmed] |
| Pages | Integer | [confirmed] |
| Answer key | Included / Not included / N/A | [confirmed] |
| Standards | CCSS, TEKS, NGSS, and other alignments | [reported] |
| Price | Free or paid. Bundles carry their own price | [confirmed] |

## 6. Search and ranking

- **Engine:** Algolia powers marketplace search, reviews, and seller stores. TPT credits Algolia with a +2% conversion lift from relevance tuning. [reported] (vendor case study)
- **Ranking factors:** TPT does not publish them. Sellers widely report these:
  1. Text relevance on title, tags, and the opening of the description
  2. Sales velocity and earnings. TPT's search exists to drive sales
  3. Conversion rate: views to purchases
  4. Listing completeness. Sellers suspect blank fields cost rank
  5. Ratings and review count
- **Keyword research:** pull from TPT's search-bar autocomplete. It reflects the words teachers type. [reported]
- **Platform signal:** a 2026 seller post describes algorithm changes. [unverified]

## 7. Tech stack (from job postings)

| Layer | Evidence | Confidence |
|---|---|---|
| Frontend | React, TypeScript, server-side rendering, CDN | [reported] |
| API | GraphQL with Apollo | [reported] |
| Services | Node.js, Elixir, service-oriented architecture, Serverless | [reported] |
| Auth | OAuth | [reported] |
| Infra | Kubernetes, Docker, Terraform, Chef | [reported] |
| Search | Algolia | [reported] |
| Mobile | iOS app, App Store id 581312419 | [confirmed] |
| Teams | Web Platform, Core Services, Search & Recommendations, Schools, Schools Insight, Digital, Quality, Developer Tools | [reported] |

Next.js and Elasticsearch do not appear in any of the postings I found.

## 8. What this means for `generate-tpt`

The AI ban covers the resource, its covers, its previews, and its listing copy. A pipeline that auto-generates TPT products and listings breaks TPT's stated policy, and TPT is building detection for it. The project has three open lanes, and they can stack:

### Lane A: tooling for human authors on TPT
These tools improve the author's own work without generating it:
- **Listing linter:** checks title length, field completeness, tag coverage, and description opening against the schema in [`schema/tpt-listing.schema.json`](../schema/tpt-listing.schema.json).
- **Keyword research:** gathers autocomplete terms and competitor titles for a topic, so the author writes with real search language.
- **Standards mapper:** maps a resource to CCSS/TEKS/NGSS codes. The Learning Commons Knowledge Graph supports standards lookups and progressions.
- **Deterministic preview renderer:** turns the author's own PDF pages into thumbnails and preview images with fixed templates and no generative imagery.
- **Pricing and tier calculator:** the Exclusive vs. Basic math from Section 2, fed real sales data.

Whether TPT's final guidelines treat AI-assisted linting or keyword suggestions as "AI-generated" is still open. Check that before you ship anything that writes text.

### Lane B: multi-platform publishing
Basic sellers keep the right to sell elsewhere. Tes, Teach Simple, Boom Learning, Etsy, Payhip, and a self-hosted store each set their own AI policy. A generator that targets those channels avoids TPT's ban. If you pick this lane, stay on Basic (or leave TPT) for those titles, because Exclusive bars you from selling digital educational resources off TPT.

### Lane C: quality as the moat
TPT made this move because AI output with factual errors flooded the catalog. Expert-verified, standards-aligned material is the scarce asset now. Any generation workflow, on any platform, needs a human review gate and an answer-key verifier. You have to be able to prove that human review.

## 9. Re-verify once the network allows it

Allowlist these hosts in the environment's network settings, then re-run the crawl:
`www.teacherspayteachers.com`, `help.teacherspayteachers.com`, `sellerblog.teacherspayteachers.com`

Then confirm:
1. The final AI-policy text, its effective date, and whether it applies to existing listings
2. Basic tier: free or $29, and current transaction fees
3. Exclusive transaction fees, if any
4. Current browse URL slugs for grade, subject, resource type, and format (legacy `/Browse/...` patterns may redirect)
5. Title, tag, and description limits from the seller upload form
6. Thumbnail and preview specs
7. `robots.txt` and the sitemap structure

## Sources

- Chalkbeat, TPT to ban AI-generated resources (2026-10-15): https://www.chalkbeat.org/2026/10/15/teachers-pay-teachers-to-ban-ai-generated-teaching-resources/
- Chalkbeat, AI slop on TPT (2026-08-03): https://www.chalkbeat.org/2026/08/03/ai-slop-on-teachers-curriculum-marketplace/
- TPT Seller Blog, Introducing Exclusive Seller: https://sellerblog.teacherspayteachers.com/tpt-exclusive-seller/
- TPT Help Center, Exclusive Seller FAQs: https://help.teacherspayteachers.com/hc/en-us/articles/53904743536276-Exclusive-Seller-FAQs-for-New-Sellers-and-Basic-Sellers
- The SEO Teacher, Town Hall recap: https://theseoteacher.com.au/the-tpt-town-hall-left-sellers-with-more-questions-than-answers-5-things-you-can-do-now/
- Lit & More, The Cost of Exclusivity: https://lit-and-more.com/teachers-pay-teachers-the-cost-of-exclusivity/
- classroomreview.games, Exclusive vs Premium: https://classroomreview.games/tpt-exclusive-seller-what-it-means-for-teachers/
- K-12 Dive, IXL acquires TPT: https://www.k12dive.com/news/ixl-learning-acquires-teachers-pay-teachers/644562/
- EdWeek Market Brief, IXL acquires TPT: https://marketbrief.edweek.org/marketplace-k-12/ixl-learning-acquires-teachers-pay-teachers/
- Algolia case study: https://www.algolia.com/customers/teachers-pay-teachers
- urlscan homepage capture: https://urlscan.io/result/52d69d70-1886-49ae-9572-142f9270b5d6/content
- Wikipedia, Teachers Pay Teachers: https://en.wikipedia.org/wiki/Teachers_Pay_Teachers
- Built In NYC, TPT job postings: https://www.builtinnyc.com/job/engineer/technical-lead-web-platform/45442
- Apify TPT scraper docs (URL patterns): https://apify.com/solidcode/teacherspayteachers-scraper
- Creative Teacher Podcast, listing must-haves: https://the-creative-teacher-podcast.captivate.fm/episode/your-product-listing-must-haves
- tpt listing optimizer (unverified specs): https://clawhub.ai/kimmi2ue/tpt-listing-optimizer
- Change.org, AI policy petition: https://www.change.org/p/reevaluate-tpt-s-ai-content-policies
