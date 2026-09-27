# Live Reconnect Test: Claude Code vs. Copilot Studio on the Same Environment

While troubleshooting why Microsoft Copilot Studio couldn't connect to the
Reltio AgentFlow MCP server on `test-usg` (see
[../copilot/README.md](../copilot/README.md)), we ran a live, side-by-side
test: could Claude Code, on the exact same environment, at the exact same
time, complete a fresh OAuth connection? The answer clarified the actual
root cause.

## Background

Earlier in this project, `claude mcp login reltio-mcp-server` was used to
connect Claude Code to this same environment (see the main
[../README.md](../README.md)). At some point after that initial setup, the
connection broke:

```
$ claude mcp get reltio-mcp-server
Status: ✘ Failed to connect
Issue: invalid_request: Invalid Authorization code
URL: https://test-usg.reltio.com/ai/tools/mcp/
```

Separately, we had confirmed (via direct `curl` testing) that this
environment's OAuth discovery metadata endpoint was broken:

```
GET https://test-usg.reltio.com/ai/tools/.well-known/oauth-protected-resource
→ HTTP/2 401
→ {"error_code":"Unauthorized","error":"No authorization bearer token detected"}
```

per the MCP Authorization spec, that endpoint must be publicly fetchable
without a token. Given Copilot Studio was also failing at the "Add MCP
server" step, this looked like the likely shared root cause for both
clients.

## The test

We re-ran `claude mcp login reltio-mcp-server` in a real interactive
terminal (this specific command needs genuine terminal interactivity;
it can't run through a non-interactive shell), while the
`oauth-protected-resource` endpoint above was still confirmed broken
(re-checked moments before the test).

**Result:**

```
$ claude mcp get reltio-mcp-server
Status: ✔ Connected
Type: http
URL: https://test-usg.reltio.com/ai/tools/mcp/
```

And a live tool call succeeded immediately after:

```
health_check_tool()
→ {"status": "ok", "message": "MCP server is running"}
```

## What this changes about the diagnosis

Claude Code's MCP client completed a full OAuth 2.0 Authorization Code +
PKCE flow, including whatever client registration step it needs, end to
end, on this exact environment, at a moment when the environment's
protected-resource metadata endpoint was still broken. That means:

- The broken `oauth-protected-resource` endpoint is a real bug (confirmed,
  reproducible, and worth reporting), but it is **not** a hard blocker for
  every MCP client. Claude Code's client tolerates it, most likely by
  falling back to the sibling `oauth-authorization-server` metadata
  endpoint directly (which we separately confirmed works correctly),
  rather than depending on the strictly-required-by-spec resource metadata
  step.
- Copilot Studio's specific failure is therefore **not** explained by a
  general "OAuth is broken here" story, since OAuth demonstrably works,
  right now, on this same environment.
- The actual, specific blocker for Copilot Studio is the one we confirmed
  directly earlier: Reltio's auth server only allows **`localhost`-style
  redirect URIs**, which is exactly what Claude Code's `mcp-remote` uses
  (it runs as a local process on the machine and listens on a local
  callback port). Microsoft Copilot Studio is cloud-hosted and must use a
  fixed HTTPS callback on Microsoft's own domain
  (`https://global.consent.azure-apim.net/redirect`), which Reltio's auth
  server rejects outright:

  ```
  GET https://login.reltio.com/?redirect_uri=https://example.com/callback&...
  → 302 location: /error?id=redirectUrlNotAllowed
  ```

## Conclusion

This test isolates the real, narrow gap: **Reltio's AgentFlow MCP OAuth
implementation on this environment supports desktop/CLI-style clients with
local redirect URIs, but has no supported mechanism (via dynamic client
registration or the admin Console) to allow a cloud-hosted platform's fixed
HTTPS redirect URI.** That's a specific, fixable, and well-evidenced gap to
raise with Reltio, distinct from (and independent of) the separate
protected-resource metadata bug.
