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

- The `oauth-protected-resource` endpoint returning 401 is not a blocker.
  Reltio's engineering team confirmed that clients are meant to use the
  sibling `oauth-authorization-server` metadata endpoint, which works
  correctly, and Claude Code's client does exactly that.
- OAuth demonstrably works end to end on this environment, so a
  Copilot Studio failure can't be explained by a general OAuth problem.

## Conclusion

The Reltio AgentFlow MCP server and its OAuth flow work correctly from
Claude Code.

An earlier version of this page concluded that Reltio's auth server only
accepts `localhost` redirect URIs, and that this was what blocked Copilot
Studio. **That conclusion was wrong.** It was based on a test that used a
placeholder redirect URI (`https://example.com/callback`), not Copilot
Studio's real one. Reltio's engineering team confirmed that redirect URIs
for Copilot Studio, Claude, and ChatGPT are allowlisted on Reltio's login
page.

The Copilot Studio failure turned out to be unrelated to Reltio. It
reproduces with Microsoft's own public MCP server and no authentication
at all. See the [control test in the Copilot guide](../copilot/README.md#control-test-a-non-reltio-mcp-server).
