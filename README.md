# Daily Job Alerts — direct source checks + search discovery

## Recipients and schedule
- **Daily general digest:** 11:10 Africa/Harare (09:10 UTC), to `takaengwa@gmail.com`.
- **PR / public relations / communications / corporate affairs digest:** to `telmamtoko@gmail.com`, with `takaengwa@gmail.com` CC'd. PR roles are intentionally excluded from the general digest.
- **Sunday weekly summaries:** separate general and PR summaries, routed to the same recipients above.
- **Urgent alerts:** confirmed-sponsored vacancies with a detected deadline within 3 days and CV score >= 5. PR roles use the PR distribution.
- Sender: Gmail SMTP using `GMAIL_ADDRESS` and `GMAIL_APP_PASSWORD` GitHub Actions secrets.

## Search coverage
The workflow now combines Google News RSS discovery with direct requests to current vacancy-listing pages for:
- [VacancyMail](https://www.vacancymail.co.zw/jobs/)
- [ZimboJobs](https://www.zimbojobs.com/jobs)
- [IPC Jobs](https://www.ipcjobs.org/)
- [IPC Consultants](https://www.ipcconsultants.com/jobs)
- [Jobs Zimbabwe](https://jobszimbabwe.co.zw/jobs/?display=list)
- [Recruitment263](https://recruitment263.co.zw/jobseekers)
- [ZimJobSite](https://www.zimjobsite.com/jobs)
- [ZimJobsHub](https://zimjobs.online/)
- [Public Service Commission eRecruitment](https://erecruitment.psc.gov.zw/)

The direct checks extract relevant IT/ICT, software/systems, data/BI/analytics, and PR/communications/corporate affairs links from listing pages, then fetch the individual advert for deadline and status screening. Search engine discovery remains as a second layer for LinkedIn, employer career pages, remote roles, Australia and New Zealand sponsorship roles, and listings missed by source-page extraction.

**Coverage limitation:** direct HTTP checks are best-effort. A site can block automated requests, use JavaScript that does not expose listings to this scraper, change its page structure, require login, or paginate beyond the pages checked. Logs report direct-source counts and errors; the email warns when checks fail. This is broader than Google News alone but is not a guarantee of exhaustive coverage. Official employer adverts remain the preferred source of truth.

## Search scope
- Zimbabwe: IT/ICT/support, software/systems, data/BI/analytics, PR/public relations/communications/corporate affairs/media relations/public affairs.
- Remote: roles where advert text indicates applicants working from Zimbabwe or a wider eligible region may apply. Location restrictions are screened out when detected.
- Australia and New Zealand: IT/software, data/BI, and PR/communications/corporate affairs roles with sponsorship classification.
- All career levels, including graduate, junior, officer, specialist, senior and management roles.

## Sponsorship policy
- **Confirmed:** explicit sponsorship wording on an accessible original advert; not a guarantee of visa approval.
- **Potential:** advert is silent or mentions visa/relocation/accredited-employer support without an explicit sponsorship offer. Ask the employer.
- **Unavailable:** explicit no-sponsorship or incompatible existing-work-rights restrictions are detected; listing is excluded.
- **Unverified:** original advert could not be checked or sponsorship wording appears only in search results.
- Search snippets alone never count as confirmed sponsorship and never trigger urgent sponsorship alerts.

## Deadline checks and deduplication
- Follows redirects, fetches destination pages, extracts visible text, detects closed wording and common deadline formats.
- Expired deadlines and explicitly closed listings are excluded. Missing dates are labelled `Not stated — verify active status` unless `REQUIRE_FUTURE_DEADLINE=true` is set.
- Direct source listings may not expose publication dates; these are marked `Not stated (direct source listing)` rather than inventing a date.
- Deduplicates by canonical URL and normalized title; state is retained for up to 60 days through GitHub Actions cache. Cache persistence is best-effort, so listings may recur if GitHub evicts the cache.

## CV matching
Keyword match profiles cover IT service management/support, Python/software systems, SQL/data analytics/BI, ETL, and PR/communications. Scores are screening aids, not determinations of eligibility or qualification.

## Setup
1. Extract this ZIP and upload its contents to the root of your private GitHub repository, preserving `.github/workflows/daily-job-alerts.yml`.
2. Under **Settings → Secrets and variables → Actions**, create `GMAIL_ADDRESS` and `GMAIL_APP_PASSWORD` for the Gmail account that sends the messages.
3. Recipient routing is set in the workflow: `EMAIL_TO=takaengwa@gmail.com`, `PR_EMAIL_TO=telmamtoko@gmail.com`, `PR_EMAIL_CC=takaengwa@gmail.com`.
4. Enable Gmail 2-Step Verification and create an App Password at https://myaccount.google.com/apppasswords. Do not use your normal Gmail password.
5. Open **Actions → Daily Job Alerts → Run workflow**. Inspect the logs for direct-source counts and errors; confirm emails arrive in the correct inboxes.
6. Keep the workflow enabled. Scheduled GitHub Actions runs can be delayed.

## Important
The package is not deployed until you upload it and configure secrets. It does not claim live email delivery. Always verify the original employer advert and immigration requirements before applying. Never commit credentials.
