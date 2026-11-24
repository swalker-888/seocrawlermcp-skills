---
name: fix-seo-issues
description: Fix technical SEO issues in a website's codebase using crawl results from SEO Crawler MCP. Crawl the live or preview site, trace each issue to the route, template, component or config that causes it, change the code, then re-crawl and compare to verify. Use in a coding agent when the user asks to fix SEO problems, canonicals, meta tags, redirects, sitemaps, broken links or noindex issues in their own site.
---

# Fix SEO issues in code

The crawl says which URLs have which problems. The repository says why. Fix causes, not pages.

## 1. Crawl the deployed site

The crawler runs in the cloud and can't reach `localhost` or private networks. Crawl production or a public preview deployment (previews behind deployment protection won't work). If there's no recent crawl, use the technical-seo-audit skill first (`start_crawl`, poll `get_crawl`). With `get_crawl` and no `crawl_id` you can list recent crawls.

## 2. Group issues by cause

For each issue in `summary.issues`, call `query_pages` with `issues: ["<check>"]`, `sort: "inlinks_desc"`, `limit: 20`. Group the URLs by path pattern. One pattern usually maps to one route or template:
- `/blog/*` → the blog post route or layout
- every page → the root layout, head component, middleware or server config
- links from every page → navigation or footer component

Use `detail: "full"` on a couple of URLs to see canonical targets, redirect chains, meta values and which pages link to the URL.

## 3. Find the code and fix it

Search the repository for the route, the metadata or head code, redirect rules, sitemap generation and link components. Common fixes:

| Issue | Usual cause in code |
|---|---|
| `no_canonical`, `canonical_to_redirect`, `canonical_chain` | Canonical built from the wrong base URL, missing trailing slash rule, or not set in a layout |
| `has_links_to_redirects` | Hard-coded links to old URLs, or links without the trailing slash the server redirects to |
| `has_broken_links`, `redirect_to_broken` | Links or redirects to removed routes |
| `duplicate_title`, `no_description` | A template with a static title or no description field |
| `sitemap_non_indexable`, `indexable_not_in_sitemap` | Sitemap generator listing redirects, noindexed pages or missing a section |
| unexpected `noindex_meta` / `noindex_header` | An environment check that leaks to production, or a header set in config or middleware |
| `js_noindex_added`, `js_canonical_changed`, `soft_404` | Client-side code changing head tags or rendering "not found" with a 200 status |

Make the smallest change that fixes the cause. Don't touch pages one by one when a template does it. Show the user the diff and explain which crawl finding each change addresses.

## 4. Verify

After the user deploys, crawl the same address again and call `compare_crawls` with the new `crawl_id`. Report which issues are resolved and anything new. Pages are matched by URL, so compare crawls of the same host.
