---
name: audn-login
description: Authenticate with Audn.ai for red-team security testing. Sets up OAuth login or API token. Run this before using any other audn skill. Use when the user needs to log in, authenticate, connect to Audn, or set up their API credentials.
allowed-tools: Bash(claude mcp *) Bash(curl *) Bash(test *) Bash(echo *) Bash(export *)
---

# Audn.ai Authentication Setup

Set up authentication to the Audn.ai API. This must be done before using any other audn skill.

## Step 1 — Check if MCP server is already configured

Run this command to check:

```bash
claude mcp get audn-redteam 2>/dev/null && echo "EXISTS" || echo "NOT_FOUND"
```

## Step 2 — Register the MCP server (if NOT_FOUND)

If the MCP server is not configured, register it now. Run this exact command:

```bash
claude mcp add --transport http audn-redteam https://mcp.audn.ai/mcp/audn-rn8sx
```

This adds the Audn.ai MCP server with OAuth 2.1 support. Claude Code will auto-discover the OAuth endpoints at `https://mcp.audn.ai/.well-known/oauth-authorization-server/mcp/audn-rn8sx` and handle Dynamic Client Registration automatically.

## Step 3 — Authenticate

After registering the MCP server, tell the user:

> **Run `/mcp` now, select `audn-redteam`, and click "Authenticate".**
> A browser window will open — log in at auth.audn.ai (or sign up at https://audn.ai if you don't have an account).
> After approving, your tokens are stored securely in the system keychain and refresh automatically.

Wait for the user to complete authentication.

## Step 4 — Verify

After the user authenticates, check the MCP server status:

```bash
claude mcp get audn-redteam
```

If it shows `connected`, authentication is complete. All 34 Audn API tools are now available as MCP tools.

If the user has trouble with OAuth, fall back to API token method (see below).

## Fallback — API Token Method

If OAuth doesn't work (firewall, SSH, container), use an API token instead:

1. Tell the user to generate a token at: https://audn.ai/dashboard/settings (API Keys section)
2. Then run in this session:

```bash
export AUDN_API_TOKEN="<their-token>"
```

3. For persistence, add to shell profile:

```bash
echo 'export AUDN_API_TOKEN="<token>"' >> ~/.zshrc
```

4. Verify it works:

```bash
curl -s -H "Authorization: Bearer $AUDN_API_TOKEN" https://audn.ai/api/user/profile
```

Should return user profile with `success: true`.

## Output

After successful authentication (either method), report:

```
## Authenticated with Audn.ai

**Method**: MCP OAuth / API Token
**Status**: Connected

Ready to use audn skills:
- /audn-red <api-url> — Full red-team audit
- /audn-quick-scan <api-url> — Quick vulnerability scan
- /audn-agent-setup — Register an agent
- /audn-vuln-monitor — Check vulnerabilities
```

$ARGUMENTS
