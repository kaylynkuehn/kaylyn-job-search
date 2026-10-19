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

## History log (update every run)

`history.json` is the permanent backlog behind `history.html` (live: /history.html). Never delete roles from it. On every weekly refresh, after `jobs.json` is final:

1. Append a run: `{"date":"YYYY-MM-DD","count":<roles published>,"note":"<one line>"}` to `runs`. If a run is blocked and nothing could be verified, append it with `"count":null` and say why in `note`.
2. For every role in the new `jobs.json`: if it is already in `roles` (match on company + title), set `lastSeen` to today, add today to `seen`, keep `status:"open"`, refresh `url`/salary if they changed. Otherwise add it with `firstSeen` = `lastSeen` = today, `status:"open"`, `reason:"Still listed on the last verified run"`.
3. For every role with `status:"open"` that is NOT in the new `jobs.json`: set `status` (`closed` = posting gone or no longer accepting applications, `applied` = found in Gmail or the 2026_JobSearch sheet, `dropped` = no longer fits criteria, `retitled` = same req re-posted under a new title), `removedOn` = today, and a short `reason`.
4. Set `generated` to today.

Role fields: `company, title, url, focus, level, industry, salaryMin, salaryLabel, datePosted, firstSeen, lastSeen, seen[], status, removedOn, reason`.

## Search method (updated 2026-10-19). This section overrides anything above that conflicts.

Kaylyn should never have to find roles herself. The refresh must find them first.

### Why runs kept missing roles (diagnosed 2026-10-19)

- **Loose keyword searches drown the real matches.** Unquoted searches like `lifecycle marketing` (Remote, US, 2 weeks) return 2,000 to 3,500 results ranked by "relevance", led by promoted ads. Reading 1 to 2 pages (about 50 cards) missed exact-title roles. Example: Leap "Senior Lifecycle Marketing Manager" (posted 4 days earlier, saved by Kaylyn) never appeared in the first 50.
- **Window too short for the gap between runs.** Runs were 2 weeks apart but searches used a 1 or 2 week window, so roles posted 2 to 4 weeks earlier (First Stop Health, BenefitHub, Progyny) only showed up because she saved them.
- **Applied check was incomplete.** Only Gmail was checked; the LinkedIn Applied tab showed she had already applied to BenefitHub, Weiss Ratings and Wander.

### Step 0: Her trackers (every run, before searching)

1. LinkedIn Saved: https://www.linkedin.com/jobs-tracker/?stage=saved. Page through every page. Pages after about 3 are mostly "No longer accepting applications"; skip those.
2. LinkedIn Applied: https://www.linkedin.com/jobs-tracker/?stage=applied. Page through until entries are more than 2 months old. Anything there is excluded from the dashboard and logged in `history.json` as `applied`.
3. Gmail: search the last 75 days for rejections ("not moving forward", "other candidates", "decided to pursue", "not be moving forward") and application confirmations. Log rejections in `history.json` as `rejected` with the email date.

### Step 1: LinkedIn Boolean searches (the main source)

Use quoted phrases joined with OR, sorted Most recent, Remote, United States, and read EVERY page (the result sets are small, 40 to 120):

- Search A: `"lifecycle marketing" OR "lifecycle manager" OR "retention marketing" OR "CRM marketing" OR "lifecycle lead"`
- Search B: `"CRM manager" OR "email marketing manager" OR "retention manager" OR "director of retention" OR "head of retention" OR "director of lifecycle" OR "head of lifecycle" OR "CRM lead" OR "email marketing lead"`

URL pattern: `https://www.linkedin.com/jobs/search/?keywords=<urlencoded query>&location=United%20States&f_WT=2&f_TPR=r2592000&sortBy=DD&start=0` then start=25, 50, ...

Window: past month (`f_TPR=r2592000`) every run. On 2026-10-19 Search A returned 115 results and Search B 45; together they surfaced 11 qualifying roles the old method missed.

Reading cards: the left list is lazy-loaded. Scroll the list container itself (find the scrollable ancestor of `li[data-occludable-job-id]` and step `scrollTop` by 400 with short waits), then read every `li[data-occludable-job-id]`.

Verifying a role without opening its page: load `https://www.linkedin.com/jobs/search/?currentJobId=<id>&keywords=lifecycle` and read the right-hand detail pane (`.jobs-search__job-details--wrapper`). It shows status, workplace type, the top-card salary and the full description, and does not need a screenshot.

Screening: FULL-TIME ONLY (Kaylyn handles freelance work elsewhere; drop contract, temporary, fractional, freelance and part-time). Drop top of range under ~$120K, Senior Director / VP, specialist / associate / coordinator / account manager, contract or fractional, hybrid or on-site. Agencies and recruiters (Ladders, Jobgether, RemoteHive, "remote quest jobs") repost the same roles; link the employer's own posting instead.

### Step 2: Other sources (secondary)

- Hightouch Lifecycle Leaders, pages 1 to 3.
- **Braze Bonfire job board** (https://community.braze.com/categories/jobsboard) and **Iterable community job board** (https://community.iterable.com/job-board-22). Added 2026-10-19 at Kaylyn's request. Open each recent post and read it: most are EU, contract or hybrid. For any US-remote, full-time post, follow it to the employer's own posting (Greenhouse, Ashby, Lever, Workday, careers page or LinkedIn), confirm it is live, and link that employer posting on the dashboard, never the community post. If the post has no employer link (e.g. 'send your resume'), search the employer's careers page or LinkedIn for the role; skip it if you cannot find a live posting.
- ZipRecruiter connector (working since 2026-10-19): `location_types:["REMOTE"]`, `max_posted_minutes_ago:20160`, `salary_min:120000`. Its salaries are often estimates; only show pay that the posting itself lists.
- Lenny's Jobs: works when filters are set by hand (clear Job category, type "lifecycle", Location Remote or United States (remote)).
- Google Jobs (`google.com/search?q=lifecycle+marketing+manager+remote&udm=8`, Date posted: Last week, Remote): mostly aggregator noise but occasionally surfaces a company-site posting. Use as a cross-check only. Her Google "Saved jobs" list is old and expired; ignore it.
- lifecyclemarketingjobs.com, emailjobs.io: low yield; check quickly.
- Indeed connector: still noise. Skip unless the others are down.

### Sites the browser extension blocks

prizepicks.com (gambling) is blocked and locks the whole Chrome session until the tab is closed or Chrome restarts. Verify PrizePicks roles through their LinkedIn posting instead.

### Ranking by fit (added 2026-10-19)

Every role in `jobs.json` gets `fit` (score), `fitTier` and `fitWhy`, and `jobs.json` is saved sorted by `fit` (ties: health first, then Director > Senior Manager > Manager, then pay). The dashboard shows the tier badge and the reason, and has a Sort menu (best fit, level, highest pay).

Score = audience + role + level + platforms:

- Audience: consumer health / telehealth / wellness app 4; DTC brand or consumer subscription 3; consumer app or consumer fintech 2; health with an employer, provider or B2B2C audience 1; B2B SaaS, enterprise, agency or unnamed company 0.
- Role: core lifecycle, CRM or retention role 2; broader marketing role 1.
- Level: Director or Senior Manager 1; Manager 0.
- Platforms: Braze or Iterable named in the posting 1.

Tiers: 7+ Top fit, 5 to 6 Good fit, 4 or less Lower fit. Kaylyn's strongest fit is a lifecycle lead at a consumer health brand; B2B and non-DTC roles rank lower.

Also check Gmail for each company on the list (search the company name plus application / moving forward, past year). If she was rejected for the same role, drop it. If she applied or was rejected for a different role at that company, keep it and add a note to `desc`.

### After each run

Report to Kaylyn: what each source returned, anything in her saved list the searches missed and why, any rejections or applications newly logged, and which sources were broken.
