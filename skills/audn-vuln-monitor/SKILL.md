---
name: audn-vuln-monitor
description: List, triage, and monitor vulnerabilities detected across your AI agents. Shows severity ratings, categories, status, and remediation recommendations. Use when checking security posture or reviewing findings.
---

# Audn Vulnerability Monitor

View and triage vulnerabilities detected by Audn.ai security campaigns.

## Authentication

Bearer token via `AUDN_API_TOKEN` env var. Base URL: `https://audn.ai`.
All requests include header: `Authorization: Bearer $AUDN_API_TOKEN`.

## Workflow

### List Vulnerabilities

```
GET /api/vulnerabilities
```

Returns an array of findings with: `id`, `title`, `severity` (critical/high/medium/low/info), `status` (open/in_progress/resolved/suppressed), `category`, `description`, `detected_at`, `recommendations`.

### List Recent Test Results

For deeper context:

```
GET /api/results?limit=20&status=fail&sortOrder=desc
```

### Get Specific Result Details

```
GET /api/results/<result_id>
```

Returns full transcript, analysis, attack details, and linked campaign/agent/scenario.

### Check Campaign Results

```
GET /api/campaigns/<campaign_id>
```

## Output Format

### Summary View

```
## Vulnerability Dashboard

**Total**: X findings | **Critical**: A | **High**: B | **Medium**: C | **Low**: D

### Open Vulnerabilities

| # | Severity | Category | Title | Agent | Detected |
|---|----------|----------|-------|-------|----------|
| 1 | CRITICAL | Prompt Injection | System prompt extractable via... | GPT-4 Bot | 2026-04-10 |
| 2 | HIGH | Data Extraction | PII leakage through indirect query | Claude Assistant | 2026-04-09 |
| 3 | MEDIUM | Social Engineering | Partial compliance with authority claim | Internal Bot | 2026-04-08 |

### Remediation Priority

1. **[CRITICAL]** <title> — <recommendation>
2. **[HIGH]** <title> — <recommendation>
```

### Detailed View (when user asks about a specific vulnerability)

```
## Vulnerability Detail — <title>

**Severity**: CRITICAL
**Category**: Prompt Injection
**Status**: open
**Detected**: <date>
**Agent**: <agent name>

### Description
<full description>

### Recommendations
1. <recommendation 1>
2. <recommendation 2>

### Related Test Result
**Campaign**: <campaign name>
**Scenario**: <scenario name>
**Result**: FAIL
**Transcript excerpt**: <relevant portion of conversation>
```

## Trend Analysis

If user asks about trends, fetch multiple data points:

```
GET /api/results?limit=100&sortBy=created_at&sortOrder=desc
```

Group by week/category and report:
- Vulnerability count over time
- Most common categories
- Agents with most findings
- Improvement/regression trends

$ARGUMENTS
