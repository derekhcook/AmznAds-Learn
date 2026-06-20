# Amazon Ads Learning App

An interactive, browser-based course for learning Amazon Advertising well enough to build an AI agent that manages campaigns via the Amazon Ads MCP Server.

## How to Open

**Option 1 — Double-click** `index.html` in Finder. Opens directly in your browser, no setup needed.

**Option 2 — Local server** (avoids any browser file:// restrictions):
```bash
python3 -m http.server 3456
```
Then visit `http://localhost:3456`

## What You'll Learn

9 modules, 38 lessons, covering everything needed to build the agent:

| Module | Topics |
|--------|--------|
| 1 — Amazon Ads Fundamentals | Ad types, campaign hierarchy, bidding, CTV, audio, metrics |
| 2 — Campaign Targeting | Keywords, match types, product targeting, audiences, AI-powered targeting (2026) |
| 3 — Amazon Ads API | REST API overview, v1 vs v3 headers, authentication (LWA/OAuth2), SP API in depth |
| 4 — MCP & Tool Use | MCP protocol (JSON-RPC 2.0), Amazon Ads MCP Server, tool schemas |
| 5 — Claude API Integration | Tool use loop, adaptive thinking, Go SDK usage |
| 6 — Amazon Marketing Cloud | AMC SQL queries, clean room, audience building, 25-month lookback |
| 7 — Building the AI Agent | Agent architecture, error handling, token refresh, retry logic |
| 8 — Production Readiness | Cost estimation, rate limits, logging, monitoring |
| 9 — Advanced Topics | Bulk operations, scheduling, reporting pipelines |

Content is accurate as of **June 2026** — includes Amazon Ads API v1 (GA December 2025), AMC 25-month lookback, Sponsored TV self-serve, and February 2026 AI targeting updates.

## End Goal

By the end of this course you'll have the knowledge to build a Go CLI tool + AI agent that:
- Authenticates with Amazon Ads via LWA (Login with Amazon)
- Connects to the Amazon Ads MCP Server
- Uses Claude (claude-opus-4-8) with tool use to manage campaigns, pull reports, and optimize bids
- Can be run from the command line: list campaigns, generate reports, create/update campaigns

## Step 0 — What to Build First

After completing the course, the first concrete deliverable (as outlined by the team) is a **Go CLI library** that connects to the Amazon Ads MCP Server and supports:

1. `list-campaigns` — show active campaigns and their status
2. `get-report` — pull a performance report for a date range
3. `create-campaign` — set up a new Sponsored Products campaign

This library becomes the foundation the AI agent calls into. Keep it simple: one binary, environment-variable configuration, JSON output.

## Tech Stack (for the Agent)

- **Language**: Go
- **AI**: Claude API (`claude-opus-4-8`, adaptive thinking)
- **Amazon integration**: Amazon Ads MCP Server
- **Auth**: LWA OAuth2 (client credentials or authorization code flow)
