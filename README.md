# Kaylyn's Remote Job Search

Weekly dashboard of fully remote lifecycle, CRM, retention and email marketing roles for **Kaylyn Murray**. Live site: https://kaylynkuehn.github.io/kaylyn-job-search/

Data lives in `jobs.json`; the page (`index.html`) renders from it.

---

## Candidate profile (summary)

Lifecycle, CRM and retention marketing leader, 12+ years. Founder, Hook Digital LLC. Prior: Thirty Madison (Nurx, Keeps, Cove), Function of Beauty, Hawthorne, Elizabeth Arden Red Door Spas. Platforms: Braze, Iterable, Adobe Journey Optimizer, Adobe CJA, ActiveCampaign, Slate.

Targets: fully remote (US), Manager / Senior Manager / Director / Head of, in lifecycle, CRM, retention and email marketing. Strongest fit in health and wellness, telehealth, DTC and consumer tech.

---

## Sources to search (every run)

1. **Hightouch Lifecycle Leaders** - https://jobs.lifecycle-leaders.hightouch.com/lifecycle (fetchable; page through all pages).
2. **Lifecycle Marketing Jobs** - https://www.lifecyclemarketingjobs.com (blocks the fetch tool; use the browser).
3. **Email Jobs (Email Geeks)** - https://www.emailjobs.io/ (blocks the fetch tool; use the browser).
4. **Lenny's Jobs** - https://www.lennysjobs.com (JS-rendered via trueup; use the browser). Filters: Marketing & Growth > Growth, United States (remote). Run separate searches for **Lifecycle**, **Retention** and **CRM**.
5. **ATS-targeted web search** - `job-boards.greenhouse.io`, `boards.greenhouse.io`, `jobs.lever.co`, `jobs.ashbyhq.com`. Ashby pages are JS-rendered; verify via `api.ashbyhq.com/posting-api/job-board/<company>`.

Search terms: "Lifecycle Marketing Manager", "Senior Manager Lifecycle", "Director of Lifecycle Marketing", "Director CRM", "Head of Retention", "Retention Marketing", "CRM Manager Braze", "Lifecycle Iterable".

## Curation rules

- **Fully remote (US).** Drop hybrid, on-site and non-US. State-restricted remote is allowed only if NY is eligible; say so on the card.
- **Level: Manager, Senior Manager, Director, Head of.** No coordinator, specialist, associate, VP or CMO.
- **Always link the employer's own posting**, never the aggregator.
- Show salary only when the posting lists it. Never invent salary or dates.
- Flag roles already applied to: check Gmail application confirmations and the 2026_JobSearch Google Sheet tracker. Set `applied: true` for an exact-role match; note different-role matches in `desc`. Drop roles that rejected her.

## Liveness rule

Re-verify every carried-over role each run. Remove anything closed, 404, or no longer on the company's job board.

## Dashboard architecture

- `index.html` fetches `jobs.json` and renders cards. Weekly refresh only edits `jobs.json`.
- Thumbs up/down persist in `localStorage` keyed by job URL (`kaylyn_ratings_v1`).
- Each job object: `title, src, url, focus, level, industry, salaryMin, salaryLabel, datePosted, desc, applied`.
- `focus`: Lifecycle / CRM / Retention / Email. `level`: Manager / Senior Manager / Director. `industry`: Health & Wellness / DTC / Consumer / Tech / SaaS / Fintech / Other.
