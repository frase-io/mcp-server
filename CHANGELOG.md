# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- `get_run_status` names the current step instead of numbering it:
  `Current Step: 3 (Generate Content)` rather than `Current Step: 3`. The name
  is read from the run's own `step_results`, never resolved against the live
  playbook definition — a definition edited mid-run renumbers its steps, so
  index 2 can name a step an in-flight run never reached. Prefers the server's
  new `current_step_name` field when present and falls back to `step_results`,
  so it keeps working against a deployment that does not send it. Runs with no
  stored name still render the bare position. Response rendering only; no tool
  schema changed.

## [0.9.2]

- `create_brief`: reports an existing brief honestly ("Brief Already Exists"), warns when the research changed after generation (STALE), and adds `refresh: true` to rebuild via `POST /briefs/{id}/regenerate` (work-preserving: locked briefs fork; the original stays).
- `create_rule`: `competitor_filter` condition type advertised and documented ({"competitors": ["Acme"]}, config required) - accepted end-to-end by the API.
- `publish_content`: honest headers ("Draft Saved" / "Publish Scheduled" / "Content Published"); refuses the contradictory `status: "draft"` + `scheduled_at` combination (the server schedules a LIVE publish in that case) and validates `scheduled_at` as a datetime client-side.
- `bulk_cms_posts` / `bulk_set_comment_status`: `success` now mirrors the outcome (all-failed batches report `success: false`).

## [0.7.0] - 2026-08-10

### Added

- `audit_page` accepts `confirm_target_keyword_change`. A `keyword` that differs
  from the page's stored canonical target is refused with a target-keyword
  conflict, so without this flag the tool could never complete a deliberate
  retarget — it forwards to the same `allowTargetKeywordRetarget` gate the app
  surfaces use, and is only set after the user explicitly confirms.

### Fixed

- `get_site_health` rendered `Health Score: undefined/100` followed by four
  missing lines. It declared `health_score`, `total_pages`, `issues_count`,
  `critical_issues` and `warnings` — none of which `GET /api/v1/site-health`
  returns. It now reads the route's actual shape (`audit_stats`, which is null
  until the site has been audited, plus `issue_summary` and `average_scores`),
  and omits a line rather than printing `null/100` when a score is absent.
- `audit_page` no longer renders an absent score as `null%`. The audit score
  columns are all nullable, so declaring the wire types as bare `number` was an
  unchecked claim about the server; the render now drops the line, matching how
  `get_audit` already handles an unscored result.

## [0.6.3] - 2026-07-30

### Changed

- Version-only release so this package republishes against `@frase/core` 0.1.6.
  No functional changes.

## [0.6.2] - 2026-07-29

### Security

- Encode all request-path interpolations (`encodeURIComponent`). Caller-supplied
  ids were interpolated raw, and `fetch` normalises `..` before the request
  leaves the process, so an id like `x/../../sites/<id>/cms-token` retargeted a
  tool onto a different endpoint. This let read-only-annotated tools reach a
  privileged endpoint, and pointed destructive tools at a resource the user had
  not approved. The verb was always preserved and every route is org-scoped, so
  this was retargeting rather than privilege escalation or cross-tenant access.
- Validate `playbook_id` / `run_id` as UUIDs rather than non-empty strings.

### Added

- Tool annotations (`title`, `readOnlyHint`/`destructiveHint`) on the remote HTTP
  transport, required by the Claude Connectors Directory. The stdio transport is
  deliberately unannotated: the extra ~13.5 KB would cross the 64 KiB Windows
  anonymous-pipe limit that `npx` runs on.

### Fixed

- Validation failures now report the field and the problem instead of dumping
  zod's raw issue array (which included the full UUID regex).
- `manifest.json`, `server.json` and `package.json` tool counts corrected to 121
  and guarded by a test so they cannot drift again.

### Removed

- Stopped tracking built desktop-extension artifacts (`*.mcpb`,
  `dist/mcpb-bundle.js`). The committed copies had gone stale, declaring 64
  tools and missing the fixes above.

## [0.3.7] - 2026-06-04

### Fixed
- `analyze_serp`: People Also Ask entries rendered as `[object Object]` and several fields showed `undefined`. PAA questions and result fields now render correctly.

### Changed
- Bumped `@frase/core` dependency to `0.1.5`, which adds the `ToolResult.hidden`/`reason` hide-mirror fields and `McpToolDefinition.inputSchema.additionalProperties` that the AI-visibility tools rely on. (The tools had used these since late May, but core was never republished — the build only typechecks at publish time, so it surfaced now.)
- Updated runtime dependencies (`hono`, `@modelcontextprotocol/sdk`) and upgraded the test toolchain (`vitest` 2.x → 4.x) to clear a critical security advisory.
- `server.json` `version` (and its nested npm package `version`) corrected from `0.3.5` to track the package version — it had drifted behind `package.json`/`manifest.json` since 0.3.6.

## [0.3.6] - 2026-05-18

### Added
- `create_brief`, `update_brief`, `list_templates`, `create_template`: `content_type` now accepts the canonical 16-type taxonomy in addition to the legacy 5-bucket values. New types: `blog_post`, `page`, `faq`, `glossary`, `pillar`, `how_to`, `listicle`, `case_study`, `tutorial`, `news`, `product_update`, `data_report`, `event`, `resource` (alongside the existing `comparison` and `landing`). Prefer the specific types — they produce correctly-shaped outlines and type-appropriate word counts via the platform's spec system.

### Changed
- **`create_brief.target_word_count`** is now optional with no client-side default. When omitted, the server uses the content type's recommended length (e.g. ~1000 for `faq`, ~1500 for `glossary`, ~3500 for `pillar`) instead of a fixed 2000. If your integration assumed 2000, either pass an explicit value or be ready to receive different per-type defaults in the response.
- **`create_brief.target_word_count`** lower bound widened from 500 to 80 to support short-form types (`resource`, `product_update`, `event`).
- **`create_brief.content_type`** default changed from `"blog"` to `"blog_post"` (canonical 16-type equivalent).
- **Response shape**: brief responses now echo the **normalized 16-type** `content_type`, not the input string. A client sending `content_type: "blog"` will receive `content_type: "blog_post"` in the response; `"guide"` → `"how_to"`, `"product"` → `"product_update"`. Update any client code that keys off the returned `content_type` field.
- Validation error messages for `content_type` now list 16-type values.

### Deprecated
- Legacy 5-bucket `content_type` values (`blog`, `guide`, `landing`, `product`, `comparison`) are still accepted but normalized to 16-type equivalents at the API boundary. New integrations should use the 16-type values directly.

### Fixed
- `SERVER_VERSION` constant and `manifest.json` `version` now correctly report the package version (previously stuck at `0.2.2` despite `package.json` being at `0.3.5`). Clients reading the MCP `initialize` response will now see the actual server version.

## [0.2.0] - 2026-03-08

### Added
- **Prompt: `content_pipeline`** — Full autonomous content lifecycle workflow (research → brief → write → optimize → publish → monitor)
- **Prompt: `content_watchdog`** — Performance monitoring and auto-fix workflow for ranking drops
- **Enhanced `create_seo_article` prompt** — Now includes optimization scoring, revision loop, and CMS publishing steps
- Multi-tool setup instructions for **Cursor**, **Windsurf**, and **VS Code Copilot**
- `target_language` parameter on `create_brief` tool (ISO 639-1 code, e.g., 'en', 'de', 'pt')
- `target_country` parameter on `create_brief` tool (ISO 3166-1 code, e.g., 'us', 'de', 'br')

### Fixed
- `SERVER_VERSION` constant now correctly reads `0.2.0` (was stuck at `0.1.0` since initial release)

### Changed
- Version bump to 0.2.0 across package.json, manifest.json, and server constant

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
