---
name: audn-agent-setup
description: Register and verify an AI agent on Audn.ai for security testing. Supports text API agents (OpenAI, Anthropic, custom LLMs) and voice agents (Twilio, Genesys, Amazon Connect). Use when the user wants to add a new agent to test.
---

# Audn Agent Setup

Register and verify a text or voice AI agent on the Audn.ai platform.

## Authentication

Bearer token via `AUDN_API_TOKEN` env var. Base URL: `https://audn.ai`.
All requests include header: `Authorization: Bearer $AUDN_API_TOKEN`.

## Determine Agent Type

Ask the user or infer from context:

- **Text agent**: User has an API URL (e.g., OpenAI, Anthropic, custom endpoint)
- **Voice agent**: User has a phone number for a voice bot

## Text Agent Setup

### Step 1 — Collect Configuration

Required: `api_url`
Optional: `model`, `auth_method`, `api_key`, `response_path`, `temperature`, `max_tokens`

Auto-detect platform from URL:
- `api.openai.com` → model: `gpt-4-turbo`, response_path: `$.choices[0].message.content`
- `api.anthropic.com` → model: `claude-3-opus-20240229`, auth_method: `custom_header`, custom_header_name: `x-api-key`, response_path: `$.content[0].text`
- Other → ask user for response_path or try `$.choices[0].message.content`

### Step 2 — Register

```
POST /api/agents
Content-Type: application/json

{
  "name": "<user-provided or auto-generated name>",
  "description": "<description of what this agent does>",
  "agent_type": "text",
  "text_config": {
    "api_url": "<url>",
    "model": "<model>",
    "auth_method": "<bearer|custom_header>",
    "temperature": 0.7,
    "max_tokens": 4096,
    "response_path": "<path>"
  }
}
```

### Step 3 — Verify

```
POST /api/agents/verify-text
Content-Type: application/json

{
  "api_url": "<url>",
  "auth_method": "<method>",
  "model": "<model>"
}
```

Report: success/failure, response preview, latency.

## Voice Agent Setup

### Step 1 — Register

```
POST /api/agents
Content-Type: application/json

{
  "name": "<name>",
  "description": "<description>",
  "platform": "<twilio|genesys|amazon_connect|custom>",
  "phone_number": "<+E.164 format>",
  "agent_type": "voice"
}
```

### Step 2 — Send Verification Code

```
POST /api/agents/verify-phone
Content-Type: application/json

{
  "phone_number": "<phone>",
  "action": "send_code",
  "agent_id": "<agent_id>",
  "channel": "sms"
}
```

### Step 3 — Verify Code

Ask user for the code they received, then:

```
POST /api/agents/verify-phone
Content-Type: application/json

{
  "phone_number": "<phone>",
  "action": "verify_code",
  "code": "<user-provided code>"
}
```

## Output

```
## Agent Registered

**Name**: <name>
**ID**: <agent_id>
**Type**: text / voice
**Platform**: <platform>
**Status**: active
**Verified**: yes/no
**Latency**: <X>ms (text agents)

Ready for testing. Run `/audn-red` or `/audn-quick-scan` to start a security audit.
```

## List Existing Agents

If user asks to list agents:

```
GET /api/agents?limit=20
```

Present as a table with id, name, type, platform, status.

## Delete Agent

```
DELETE /api/agents/<id>
```

$ARGUMENTS
