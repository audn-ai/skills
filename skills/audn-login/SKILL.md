---
name: audn-login
description: Authenticate with Audn.ai for red-team security testing. Sets up OAuth login or API token. Run this before using any other audn skill. Use when the user needs to log in, authenticate, connect to Audn, or set up their API credentials.
---

# Audn.ai Authentication Setup

Set up authentication to the Audn.ai API. This must be done before using any other audn skill.

## Method 1 — Claude Code MCP OAuth (Recommended)

If running in **Claude Code**, the plugin bundles an MCP server with OAuth. Authentication is automatic:

1. Tell the user to run `/mcp` in Claude Code
2. They should see `audn-redteam` in the server list
3. Select it and click "Authenticate" — a browser window opens
4. They log in at `auth.audn.ai` (or sign up at https://audn.ai)
5. After approving, tokens are stored securely in the system keychain
6. All audn MCP tools become available automatically

If the MCP server isn't showing, the user needs to install this repo as a plugin first:

```bash
claude --plugin-dir /path/to/audn-skills
```

Or add the MCP server manually:

```bash
claude mcp add --transport http audn-redteam https://mcp.audn.ai/mcp/audn-rn8sx
```

Then run `/mcp` and authenticate.

## Method 2 — API Token (All Agents)

For **Cursor, Codex, Windsurf, Cline**, or any non-Claude Code agent:

### Step 1 — Get a Token

Option A: If user has an Audn.ai account, they can generate a token at:
https://audn.ai/dashboard/settings (API Keys section)

Option B: Create one programmatically (if already authenticated via another method):

```
POST https://audn.ai/api/api-keys
Authorization: Bearer <existing-token>
Content-Type: application/json

{
  "name": "CLI Agent — <agent name> — <date>",
  "permissions": ["campaigns:read", "campaigns:write", "agents:read", "agents:write", "scenarios:read", "scenarios:write", "results:read", "reports:read", "reports:write", "vulnerabilities:read"]
}
```

The response includes a `secret` field — this is the API token. It is only shown once.

### Step 2 — Set Environment Variable

Tell the user to set:

```bash
export AUDN_API_TOKEN="<their token>"
```

Add to shell profile for persistence:

```bash
echo 'export AUDN_API_TOKEN="<token>"' >> ~/.zshrc  # or ~/.bashrc
```

### Step 3 — Verify

Test the connection:

```
GET https://audn.ai/api/auth/session
Authorization: Bearer $AUDN_API_TOKEN
```

Should return `{ "success": true, "data": { "authenticated": true, ... } }`.

If it fails with 401, the token is invalid or expired.

## Method 3 — OAuth PKCE Flow (Advanced / CLI)

For automated setups or custom tooling, use the OAuth 2.1 Authorization Code flow with PKCE:

**OAuth Endpoints:**
- Authorization: `https://auth.audn.ai/authorize`
- Token: `https://auth.audn.ai/oauth/token`
- Discovery: `https://mcp.audn.ai/.well-known/oauth-authorization-server/mcp/audn-rn8sx`
- Dynamic Client Registration: `https://auth.audn.ai/oidc/register`
- JWKS: `https://auth.audn.ai/.well-known/jwks.json`
- UserInfo: `https://auth.audn.ai/userinfo`

**Flow:**
1. Register a client via DCR (or use existing client_id)
2. Generate PKCE code_verifier + code_challenge (S256)
3. Redirect to authorization_endpoint with `response_type=code`, `code_challenge`, `code_challenge_method=S256`
4. User logs in, approves scopes
5. Exchange authorization code for access_token + refresh_token at token_endpoint
6. Use access_token as Bearer token for all API calls

**Supported scopes:** `openid`, `profile`, `email`, `offline_access`

## Verify Authentication Works

After any method, verify by fetching the user profile:

```
GET https://audn.ai/api/user/profile
Authorization: Bearer <token>
```

Expected response includes: `id`, `email`, `full_name`, `company`, `subscription` (plan, status), `stats` (total_campaigns, total_tests, vulnerabilities_found).

## Output

After successful authentication, report:

```
## Authenticated with Audn.ai

**User**: <email>
**Plan**: <subscription plan>
**Status**: <subscription status>
**Stats**: <total_campaigns> campaigns, <total_tests> tests, <vulnerabilities_found> vulnerabilities found

Ready to use audn skills. Try:
- /audn-red <api-url> — Full red-team audit
- /audn-quick-scan <api-url> — Quick vulnerability scan
- /audn-vuln-monitor — Check vulnerability dashboard
```

$ARGUMENTS
