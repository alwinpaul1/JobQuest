# Changelog

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
