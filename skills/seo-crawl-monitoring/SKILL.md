---
name: seo-crawl-monitoring
description: Set up recurring SEO crawls of a website and report what changed between runs, using schedule_crawl, list_schedules and compare_crawls from SEO Crawler MCP. Use when the user wants to monitor a site, catch SEO regressions after releases, crawl weekly or daily, or compare a crawl with an earlier one.
---

# SEO crawl monitoring

## Set up a schedule

You need the SEO Crawler MCP tools; if `schedule_crawl` isn't available, point the user to https://seocrawlermcp.com/guides/connect-seo-crawler-mcp.

1. Confirm `url`, `frequency` (`daily`, `weekly` or `monthly`), `max_pages` and `render` (`"http"` or `"js"`).
2. State the cost before creating it: up to `max_pages` credits per run (×5 for JavaScript), times the number of runs a month.
3. Call `schedule_crawl`. The first crawl starts now and is returned as `first_crawl`; poll it with `get_crawl` like any crawl. Later runs start automatically once a day when due.
4. Up to 10 schedules per user. A run is skipped, with the reason in `last_error`, when credits run short or the previous run is still going.

`list_schedules` shows every schedule, its next run, the latest crawl and any error. Only call `delete_schedule` when the user asks.

## Compare two crawls

`compare_crawls` with the newer `crawl_id`. Omit `previous_crawl_id` to compare with the previous finished crawl of the same site and render mode. Pages are matched by URL, so both crawls must be of the same address.

It returns URLs added and removed, status code changes, indexability changes, title changes, pages with new or resolved issues, and the change in pages per issue, best-linked pages first.

## Report changes

Lead with regressions, then fixes, then neutral changes:
1. Pages that became non-indexable, started erroring or started redirecting.
2. New issues, by number of pages, with examples.
3. Issues resolved since last time.
4. URLs added or removed (often a release or a migration).

If nothing important changed, say so in one line. Don't pad.
