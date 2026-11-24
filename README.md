# SEO Crawler MCP skills

Agent skills and a Claude Code plugin for technical SEO audits with [SEO Crawler MCP](https://seocrawlermcp.com), a hosted website crawler that AI assistants use through the Model Context Protocol.

The crawler runs in the cloud: your assistant starts a crawl, the crawler fetches every URL the way a search engine does, and the assistant filters the results on the server. These skills teach the assistant how to run the audit well: what to check first, how to prioritise, how to trace issues to code, and how to report.

| Skill | Use it to |
|---|---|
| [`technical-seo-audit`](skills/technical-seo-audit/SKILL.md) | Crawl a site and report indexability, redirects, canonicals, broken links, duplicates, sitemaps and hreflang, prioritised |
| [`javascript-seo-audit`](skills/javascript-seo-audit/SKILL.md) | Render every page in a headless browser and report what JavaScript changes: noindex, canonicals, content, links, soft 404s |
| [`ai-crawler-access`](skills/ai-crawler-access/SKILL.md) | Check which AI crawlers robots.txt allows and whether they can read pages without JavaScript |
| [`seo-crawl-monitoring`](skills/seo-crawl-monitoring/SKILL.md) | Schedule recurring crawls and report what changed between runs |
| [`fix-seo-issues`](skills/fix-seo-issues/SKILL.md) | In a coding agent: trace each issue to the template that causes it, fix it, re-crawl to verify |

## Install

### Claude Code plugin (server and skills together)

```
/plugin marketplace add swalker-888/seocrawlermcp-skills
/plugin install seo-crawler@seocrawlermcp
```

Then run `/mcp`, choose `seocrawlermcp` and sign in in your browser.

### Skills only, for any agent that supports them

```
npx skills add swalker-888/seocrawlermcp-skills
```

The skills need the MCP server connected. Add `https://seocrawlermcp.com/api/mcp` as a remote MCP server in your app; setup for Claude, ChatGPT, Cursor, VS Code, Claude Code and Codex is at [seocrawlermcp.com/guides/connect-seo-crawler-mcp](https://seocrawlermcp.com/guides/connect-seo-crawler-mcp).

## Try it

```
Crawl example.com, up to 500 pages, and tell me the five problems that affect the most pages.
```

New accounts get 1,000 free credits: 1 credit per page, 5 per page rendered with JavaScript.

## Free tool

[AI crawler checker](https://seocrawlermcp.com/tools/ai-crawler-checker): see which of 16 AI crawlers a site's robots.txt allows, and the line that decides. No sign-up.

## Links

- Website: [seocrawlermcp.com](https://seocrawlermcp.com)
- MCP server: `https://seocrawlermcp.com/api/mcp` (Streamable HTTP, OAuth). Official MCP Registry: `com.seocrawlermcp/seo-crawler`
- Guides: [seocrawlermcp.com/guides](https://seocrawlermcp.com/guides)
- About the crawler: [seocrawlermcp.com/bot](https://seocrawlermcp.com/bot)
- Contact: hello@seocrawlermcp.com

The skills in this repository are MIT licensed. The crawler service itself is not open source.
