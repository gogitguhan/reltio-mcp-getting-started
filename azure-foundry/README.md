# Connecting Microsoft Foundry to the Reltio MCP Server

Steps to connect an agent in Microsoft Foundry (formerly Azure AI Foundry)
to the Reltio AgentFlow MCP server, based on Microsoft's documentation,
with screenshots captured while performing the integration.

> **Note on privacy:** tenant IDs, client IDs, secrets, and personal
> identifiers are replaced with placeholders (e.g. `<environment>`).

## How it works

Foundry Agent Service connects to remote MCP servers through the **MCP
tool**. For a server like Reltio's, which signs each user in with OAuth,
Foundry uses **OAuth identity passthrough**:

> "When you use OAuth identity passthrough, Agent Service generates a
> consent link the first time a particular user needs to authorize access.
> After the user signs in and consents, the agent can discover and invoke
> tools on the MCP server with that user's credentials."
>
> Source: [Set up MCP server authentication](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/mcp-authentication)

Foundry offers **managed OAuth** (the OAuth app is managed by Microsoft or
the MCP server publisher) and **custom OAuth** (you bring your own OAuth
app registration). Reltio uses custom OAuth.

## Prerequisites

From Microsoft's documentation:

- An Azure subscription with an active Microsoft Foundry project.
- The **Foundry User** role on the project to create and test agents, and
  the **Foundry Project Manager** role to create the project connection that
  stores the MCP authentication.
- To use OAuth identity passthrough, people using the agent need at least
  the **Foundry Agent Consumer** role, and their Microsoft Entra tenant must
  match the Foundry project's tenant.

Source: [Connect agents to MCP server endpoints](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/model-context-protocol)

From Reltio:

- The Reltio MCP server endpoint for your environment:
  `https://<environment>.reltio.com/ai/tools/mcp/`. `<environment>` is the
  Reltio environment your tenant runs on, **not** the tenant ID (see the
  [main guide](../README.md#common-issue-which-endpointurl-do-i-authenticate-against)).
- A **Client ID and secret issued by Reltio** for this integration. Reltio's
  engineering team confirmed that the login page relies on specific
  credentials for identification, and that other credentials won't work.
- Foundry's **redirect URL** added to Reltio's login page allowlist (see
  step 5).

## Steps

### 1. Control test: confirm MCP tools work in your Foundry project

In Copilot Studio, MCP tool creation failed in one environment for reasons
unrelated to Reltio (see [../copilot/README.md](../copilot/README.md)).
Before configuring Reltio, confirm that MCP works in your Foundry project
by adding Microsoft's own public MCP server, which needs no
authentication:

- Server URL: `https://learn.microsoft.com/api/mcp`
- Authentication: unauthenticated

If this succeeds, the project is ready for the Reltio setup.

### 2. Create or open an agent

In the [Foundry portal](https://ai.azure.com), open your project and create
an agent, or open an existing one.

### 3. Add the MCP tool

Per Microsoft's documentation: "Connect a tool, go to **Custom**, and then
select **MCP**. Provide a name and MCP server endpoint, and then select
**OAuth Identity Passthrough**."

- **Name**: e.g. `reltio-agentflow`
- **MCP server endpoint**: `https://<environment>.reltio.com/ai/tools/mcp/`
- **Authentication**: OAuth Identity Passthrough

### 4. Configure custom OAuth

Fields Foundry asks for, per Microsoft's documentation, with the Reltio
values:

| Field | Required | Reltio value |
|---|---|---|
| Client ID | Yes | Issued by Reltio |
| Client secret | Optional | Issued by Reltio |
| Auth URL | Yes | `https://login.reltio.com` |
| Token URL | Yes | `https://login.reltio.com/token` |
| Refresh URL | Yes | `https://login.reltio.com/token` (Microsoft: "if you don't have a separate refresh URL, you can use the token URL instead") |
| Scopes | Optional | Leave blank unless Reltio specifies scopes |

The Auth and Token URLs match the values published in Reltio's OAuth
metadata at
`https://<environment>.reltio.com/ai/tools/.well-known/oauth-authorization-server`.

### 5. Register Foundry's redirect URL with Reltio

> "If you use custom OAuth, you receive a redirect URL after configuration.
> Add the redirect URL to your OAuth app so Agent Service can complete the
> flow."
>
> Source: [Set up MCP server authentication](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/mcp-authentication)

For Reltio, redirect URLs are managed on Reltio's login page allowlist, not
through the Console or the client management API. Send the redirect URL to
Reltio's AgentFlow engineering team to add.

### 6. Test the agent

Send the agent a prompt that requires a Reltio tool. The first time, the
response includes an `oauth_consent_request` with a consent link. Open it,
sign in to Reltio, and consent. After that, tool calls run with your Reltio
credentials, and you won't be asked again.

## Screenshots

Screenshots are added inline next to each step as the integration is
performed.
