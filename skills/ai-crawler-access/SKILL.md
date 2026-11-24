---
name: ai-crawler-access
description: Check whether AI crawlers (GPTBot, OAI-SearchBot, ClaudeBot, Claude-SearchBot, PerplexityBot, Google-Extended, CCBot and others) can reach and read a website, using SEO Crawler MCP crawl data and the free AI crawler checker. Use when the user asks about AI search visibility, GEO, being cited by ChatGPT, Claude or Perplexity, robots.txt rules for AI bots, or llms.txt.
---

# AI crawler access

Two questions decide whether AI products can use a site: are their crawlers allowed, and can they read the page without running JavaScript?

## 1. Are they allowed?

Quick check for one URL, no account needed: https://seocrawlermcp.com/tools/ai-crawler-checker, or as JSON from `GET https://seocrawlermcp.com/api/tools/ai-crawlers?url=example.com`. It reports, for 16 user agents, allowed or blocked and the robots.txt line that decides.

With the SEO Crawler MCP tools, any crawl's `get_crawl` result includes `ai_crawlers_blocked` (AI crawlers robots.txt disallows from the homepage) and whether the site has an llms.txt.

Explain the difference between bot types:
- **Training** (`GPTBot`, `ClaudeBot`, `CCBot`, and the `Google-Extended` and `Applebot-Extended` tokens): blocking keeps pages out of future training data; it doesn't remove the site from AI answers that cite live pages.
- **AI search** (`OAI-SearchBot`, `Claude-SearchBot`, `PerplexityBot`): blocking can stop those products finding and citing the site. Usually a mistake for a site that wants AI visibility.
- **User fetchers** (`ChatGPT-User`, `Claude-User`, `Perplexity-User`): fetch a page because a person asked; some providers say robots.txt may not apply.
- **Googlebot** powers AI Overviews and AI Mode; blocking it removes the site from Search.

A robots.txt group that names a bot replaces the `*` group for that bot entirely, so rules copied from "block all AI bots" lists often block search crawlers by accident.

## 2. Can they read it?

Many AI crawlers don't run JavaScript. Crawl with `render: "js"` (5 credits per rendered page) and look for `js_content_dependent` (most text appears only after rendering) and `js_links_dependent`. Pages with those issues may be empty to AI crawlers even when allowed. See the javascript-seo-audit skill.

## Report

Say which AI crawlers are blocked and whether that looks intentional (training blocked, search allowed is a common deliberate choice), quote the deciding robots.txt lines, note llms.txt presence, and list templates that depend on JavaScript for content. Note that robots.txt doesn't show firewall or CDN bot blocking; only server logs do.
