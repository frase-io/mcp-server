# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.3.0] - 2026-03-19

### Added
- **95 tools** (up from 70+) — new CMS publishing, content rules, playbooks, templates, atomization, opportunities, clusters, and site health tools
- **9 agent skills** — pre-built workflows for common content operations
- **7 workflow prompts** — including `content_pipeline` and `content_watchdog`
- Multi-client support: Claude Desktop, Claude Code, Cursor, Windsurf, VS Code Copilot, Lovable, Replit
- `@frase/core` shared library for API client, types, and utilities
- `@frase/cli` companion CLI with 22 commands

### Changed
- Upgraded to `@frase/core` for shared API client (previously inline)
- Updated MCP Registry metadata and description

## [0.2.0] - 2026-03-08

### Added
- **Prompt: `content_pipeline`** — Full autonomous content lifecycle workflow (research > brief > write > optimize > publish > monitor)
- **Prompt: `content_watchdog`** — Performance monitoring and auto-fix workflow for ranking drops
- **Enhanced `create_seo_article` prompt** — Now includes optimization scoring, revision loop, and CMS publishing steps
- Multi-tool setup instructions for **Cursor**, **Windsurf**, and **VS Code Copilot**
- `target_language` parameter on `create_brief` tool (ISO 639-1 code, e.g., 'en', 'de', 'pt')
- `target_country` parameter on `create_brief` tool (ISO 3166-1 code, e.g., 'us', 'de', 'br')

### Fixed
- `SERVER_VERSION` constant now correctly reads `0.2.0` (was stuck at `0.1.0` since initial release)

## [0.1.0] - 2026-02-01

### Added

#### Tools (38 total)
- **Sites**: `list_sites`
- **Briefs**: `list_briefs`, `create_brief`, `get_brief`
- **Content**: `list_content`, `get_content`
- **Research**: `list_research`, `start_research`
- **Jobs**: `get_job_status`
- **Audits**: `list_audits`, `start_audit`, `get_audit`, `export_audit`
- **SERP**: `analyze_serp`, `analyze_competitors`, `get_search_intent`
- **AI Visibility**: `get_ai_visibility`, `list_prompts`, `create_prompt`, `get_prompt`, `update_prompt`, `get_competitors`, `get_alerts`, `get_insights`
- **Analytics**: `get_gsc_overview`, `get_gsc_queries`, `get_gsc_pages`, `get_content_gaps`
- **Optimizations**: `list_optimizations`, `start_optimization`, `get_optimization`, `apply_optimization`, `get_optimization_insights`
- **Webhooks**: `list_webhooks`, `create_webhook`, `get_webhook`, `update_webhook`, `delete_webhook`

#### Resources
- `frase://sites` - List all sites
- `frase://sites/{id}` - Individual site details
- `frase://content` - List all content
- `frase://content/{id}` - Individual content with body
- `frase://briefs` - List all briefs
- `frase://briefs/{id}` - Individual brief with outline

#### Prompts
- `create_seo_article` - Full SEO article creation workflow
- `optimize_content` - Content optimization workflow
- `keyword_research` - Keyword research workflow
- `competitor_analysis` - Competitor analysis workflow
- `content_audit` - Site content audit workflow

#### Features
- API client with retry logic and exponential backoff
- Response caching (60s for lists, 5min for resources)
- Markdown-formatted responses for better readability
- Debug mode via `FRASE_MCP_DEBUG` environment variable
