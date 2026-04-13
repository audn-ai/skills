# audn/skills

**Red-team your AI agents from any coding IDE.** Automated adversarial security testing for voice and text AI agents — jailbreaks, prompt injection, data extraction, social engineering, compliance reporting.

Works with **Claude Code, Cursor, Codex, Windsurf, Cline, Amp**, and [40+ other coding agents](https://agentskills.io).

---

## Prerequisites

**Audn.ai account** — [sign up free](https://audn.ai)

---

## Quick Start

### 1. Install skills

```bash
npx skills add audn-ai/skills --all -a claude-code
```

### 2. Authenticate (first time only)

In Claude Code, run:

```
/audn-login
```

This registers the Audn.ai MCP server. Then:

1. **Restart Claude Code** — exit with `/exit` or `Ctrl+C`, then run `claude` again (MCP servers load at startup)
2. Run `/audn-login` again
3. Run `/mcp` → select `audn-redteam` → click "Authenticate" → log in via browser

After login, tokens are stored in your system keychain and refresh automatically. No API keys needed.

**Not using Claude Code?** Generate a token at [audn.ai/dashboard/settings](https://audn.ai/dashboard/settings), then:
```bash
export AUDN_API_TOKEN="your-bearer-token"
```

### 3. Red-team your agent

```
/audn-red https://api.openai.com/v1/chat/completions
```

That's it. Audn registers your agent, selects attack scenarios, runs the campaign, and delivers a full vulnerability report.

## Install

```bash
# Single skill
npx skills add audn-ai/skills --skill audn-red -a claude-code

# All skills
npx skills add audn-ai/skills --all -a claude-code

# Multiple agents at once
npx skills add audn-ai/skills --skill audn-red -a claude-code -a cursor -a codex

# Global install (available in all projects)
npx skills add audn-ai/skills --skill audn-red -a claude-code -g

# Claude Code plugin mode
claude --plugin-dir ./  # from this repo
```

## Skills

### `audn-login` — Authenticate with Audn.ai

Set up authentication. On Claude Code, triggers OAuth browser login via the bundled MCP server. On other agents, guides API token setup.

```
/audn-login
```

### `audn-red` — Full Red-Team Audit

End-to-end adversarial security campaign. Registers your agent, verifies connectivity, selects attack scenarios from the library, creates and executes a campaign, monitors progress, retrieves results, checks for vulnerabilities, and generates a report.

```
/audn-red https://api.openai.com/v1/chat/completions
/audn-red https://api.anthropic.com/v1/messages
/audn-red https://your-internal-api.com/chat
```

**Output**: Security grade (A-F), vulnerability table, attack category breakdown, remediation recommendations.

### `audn-quick-scan` — Fast Vulnerability Scan

Runs only critical and high-severity scenarios for a rapid smoke test. Takes minutes instead of a full audit.

```
/audn-quick-scan https://api.openai.com/v1/chat/completions
```

### `audn-compliance-report` — Compliance Reports

Generate ISO 42001, SOC2, vulnerability, executive, or general compliance reports from completed campaign data.

```
/audn-compliance-report <campaign-id> iso_42001
/audn-compliance-report <campaign-id> soc2
/audn-compliance-report <campaign-id> executive
```

### `audn-agent-setup` — Register & Verify Agents

Register text API agents (OpenAI, Anthropic, custom LLMs) or voice agents (Twilio, Genesys, Amazon Connect) for testing.

```
/audn-agent-setup https://api.openai.com/v1/chat/completions
/audn-agent-setup +14155551234
```

### `audn-scenario-builder` — Custom Attack Scenarios

Design custom adversarial attack scenarios — prompt injection, jailbreak, data extraction, social engineering, emotional manipulation, role confusion.

```
/audn-scenario-builder
```

### `audn-vuln-monitor` — Vulnerability Dashboard

List and triage vulnerabilities across all your agents. Severity ratings, categories, remediation recommendations, trend analysis.

```
/audn-vuln-monitor
```

## What Gets Tested

| Attack Category | What It Tests |
|----------------|---------------|
| Prompt Injection | System prompt override, instruction hijacking, context manipulation |
| Jailbreak | Safety guardrail bypass, persona override, DAN-style attacks |
| Data Extraction | PII leakage, credential exposure, system prompt extraction |
| Social Engineering | Authority impersonation, urgency manipulation, trust exploitation |
| Emotional Manipulation | Sympathy exploitation, guilt tripping, rapport abuse |
| Role Confusion | Identity confusion, role-play exploitation, context switching |

## API Coverage

These skills orchestrate the [Audn.ai API](https://audn.ai/docs/api):

| Resource | Operations |
|----------|-----------|
| Agents | Create, list, delete, verify (text + phone) |
| Campaigns | Create, execute (voice + text), status, delete |
| Scenarios | Library browse, custom create, update, delete, execute |
| Results | List with filters, detail view, stats |
| Reports | Generate (ISO 42001, SOC2, vulnerability, executive, compliance) |
| Vulnerabilities | List with severity/category/status |
| API Keys | Create, list, revoke, rotate |

## Also Works As a Claude Code Plugin

This repo doubles as a Claude Code plugin with a **bundled MCP server** that handles OAuth login automatically:

```bash
# Load directly (includes MCP server with OAuth)
claude --plugin-dir /path/to/this/repo

# Or add as marketplace
/plugin marketplace add audn-ai/skills

# Or install from official marketplace (after approval)
/plugin install audn-redteam@claude-plugins-official
```

When loaded as a plugin, the MCP server at `mcp.audn.ai` connects via OAuth 2.1 with PKCE and Dynamic Client Registration — no API keys needed. Run `/mcp` to authenticate via browser, then all 34 Audn API tools are available natively.

```bash
# Or add the MCP server standalone (without plugin)
claude mcp add --transport http audn-redteam https://mcp.audn.ai/mcp/audn-rn8sx
```

## How It Works

These skills are **instruction files** (SKILL.md) that teach your coding agent how to orchestrate the Audn.ai API. They contain zero proprietary code — just structured prompts that call the public API endpoints.

```
You (IDE) → Coding Agent → Skill Instructions → Audn.ai API → Results
```

The Audn.ai platform does the heavy lifting: adversarial prompt generation, multi-turn attack execution, vulnerability analysis, and compliance report generation.

## Contributing

Found a bug or want to improve a skill? PRs welcome.

1. Fork this repo
2. Edit skills in `skills/<skill-name>/SKILL.md`
3. Test locally: `npx skills add ./ --skill <name> -a claude-code -y`
4. Submit a PR

## License

MIT — see [LICENSE](LICENSE). Skills are open-source. The Audn.ai API is subject to [Audn.ai Terms of Service](https://audn.ai/terms).

---

**[audn.ai](https://audn.ai)** — Continuous security testing for AI agents.
