# @frase/mcp-server

> **About this repository** — the public home of the Frase MCP server: documentation, registry metadata (server.json), changelog, and desktop-extension releases. The server is developed in the Frase private monorepo and ships as [@frase/mcp-server](https://www.npmjs.com/package/@frase/mcp-server) on npm; a hosted remote (OAuth) runs at https://next.frase.io/api/mcp. Issues and feature requests are welcome here.

[![npm version](https://img.shields.io/npm/v/@frase/mcp-server.svg)](https://www.npmjs.com/package/@frase/mcp-server)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Use Claude to interact with your Frase account. This MCP (Model Context Protocol) server exposes Frase's API as tools that Claude can use to help you with SEO/GEO content creation, research, optimization, AI Visibility tracking, and much more. Works with both Claude Desktop and Claude Code.

## What is MCP?

[Model Context Protocol (MCP)](https://modelcontextprotocol.io/) is an open standard that allows AI assistants like Claude to securely connect to external tools and data sources. This server implements MCP to give Claude access to your Frase account.

## Hosted Remote Server (OAuth)

Prefer zero setup? Frase runs a hosted MCP server at `https://next.frase.io/api/mcp` (streamable HTTP with OAuth). Clients that support remote MCP servers connect by URL and sign in with your Frase account in the browser. No API key, no local install, and updates ship automatically.

- **Claude.ai / Claude Desktop:** add Frase from the Connectors directory, or add a custom connector with the URL above.
- **Claude Code:**

```bash
claude mcp add --transport http frase https://next.frase.io/api/mcp
```

The options below run the server locally over stdio with an API key.

## Quick Start

### Option A: One-Click Install (Desktop Extension)

1. Download the latest extension bundle: [frase-mcp.mcpb](https://next.frase.io/api/downloads/mcp-extension)
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
- `create_prompt` - Create a new monitored prompt. Location defaults to the site's country when omitted (pass `site_id`); the same prompt can be tracked once per site.
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

### Create a brief from research
```
You: Create a brief about React performance optimization
Claude: [Runs start_research, waits for completion, then uses create_brief with the research_session_id]

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

The server retries on rate limits with exponential backoff, which resolves short per-minute bursts.

Three limits apply independently: a per-minute burst, a sustained hourly ceiling, and a monthly request budget. Retrying only helps with the first — the `429` for the hourly ceiling carries a `Retry-After` of up to an hour, and retrying sooner cannot succeed. Check the error `code` to tell them apart: `rate_limit_exceeded` (burst, retry shortly), `hourly_limit_exceeded` (wait for `Retry-After`), `monthly_budget_exceeded` (upgrade or wait for the cycle).

## Support

- [Frase Documentation](https://docs.frase.io)
- [API Documentation](https://next.frase.io/api/docs)
- [GitHub Issues](https://github.com/frase-io/mcp-server/issues)
- Email: [support@frase.io](mailto:support@frase.io)

## Privacy Policy

**[Frase Privacy Policy](https://www.frase.io/company/privacy)**

This server connects to your Frase account and sends the requests you make to
the Frase API. It collects no data of its own beyond the tool-invocation
telemetry described below, and it does not read your conversation history,
files, or memory.

- **What is collected** — for each tool call: your Frase organization ID and
  user ID (or API key ID), the tool name, a SHA-256 hash of the arguments (not
  the arguments themselves), the argument size in bytes, how long the call took,
  whether it succeeded, an error type if it failed, and a timestamp. This is
  used for auditing and reliability.
- **What is not collected** — conversation content, prompts, or any argument
  values in readable form.
- **Storage and retention** — telemetry is stored against your Frase
  organization and retained per the retention terms in the privacy policy above.
- **Third-party sharing** — none beyond the subprocessors named in the privacy
  policy.
- **Deletion** — email [support@frase.io](mailto:support@frase.io) to request
  deletion of your data.

## License

MIT
