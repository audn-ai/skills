---
name: audn-quick-scan
description: Quick vulnerability scan of an AI agent endpoint. Runs only critical and high-severity attack scenarios for a fast security check. Use for a rapid smoke test before a full red-team audit.
---

# Audn Quick Scan

Run a fast adversarial scan using only critical and high-severity scenarios.

## Authentication

Bearer token via `AUDN_API_TOKEN` env var. Base URL: `https://audn.ai`.
All requests include header: `Authorization: Bearer $AUDN_API_TOKEN`.

## Workflow

### Step 1 — Register Agent (if not already registered)

If user provides an API URL:

```
POST /api/agents
Content-Type: application/json

{
  "name": "Quick Scan Target — <timestamp>",
  "agent_type": "text",
  "text_config": {
    "api_url": "$ARGUMENTS",
    "model": "auto",
    "auth_method": "bearer",
    "response_path": "$.choices[0].message.content",
    "temperature": 0.7,
    "max_tokens": 4096
  }
}
```

If user provides an agent ID, skip to step 2.

### Step 2 — Verify Agent

```
POST /api/agents/verify-text
{ "api_url": "<url>" }
```

If fails, stop and report.

### Step 3 — Get Critical Scenarios

```
GET /api/scenarios?severity=critical&limit=20
```

Also fetch high-severity:

```
GET /api/scenarios?severity=high&limit=10
```

Select up to 5 total scenarios — prioritize: prompt injection (1-2), jailbreak (1), data extraction (1), social engineering (1).

### Step 4 — Create and Execute Campaign

```
POST /api/campaigns
{
  "name": "Quick Scan — <date>",
  "description": "Rapid critical/high severity vulnerability scan",
  "agent_id": "<agent_id>",
  "scenario_ids": [<selected ids>]
}
```

Then execute:

```
POST /api/campaigns/<id>/execute
{ "execution_mode": "text", "useQStash": true }
```

### Step 5 — Poll and Report

Poll `GET /api/campaigns/<id>/status` every 10 seconds.

When complete, fetch results:

```
GET /api/results?campaignId=<id>&limit=50
```

## Output Format

```
## Quick Scan Results — <Agent>

**Grade**: X | **Pass**: Y/Z | **Vulnerabilities**: N
**Duration**: Xs

| Severity | Category | Result | Finding |
|----------|----------|--------|---------|
| CRITICAL | Prompt Injection | FAIL | System prompt extractable |
| HIGH | Jailbreak | PASS | Safety guardrails held |

**Recommendation**: <PASS — agent looks resilient / FAIL — run full audit with /audn-red>
```

$ARGUMENTS
