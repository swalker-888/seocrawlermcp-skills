---
name: technical-seo-audit
description: Run a full technical SEO audit of a website with the SEO Crawler MCP tools (start_crawl, get_crawl, query_pages, export_pages) and report prioritised findings. Use when the user asks to audit, crawl or check the SEO of a site, find indexability problems, redirect chains, broken links, canonical issues, duplicate titles, sitemap problems or hreflang errors.
---

# Technical SEO audit

Audit a site with a real crawl, not by browsing. Every finding comes from the crawl data, with page counts and example URLs.

## Before you start

The SEO Crawler MCP tools must be available (`start_crawl`, `get_crawl`, `query_pages`). If they aren't, tell the user to connect the server at `https://seocrawlermcp.com/api/mcp` (setup for each app: https://seocrawlermcp.com/guides/connect-seo-crawler-mcp) and stop.

Agree the scope:
- **Start URL**: use the exact host the user gives; www and apex count as one site.
- **max_pages**: default 500. Each page costs 1 credit (5 if rendered with JavaScript). Credits for `max_pages` are held up front and the unused ones returned, so tell the user the most it can cost before a crawl over 1,000 pages.
- **render**: `"http"` unless the site is a JavaScript app or the user asks what Google sees after rendering. Then use the javascript-seo-audit skill.
- **respect_robots**: leave it true. Only set false when the user says they own the site.

## Run it

1. Call `start_crawl` with `url`, `max_pages` and, if needed, `max_depth`. It returns a `crawl_id` immediately.
2. Call `get_crawl` with the `crawl_id` every 30–60 seconds until `status` is `finished` or `failed`. Tell the user progress in a line, not every poll.
3. Read the summary from `get_crawl`:
   - `summary.notes` first: they say when results are partial (page budget hit, robots.txt unreachable, site blocking the crawler).
   - `summary.indexability`: counts by state (`indexable`, `noindex_meta`, `noindex_header`, `canonicalised`, `redirected`, `blocked_robots`, `error`, `not_html`).
   - `summary.issues`: check name → number of pages where it's true. Every check is a problem when true.
   - Site facts: robots.txt, sitemaps found, AI crawlers blocked, llms.txt.

## Prioritise

Rank issues by impact, not by count alone:
1. Pages that can't be indexed and should be (noindex, canonicalised elsewhere, errors) on well-linked pages.
2. Broken internal links (`has_broken_links`), `redirect_to_broken`, `redirect_loop`, `is_5xx`.
3. Canonical problems: `canonical_to_redirect`, `canonical_to_broken`, `canonical_to_noindex`, `canonical_chain`, `recursive_canonical`, `no_canonical`.
4. Internal links to redirects (`has_links_to_redirects`), `redirect_chain`.
5. Sitemap hygiene: `sitemap_non_indexable`, `is_orphan`, `indexable_not_in_sitemap`.
6. `hreflang_missing_return`, `hreflang_no_self_reference`, `hreflang_invalid`.
7. Duplicates and on-page: `duplicate_title`, `duplicate_description`, `no_title`, `no_description`, `no_h1`, `multiple_h1`.
8. Everything else (title length, `images_without_alt`, URL format) last, briefly.

## Drill in

For each issue worth reporting, call `query_pages` with `crawl_id`, `issues: ["<check>"]` and `sort: "inlinks_desc"` so the best-linked pages come first, `limit` 10–20. Look for the pattern: a shared URL path (`url_contains`) usually means one template causes it. Use `detail: "full"` on two or three pages to see the redirect chain, canonical target, meta and the pages linking to it.

Useful filters: `indexability`, `status_class` (`"4xx"`, `"5xx"`, `"3xx"`), `status_code`, `url_contains`, `in_sitemap`, `max_depth`. They combine with AND. Paginate with `next_cursor` only when the user needs the full list; offer `export_pages` instead.

## Report

Lead with a two-sentence verdict. Then one section per issue, in priority order:
- what it is, in plain words, and the number of pages;
- 3–5 example URLs (best-linked first);
- the likely cause (template, redirect rule, sitemap generator) and the fix.

End with what wasn't covered (partial crawl, robots-blocked sections, no JavaScript rendering) and offer a CSV: `export_pages` with the same filters returns a link that works for 15 minutes without sign-in. Give the user the link; don't fetch it.

Don't invent numbers. Don't report a check as a problem unless `summary.issues` or `query_pages` shows it.
