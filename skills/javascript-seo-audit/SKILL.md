---
name: javascript-seo-audit
description: Audit what JavaScript changes on a website by crawling with headless-browser rendering (start_crawl with render "js") and comparing each page's raw HTML with the rendered page. Use for React, Vue, Angular, Next.js or other client-rendered sites, or when the user asks what Google sees after rendering, about soft 404s, hydration changing meta tags, or content and links that need JavaScript.
---

# JavaScript SEO audit

A JavaScript SEO audit compares raw HTML with the rendered page. The differences are the findings.

## Before you start

You need the SEO Crawler MCP tools. If `start_crawl` isn't available, point the user to https://seocrawlermcp.com/guides/connect-seo-crawler-mcp and stop.

Cost: 5 credits per rendered page (redirects, errors and non-HTML pages cost 1). Rendering runs at roughly 2 pages a second. Suggest `max_pages` of 100–300 for a first pass and tell the user the maximum cost (`max_pages × 5`).

## Run it

1. `start_crawl` with `url`, `max_pages` and `render: "js"`.
2. Poll `get_crawl` every 30–60 seconds until `finished`.
3. Read `summary.issues` for the JavaScript checks (they end in "(js)" in the tool descriptions).

## What to look for, in this order

| Check | Why it matters |
|---|---|
| `js_noindex_added` | JavaScript adds a noindex the HTML doesn't have: Google drops the page after rendering. Often a consent, A/B testing or tag manager script. |
| `js_noindex_removed` | The HTML has noindex and JavaScript removes it. Google may not render noindexed pages, so it stays noindexed. |
| `js_canonical_changed` | Two different canonicals depending on rendering; the outcome is unpredictable. |
| `soft_404` | Server returns 200 but the rendered page says "not found". Typical of single-page apps. |
| `js_content_dependent` | Most visible text only exists after rendering. Crawlers that don't run JavaScript, including many AI crawlers, see an empty page. |
| `js_links_dependent`, `links_not_crawlable`, `hash_route_links` | Internal links Google can't discover or follow. |
| `js_redirect` | A client-side redirect instead of a server redirect. |
| `resources_blocked_by_robots` | robots.txt blocks scripts or styles Googlebot needs. |
| `resources_failed`, `render_js_errors`, `render_timeout`, `render_failed` | Broken scripts or API calls; content depending on them may be missing. |
| `js_title_changed`, `js_description_changed`, `js_h1_changed`, `js_hreflang_changed`, `js_structured_data_added` | Usually fine, but say which version search engines and AI crawlers get. |

For each, `query_pages` with `issues: ["<check>"]`, `sort: "inlinks_desc"`, `limit: 10`. Then `detail: "full"` on two or three pages: the `render` field holds the raw-vs-rendered differences (word counts and internal links before and after, links only in the rendered page, changed fields, failed and blocked resources, JavaScript errors).

## Report

Start with the verdict: does the site depend on JavaScript for content, links or directives? Then each finding with page count, examples and the fix: server-render or pre-render the content, put directives (noindex, canonical) in the server HTML, return real 404 statuses, use `<a href>` links to real URLs, unblock required resources in robots.txt.

Note the limits: no clicking or scrolling, so content behind "load more" isn't rendered (as for Google); images, video and fonts aren't loaded.
