# Connecting Microsoft Copilot to the Reltio MCP Server

Steps to connect a Microsoft Copilot Studio agent to the Reltio AgentFlow MCP
server, based on Reltio's official documentation, with screenshots captured
while performing the integration.

> **Note on privacy:** As with the main guide, tenant/environment values and
> personal identifiers here are replaced with placeholders (e.g. `<environment>`).

## Prerequisites

Per Reltio's official documentation:

> "You must have access to Microsoft Copilot Studio or Microsoft 365 Copilot
> Agent Builder.
> Your organization has an eligible Microsoft 365 subscription with Copilot
> enabled.
> You are signed in using a Microsoft ID.
> You have permission to create and manage agents in the Copilot
> environment, or administrator approval if agent creation is restricted.
> An administrator has enabled Copilot features in your Microsoft 365
> tenant.
> You have access to a Reltio environment that exposes its MCP server
> endpoint."
>
> Source: [Create a Microsoft Copilot agent to run Reltio MCP tools](https://docs.reltio.com/en/developer-resources/ai-integrations/reltio-model-context-protocol-mcp-server-at-a-glance/create-a-microsoft-copilot-agent-to-run-reltio-mcp-tools), Prerequisites

The same "which endpoint do I use" issue from the main guide applies here too;
see [../README.md](../README.md#common-issue-which-endpointurl-do-i-authenticate-against)
for that background.

## Steps

The steps below are Reltio's official procedure, quoted directly, with a
screenshot placeholder under each one to fill in as the integration is
performed.

1. Go to Microsoft Copilot Studio (`copilotstudio.microsoft.com`) and sign in
   using your Microsoft ID account.

2. Select **+ New agent**.

3. On the **Start building your agent** page, select the **Configure** tab.

4. Configure the agent details:
   - In the **Name** field, enter a name for your agent (for example,
     `Reltio Data Explorer`).
   - In the **Description** field, enter a description for the agent.
   - In the **Instructions** field, define the agent's behavior and scope.

5. Select **Create** to provision the agent.

6. Select the **Tools** tab, and then select **Add tool**.

7. In the **Add a tool** dialog, select **Model Context Protocol**.

8. Configure the MCP server connection:
   - Enter a server name and description.
   - In the **Server URL** field, enter the Reltio MCP server endpoint:

     ```
     https://<environment>.reltio.com/ai/tools/mcp/
     ```

     Replace `<environment>` with your tenant domain.
   - Select **OAuth 2.0** as the authentication method and provide the
     required credentials (Client ID, Client Secret, Authorization URL,
     Token URL, and scopes).

9. Select **Create**, and then select **Connect** to establish the
   connection.

10. When the connection succeeds, verify that the MCP tools appear in the
    **Tools** tab.

> **Note:** Copilot Studio doesn't support renewing an expired tool
> connection. When the connection expires, create a new connection using the
> same tool in Connection Manager. You can have multiple connections for the
> same agent tool.

11. Select **Test** and run a sample query to validate that the agent can use
    the MCP tools.

Source: [Create a Microsoft Copilot agent to run Reltio MCP tools](https://docs.reltio.com/en/developer-resources/ai-integrations/reltio-model-context-protocol-mcp-server-at-a-glance/create-a-microsoft-copilot-agent-to-run-reltio-mcp-tools)

## Result

Once connected, the Copilot agent can discover and run Reltio MCP tools to
read and act on data in the Reltio environment, the same tools used from
Claude Code in the main guide.

## Screenshots

_To be added as the integration is performed._
