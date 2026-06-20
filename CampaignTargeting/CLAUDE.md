# CLAUDE.md — Amazon Ads Learning App

## What This Project Is

A single-file interactive learning app (`index.html`) for Amazon Advertising. No build step. No dependencies. Open in browser and click through 38 lessons across 9 modules. The goal is for a learner to understand enough to build an AI agent that manages Amazon Ads via the Amazon Ads MCP Server using Go and the Claude API.

**Do not add a build system, npm, Go binaries, or any file structure beyond `index.html`, `README.md`, and `CLAUDE.md` unless explicitly asked.**

## How to Run

```bash
# Serve locally (preferred):
python3 -m http.server 3456
# Then open http://localhost:3456

# Or just double-click index.html in Finder
```

The `.claude/launch.json` file configures Claude Code to auto-start the server on port 3456.

## File Structure

```
index.html          ← entire app: HTML + CSS + JS in one file (~4200 lines)
README.md           ← human-readable project overview
CLAUDE.md           ← this file
.claude/
  launch.json       ← starts python3 server on port 3456
  settings.local.json
```

Inside `index.html`, the content lives in a `CURRICULUM` array (starts around line 210). Each entry is one of:
- `type: "read"` — reading lesson with `content: () => \`...\``
- `type: "quiz"` — multiple-choice quiz with `questions: [...]`
- `type: "code"` — Go code challenge with `challenge: { starter, solution, checks }`

## Critical Accuracy Facts

When editing content, maintain these facts — they were verified against official docs as of June 2026:

### Amazon Ads API

**v1 (new, December 2025 GA):**
- Unified API for SP, SB, SD, STV, DSP
- Headers: `Amazon-Ads-ClientId` + `Amazon-Ads-AccountId` + `Authorization: Bearer {token}`

**v3 (legacy, still supported — no retirement date):**
- Headers: `Amazon-Advertising-API-ClientId` + `Amazon-Advertising-API-Scope` + `Authorization: Bearer {token}`

**Regional base URLs (same for both versions):**
- NA: `advertising-api.amazon.com`
- EU: `advertising-api-eu.amazon.com`
- FE: `advertising-api-fe.amazon.com`
- ⚠️ `advertising.amazon.com` is the web console — NOT an API base URL. Never use it in code.

**SP API v3 field formats (still apply to v3 endpoints):**
- Campaign/keyword/adGroup IDs are **strings** (`"9876543210"`), not integers
- `budgetType`: lowercase — `"daily"` or `"lifetime"` (NOT `"DAILY"`)
- `startDate`: `"YYYYMMDD"` format (NOT ISO 8601 `"YYYY-MM-DD"`)
- State values: UPPERCASE — `"ENABLED"`, `"PAUSED"`, `"ARCHIVED"`

### Amazon Marketing Cloud (AMC)

- AMC queries go to: `advertising-api.amazon.com/amc/reporting/workgroups/{id}/queries`
- ⚠️ `marketing-stream.amazon.com` is **Amazon Marketing Stream** — a completely different product (push-based Kinesis/SQS data stream). Never use it for AMC.
- Lookback window: **25 months** (extended November 2025 — was 13 months)
- Amazon Retail Purchases dataset: **5 years** of history

### MCP Server

Two options:
1. **Official Amazon Ads MCP Server** — `advertising.amazon.com/API/docs/en-us/mcp/mcp-overview` — 50+ tools, open beta February 2026
2. **Community** — `@marketplaceadpros/amazon-ads-mcp-server` — uses `BEARER_TOKEN` env var

MCP protocol: JSON-RPC 2.0. Methods: `tools/list`, `tools/call`. Preferred transport: **Streamable HTTP** (superseded SSE in 2025 spec revision). Stdio is fine for local dev.

Tool schemas use `"type": "string"` for campaign/keyword IDs (never `"integer"`).

### Claude API

- Default model: `claude-opus-4-8`
- Thinking: `{"type": "adaptive"}` (not `"enabled"` — that returns 400)
- Required headers: `x-api-key: {key}` + `anthropic-version: 2023-06-01`
- All current models (including Haiku 4.5) have **1M token context**
- Tool use loop: send request → `stop_reason: "tool_use"` → execute tool → send `tool_result` with matching `tool_use_id`

### Bidding Strategies (official names)

- "Fixed bids"
- "Dynamic bids — down only"
- "Dynamic bids — up and down" — raises bids up to **+100%** for top placements
- ⚠️ The 900% figure applies to the **Placement Modifier** (separate from the bidding strategy)

### DSP Minimums

- Self-serve: no enforced minimum (agencies recommend $10K+/month for meaningful data)
- Managed service: ~$35,000/quarter (NOT $50K/campaign)

### Sponsored TV

- Fully self-serve in US, UK, Brazil, Mexico, Canada as of 2026
- API path: `/stv/campaigns`

## Callout CSS Classes

```html
<div class="callout info">💡 informational note</div>
<div class="callout warn">⚠️ warning or common mistake</div>
<div class="callout tip">✅ best practice or pro tip</div>
```

## Go Code Patterns in Challenges

Struct field types to use:
```go
type Campaign struct {
    CampaignID string  // NOT int64 — v3 IDs are strings
    Name       string
    BudgetType string  // "daily" or "lifetime" (lowercase)
    StartDate  string  // "20240101" format
    State      string  // "ENABLED" / "PAUSED"
}
```

For `fmt.Fprintf` — always pass a valid `io.Writer`. Never pass `nil` (causes a panic at runtime).
