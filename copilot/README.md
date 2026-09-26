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

<img src="screenshots/01-copilot-studio-home.png" width="700" alt="Copilot Studio home page">

3. On the **Start building your agent** page, select the **Configure** tab.

4. Configure the agent details:
   - In the **Name** field, enter a name for your agent (for example,
     `Reltio Data Explorer`).
   - In the **Description** field, enter a description for the agent.
   - In the **Instructions** field, define the agent's behavior and scope.

<img src="screenshots/02-new-agent-configure-tab.png" width="700" alt="Untitled Agent build page, Configure tab, with the Name field selected for editing and the default Instructions template shown">

5. Select **Create** to provision the agent.

<img src="screenshots/03-agent-created-with-instructions.png" width="700" alt="Reltio Data Explorer agent created, showing the custom Instructions field filled in">

6. Select the **Tools** tab, and then select **Add tool**.

> **UI discrepancy observed:** Reltio's documentation says to "select the
> Tools tab," but the actual Copilot Studio agent page (at time of writing)
> shows **Tools** as a section in a right-hand configuration panel (alongside
> Model, Skills, Knowledge, Connected agents, and Memory), not a separate top
> level tab. Select the **`+`** next to **Tools** in that panel instead.

<img src="screenshots/04-agent-panel-options.png" width="700" alt="Agent configuration panel showing Model, Skills, Tools, Knowledge, Connected agents, and Memory sections">

7. In the **Add a tool** dialog, select **Model Context Protocol**.

> **UI detail observed:** the actual **Add a tool** dialog (at time of
> writing) doesn't list "Model Context Protocol" under its **Featured** tab.
> There's a dedicated **MCP** tab alongside Featured, Connectors, and
> Workflows, but the direct path is the **`+ Add`** button in the top right
> corner: it opens a small dropdown with two options, **"Model Context
> Protocol (MCP)"** and **"Workflow."** Select **Model Context Protocol
> (MCP)**.

<img src="screenshots/05-add-a-tool-dialog.png" width="700" alt="Add a tool dialog showing Featured, MCP, Connectors, and Workflows tabs">

<img src="screenshots/06-add-tool-mcp-option.png" width="700" alt="The plus Add button's dropdown showing Model Context Protocol (MCP) and Workflow options">

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

Screenshots are captured inline above, next to the step they correspond to,
as the integration is performed.
