# Frase MCP Server

[![npm version](https://img.shields.io/npm/v/@frase/mcp-server.svg)](https://www.npmjs.com/package/@frase/mcp-server)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**The content loop, run from your AI agent.** This MCP (Model Context Protocol) server connects Claude, Cursor, Windsurf, VS Code, or any MCP client to [Frase](https://www.frase.io/agents?utm_source=github&utm_medium=readme&utm_campaign=mcp-server), the content operating system for AI search. Frase runs the loop content teams use to win Google rankings and AI citations, and this server puts that loop in your agent's hands:

- **Research** what ranks and why: live SERP analysis, competitor breakdowns, topic clusters
- **Create** briefs and drafts grounded in that research, in your brand voice
- **Publish** straight to WordPress, Sanity, Webflow, Wix, or FraseCMS
- **Optimize** every page for Google rankings and AI citations (ChatGPT, Perplexity, Gemini, Google AI) together
- **Track & fix**: audit sites for decay and cannibalization, watch where AI engines cite you (and where they cite competitors instead), and surface the pages that need attention next

Why run it from an agent? Stacks that research in one tool, draft in another, and publish in a third lose context at every hop. Frase keeps research, drafts, scores, and publishing in one system — so your agent works with full context instead of copy-paste.

**New to Frase?** The server connects to your Frase account. [Start a free 7-day trial](https://www.frase.io/?utm_source=github&utm_medium=readme&utm_campaign=mcp-server) (no credit card), then grab your API key from [Settings → API](https://next.frase.io/settings/api).

## What is MCP?

[Model Context Protocol (MCP)](https://modelcontextprotocol.io/) is an open standard that allows AI assistants like Claude to securely connect to external tools and data sources. This server implements MCP to give Claude access to your Frase account.

## Quick Start

### Option A: One-Click Install (Desktop Extension)

> Desktop extension bundles are published on the [Releases](https://github.com/frase-io/mcp-server/releases) page. If no release is listed yet, use Option B or C below — same server, two commands.

1. Download the latest `frase-x.x.x.mcpb` file from [Releases](https://github.com/frase-io/mcp-server/releases)
2. Open Claude Desktop and go to **Settings**
3. Drag the `.mcpb` file into the Settings window (or use **Install Extension**)
4. Enter your Frase API key when prompted (get it from [Frase Settings](https://next.frase.io/settings/api))
5. Done — Claude can now access your Frase account

### Option B: Claude Code (CLI)

Add the Frase MCP server to Claude Code with a single command:

```bash
claude mcp add frase -- npx -y @frase/mcp-server --api-key sk_live_your_api_key_here
```

Claude Code will automatically start the server when needed.

### Option C: Manual Setup (npx)

#### 1. Get your API key

Get your API key from [Frase Settings](https://next.frase.io/settings/api).

#### 2. Configure Claude Desktop

Add this to your Claude Desktop config file:

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "frase": {
      "command": "npx",
      "args": ["-y", "@frase/mcp-server"],
      "env": {
        "FRASE_API_KEY": "sk_live_your_api_key_here"
      }
    }
  }
}
```

#### 3. Restart Claude Desktop

After saving the config, restart Claude Desktop. You should see "frase" in the MCP servers list.

### Option D: Cursor

Add to your project's `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "frase": {
      "command": "npx",
      "args": ["-y", "@frase/mcp-server"],
      "env": {
        "FRASE_API_KEY": "sk_live_your_api_key_here"
      }
    }
  }
}
```

### Option E: Windsurf

Add to your Windsurf MCP config (`~/.codeium/windsurf/mcp_config.json`):

```json
{
  "mcpServers": {
    "frase": {
      "command": "npx",
      "args": ["-y", "@frase/mcp-server"],
      "env": {
        "FRASE_API_KEY": "sk_live_your_api_key_here"
      }
    }
  }
}
```

### Option F: VS Code with GitHub Copilot

Add to your VS Code settings (`.vscode/settings.json`):

```json
{
  "github.copilot.chat.mcpServers": {
    "frase": {
      "command": "npx",
      "args": ["-y", "@frase/mcp-server"],
      "env": {
        "FRASE_API_KEY": "sk_live_your_api_key_here"
      }
    }
  }
}
```

## Available Tools

### Sites
- `list_sites` - List all sites in your account
- `create_site` - Create a new site to track

### Briefs
- `list_briefs` - List content briefs with optional status filter
- `create_brief` - Create a new brief (with optional auto-outline generation)
- `get_brief` - Get brief details including outline and SERP data
- `get_brief_status` - Check brief generation progress
- `export_brief` - Export a brief in JSON or Markdown format

### Content
- `list_content` - List content items with optional status filter
- `get_content` - Get content details including full body text
- `generate_content` - Generate content from a brief
- `update_content` - Update content title, body, status, or meta tags
- `delete_content` - Delete a content item (soft delete)
- `get_content_status` - Check content generation progress
- `export_content` - Export content in JSON, HTML, or Markdown format
- `regenerate_content` - Regenerate or improve content with optional instructions
- `rescore_content` - Recalculate SEO, GEO, and EEAT scores for content

### Research
- `list_research` - List research sessions
- `start_research` - Start new AI-powered research on a topic (types: topic, competitor, gap, serp)
- `get_research` - Get research details with findings and sources
- `delete_research` - Delete a research session
- `export_research` - Export research findings in JSON or Markdown format

### Jobs
- `get_job_status` - Check status of async jobs (briefs, research, audits, content, etc.)

### Audits
- `list_audits` - List site audits
- `start_audit` - Start a new site audit (full, technical, content, or quick)
- `get_audit` - Get audit details with pages and issues
- `export_audit` - Export audit data as JSON or CSV
- `audit_page` - Audit a single page URL for E-E-A-T, GEO, and SEO scores

### SERP Analysis
- `analyze_serp` - Analyze search engine results for a query
- `analyze_competitors` - Analyze competitor content for a query
- `get_search_intent` - Get search intent classification for a query

### AI Visibility
- `get_ai_visibility` - Get AI visibility overview metrics
- `list_prompts` - List monitored AI prompts
- `create_prompt` - Create a new monitored prompt
- `get_prompt` - Get prompt details with citations and results
- `update_prompt` - Update a monitored prompt
- `get_competitors` - Get AI visibility competitors
- `get_alerts` - Get AI visibility alerts
- `get_insights` - Get AI visibility insights and recommendations
- `get_crawler_logs` - View AI bot crawl activity on your sites

### Analytics
- `get_gsc_overview` - Get Google Search Console overview metrics
- `get_gsc_queries` - Get top search queries from GSC
- `get_gsc_pages` - Get top performing pages from GSC
- `get_content_gaps` - Get content gap opportunities
- `list_performance_alerts` - List performance alerts (traffic drops, ranking changes)
- `resolve_alert` - Mark a performance alert as resolved

### Optimizations
- `list_optimizations` - List optimization sessions
- `start_optimization` - Start optimization analysis for content
- `get_optimization` - Get optimization details with suggestions
- `apply_optimization` - Apply optimization suggestions
- `get_optimization_insights` - Get optimization insights across content
- `dismiss_optimization` - Dismiss an optimization

### Webhooks
- `list_webhooks` - List configured webhooks
- `create_webhook` - Create a new webhook
- `get_webhook` - Get webhook details
- `update_webhook` - Update a webhook
- `delete_webhook` - Delete a webhook

### CMS Publishing
- `publish_content` - Publish content to Frase CMS or connected platforms
- `list_cms_connections` - List CMS connection status for a site

## Example Conversations

### List your sites
```
You: What sites do I have connected?
Claude: [Uses list_sites tool]

Here are your connected sites:

| ID | Name | Domain | GSC | Created |
|----|------|--------|-----|---------|
| site_abc | Main Blog | example.com | Yes | Jan 15, 2024 |
| site_def | Product Site | product.example.com | No | Feb 20, 2024 |
```

### Create a brief with auto-outline
```
You: Create a brief about React performance optimization
Claude: [Uses create_brief with generate_outline=true]

## Brief Created

- **ID:** brief_abc123
- **Topic:** React performance optimization
- **Status:** pending

**Outline generation started.** Let me check the status...

[Uses get_job_status]

The outline is ready! Let me get the details...

[Uses get_brief]

## Brief: React performance optimization

### Outline
- Introduction to React Performance
- Common Performance Bottlenecks
- Optimization Techniques
- Measuring Performance
- ...
```

### Research a topic
```
You: Research best practices for Next.js SEO
Claude: [Uses start_research]

## Research Started

- **ID:** res_xyz789
- **Query:** best practices for Next.js SEO
- **Type:** topic
- **Status:** pending

I'll check on the progress...
```

## MCP Resources

Browse your Frase data directly in Claude:

- `frase://sites` - List all your sites
- `frase://sites/{id}` - View individual site details
- `frase://content` - List all content items
- `frase://content/{id}` - View content with full body
- `frase://briefs` - List all briefs
- `frase://briefs/{id}` - View brief with outline

## MCP Prompts

Pre-built workflows for common SEO tasks:

| Prompt | Description |
|--------|-------------|
| `create_seo_article` | Full workflow: research, brief, outline, content generation |
| `optimize_content` | Analyze content and apply optimization suggestions |
| `keyword_research` | Research keywords, SERP analysis, and content opportunities |
| `competitor_analysis` | Analyze competitor content and find gaps |
| `content_audit` | Run site audit and prioritize improvements |
| `content_pipeline` | Full autonomous content lifecycle: research, brief, write, optimize, publish |
| `content_watchdog` | Monitor performance, fix ranking drops, re-optimize underperforming content |

## Configuration

| Variable | Required | Description |
|----------|----------|-------------|
| `FRASE_API_KEY` | Yes | Your Frase API key |
| `FRASE_API_URL` | No | Override API URL (default: `https://next.frase.io/api/v1`) |

## Troubleshooting

### Server not appearing in Claude Desktop

1. Verify your config file location:
   - **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
   - **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

2. Check JSON syntax - use a validator if needed

3. Restart Claude Desktop completely (quit and reopen)

### Server not working in Claude Code

1. Verify the server is registered: `claude mcp list`
2. Check server status: `claude mcp status frase`
3. Remove and re-add if needed: `claude mcp remove frase`

### Authentication errors

- Verify your API key is correct
- Ensure the key has not expired
- Check that the key has the required permissions

### Rate limiting

The server automatically retries on rate limits with exponential backoff. If you're hitting limits frequently, consider spacing out your requests.

## Support

- [Frase Documentation](https://docs.frase.io)
- [API Documentation](https://next.frase.io/api/docs)
- [GitHub Issues](https://github.com/frase-io/mcp-server/issues)

## License

MIT
