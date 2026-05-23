# Changelog

## 0.1.12 (2026-05-23)
- **fix:** Glassdoor Cloudflare now actually solved. `stealth_fetch` uses Scrapling's
  `StealthyFetcher` (Camoufox) with `solve_cloudflare=True` — it detects the challenge
  type and clicks the Turnstile widget, clearing managed/Turnstile challenges and
  obtaining `cf_clearance` (verified on Glassdoor incl. on a flagged datacenter IP,
  ~10-25s). Falls back to DynamicFetcher (patchright) if Camoufox can't launch. 0.1.10
  had switched to patchright-only after a Camoufox EPIPE that was actually caused by the
  missing `camoufox` package — now declared as a dependency.

## 0.1.11 (2026-05-23)
- **fix:** Glassdoor jobs-API Cloudflare recovery now **retries** the stealth
  solve up to 3× (breaks on first 200). Under datacenter-IP scrutiny the
  undetected browser is intermittent (~2-in-3 attempts loop the challenge), so a
  single solve sometimes failed; retrying makes recovery reliable without waiting
  for the IP's reputation to cool down.

## 0.1.10 (2026-05-23)
- **fix:** Glassdoor Cloudflare 403 recovery now actually works. Two bugs fixed:
  (1) the solved `cf_clearance` cookies were stored in `_cf_cookies` but never
  reused — now passed on every CSRF/location/jobs request via the `cookies=`
  kwarg; (2) the jobs GraphQL POST (`/graph`) had **no** 403 recovery and raised
  immediately — it now solves Cloudflare with the stealth browser, caches the
  cookies, and retries.
- **fix:** `stealth_fetch` now uses Scrapling's `DynamicFetcher` (backed by
  **patchright**, undetected Playwright/Chromium) instead of the Camoufox
  `StealthyFetcher`, which hangs/crashes (`EPIPE`) in many headless server
  environments. patchright's undetected browser passes Cloudflare reliably
  (~10s) and is far lighter. Requires `patchright install chromium` once.

## 0.1.9 (2026-05-20)
- **feat:** LinkedIn now extracts hour-level precision from the `<time>` tag text
  (e.g. "20 hours ago", "8 hours ago"). Previously we only used the `datetime`
  attribute which is day-level. `date_posted` may now be a `datetime` with hour
  precision; `JobPost.date_posted` typed as `datetime | date | None`.
- **fix:** Normalize `date_posted` to `pd.Timestamp` in `scrape_jobs()` before
  sort to avoid `TypeError` when mixing LinkedIn's datetime with Indeed/Glassdoor's
  date in the same DataFrame.

## 0.1.8 (2026-05-20)
- **fix:** Glassdoor URL missing `/` separator (`glassdoor.dejob-listing` → `glassdoor.de/job-listing`) — was causing Telegram `Button_url_invalid`
- **fix:** Glassdoor `locationType="S"` no longer blindly treated as remote; state-level locations like Hamburg now extracted correctly (Hamburg was being silently dropped, jobs shown without location)
- **fix:** Glassdoor crash when `ageInDays=None` — moved `timedelta` computation inside guard
- **feat:** Indeed company name fallbacks for anonymous/sponsored listings — extract from `relativeCompanyPageUrl` (`/cmp/Zenjob` → `Zenjob`), from title patterns ("Praktikum bei Horbach"), and from description bold tags / "About us" / "Welcome to" patterns. Catches 99.2% of cases that previously had `null` company.

## 0.1.7 (2026-05-19)
- **fix:** Deterministic output — stable mergesort with `id` tiebreaker (was non-stable quicksort over day-granularity dates with huge tie groups, making identical result sets appear reordered run-to-run)
- **fix:** Dedup now runs *after* the stable sort so `keep="first"` operates on deterministic order
- Verified: identical queries now produce byte-identical ordering; `hours_old` overfetch path is bit-stable (symdiff=0)

## 0.1.6 (2026-05-19)
- **fix:** Indeed regression — overfetch 2x when `hours_old` is set so client-side date filter doesn't starve results (AI Engineer 25→11 on v0.1.5, now back to 28)
- **fix:** Remove incorrect global `head(results_wanted)` cap — `results_wanted` is per-site, not total

## 0.1.5 (2026-05-19)
- **feat:** Title-based `experience_range` fallback — infers from "Senior", "Junior", "Werkstudent", etc. when description isn't available
- **feat:** Multilingual experience regex — matches "Jahre Berufserfahrung", "ans", "jaar" alongside English

## 0.1.4 (2026-05-19)
- **refactor:** Deduplicate `extract_experience_range()` and `gql_escape()` into `util.py`
- **fix:** GraphQL injection — strip `{}()` from search inputs
- **fix:** URL injection — `quote()` Glassdoor location parameter
- **fix:** Bare `except:` → `except Exception:` in LinkedIn and Glassdoor
- **fix:** Indeed `super().__init__` now passes `ca_cert` and `user_agent`
- **perf:** Pre-compiled regex at module level, all imports moved to top

## 0.1.3 (2026-05-19)
- **feat:** `skills` column — extracted from Indeed's `attributes` field
- **feat:** `experience_range` column — parsed from job descriptions on Indeed, LinkedIn, Glassdoor

## 0.1.2 (2026-05-19)
- **perf:** 6.7x faster Glassdoor — HTTP-first, Chromium only on 403 (2.1s vs 14s)
- **feat:** `hours_old` client-side enforcement — strict date filtering
- **feat:** Cross-site URL deduplication
- **feat:** Glassdoor metadata — `company_rating`, `easy_apply`, `job_url_direct`, `company_industry`
- **fix:** `description_format='plain'` now works on Indeed and Glassdoor

## 0.1.1 (2026-05-19)
- **fix:** Relax dependency version floors — prevents conflicts with existing projects (pandas>=2.1, numpy>=1.26, etc.)

## 0.1.0 (2026-05-19)
- Initial release — LinkedIn, Indeed, Glassdoor with Scrapling stealth
- Chrome TLS fingerprinting via `curl_cffi`
- Cloudflare bypass via `StealthyFetcher` for Glassdoor
- 0 CVEs (pip-audit clean)
