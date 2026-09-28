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
- **Level: Manager, Senior Manager, Director, Head of.** No Senior Director, VP, CMO, coordinator, specialist or associate. Drop roles whose top of range is under ~$120K.
- **Always link the employer's own posting**, never the aggregator.
- Show salary only when the posting lists it. Never invent salary or dates.
- **Remove roles already applied to** (check Gmail application confirmations and the 2026_JobSearch Google Sheet). A different role at the same company can stay, with a note.

## Liveness rule (critical)

Open EVERY link in the logged-in Chrome before publishing. Greenhouse/Lever/Ashby APIs and the fetch tool return stale data: closed Greenhouse jobs redirect to `?error=true` with "The job you are looking for is no longer open". Job-board apply links (emailjobs.io, lifecyclemarketingjobs.com) are often dead; follow them through to the employer page. On LinkedIn, drop anything showing "No longer accepting applications" or older than ~30 days. Only take salary from the posting itself (LinkedIn top card or description), never from sidebar text.

Sources also include LinkedIn (logged in; keywords "lifecycle marketing" OR "CRM marketing" OR "retention marketing", f_WT=2, f_E=4,5, past month) and the Indeed connector. The ZipRecruiter connector errored on 2026-09-28.

## Dashboard architecture

- `index.html` fetches `jobs.json` and renders cards. Weekly refresh only edits `jobs.json`.
- Thumbs up/down persist in `localStorage` keyed by job URL (`kaylyn_ratings_v1`).
- Each job object: `title, src, url, focus, level, industry, salaryMin, salaryLabel, datePosted, desc, applied`.
- `focus`: Lifecycle / CRM / Retention / Email. `level`: Manager / Senior Manager / Director. `industry`: Health & Wellness / DTC / Consumer / Tech / SaaS / Fintech / Other.
