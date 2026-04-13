---
name: audn-compliance-report
description: Generate ISO 42001, SOC2, vulnerability, executive, or compliance reports from Audn.ai campaign results. Use when the user needs compliance evidence, audit documentation, or executive summaries of security testing.
---

# Audn Compliance Report Generator

Generate compliance and security reports from completed campaign data.

## Authentication

Bearer token via `AUDN_API_TOKEN` env var. Base URL: `https://audn.ai`.
All requests include header: `Authorization: Bearer $AUDN_API_TOKEN`.

## Workflow

### Step 1 — Identify Campaign

If user provides a campaign ID, use it directly.

If not, list recent campaigns:

```
GET /api/campaigns?limit=10&status=completed
```

Present the list and let the user choose, or auto-select the most recent completed campaign.

### Step 2 — Determine Report Type

Available report types:

| Type | Use Case |
|------|----------|
| `iso_42001` | ISO/IEC 42001 AI management system compliance evidence |
| `soc2` | SOC 2 Type II security control evidence |
| `vulnerability` | Technical vulnerability assessment with CVSS-style ratings |
| `executive` | Board-level summary with risk posture and trends |
| `compliance` | General compliance overview combining multiple frameworks |

Parse user intent to select the right type. Default to `vulnerability` if unclear.
If user says "all" or "full", generate `vulnerability` + `executive` + `compliance`.

### Step 3 — Generate Report

```
POST /api/reports
Content-Type: application/json

{
  "type": "<report_type>",
  "campaign_id": "<campaign_id>",
  "name": "<Report Type> Report — <Campaign Name> — <date>"
}
```

Save returned `report_id`.

### Step 4 — Poll Until Complete

```
GET /api/reports/<report_id>
```

Poll every 5 seconds until `status` is `completed` or `failed`.

### Step 5 — Retrieve and Present

When complete, the response includes:
- `file_url` — downloadable report URL
- `metadata` — report contents/summary

## Output Format

```
## Report Generated

**Type**: ISO 42001 / SOC2 / Vulnerability / Executive / Compliance
**Campaign**: <campaign name>
**Status**: Completed
**Generated**: <timestamp>

**Download**: <file_url>

### Summary
<Extract key findings from metadata if available>

- Total tests: X
- Vulnerabilities: Y (critical: A, high: B, medium: C, low: D)
- Compliance status: passing/failing
- Risk level: low/medium/high/critical
```

If generating multiple reports, present each in sequence.

$ARGUMENTS
