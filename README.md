# Audienceful MCP Server

<img src="assets/icon.png" alt="Audienceful" width="64" align="right">

Connect Claude, ChatGPT, Gemini, Cursor or any other AI assistant to your [Audienceful](https://www.audienceful.com) workspace. Your assistant can find contacts, build audiences, write emails, set up automations, read reports and send email, and it asks for your approval before anything goes out.

Audienceful hosts the server. There is nothing to install and no API key to copy: add one URL to your AI app and sign in.

```text
https://api.audienceful.com/v2/mcp
```

This repository holds the listing and setup docs. The server itself runs on Audienceful's infrastructure and is not open source.

| | |
|---|---|
| Endpoint | `https://api.audienceful.com/v2/mcp` |
| Transport | Streamable HTTP |
| Auth | OAuth 2.1 (authorization code + PKCE, Dynamic Client Registration) |
| Permissions | `mcp:read` (view), `mcp:write` (create and edit), `mcp:send` (send email) |
| Plans | Every Audienceful plan, including Free. It does not use your Audienceful AI credits. |
| Docs | [audienceful.com/help/api/mcp](https://www.audienceful.com/help/api/mcp) |

## Install

When you connect, you sign in to Audienceful, pick a workspace and choose what the assistant can do: view only, create and edit, or send email too. You can disconnect at any time from **Apps → Installed apps** in Audienceful.

### Claude (claude.ai and Claude Desktop)

**Customize → Connectors → Add → Add custom connector**, then paste `https://api.audienceful.com/v2/mcp`. On Team and Enterprise plans, an owner adds the connector under organization settings first. [Full guide](https://www.audienceful.com/integrations/claude)

### Claude Code

```bash
claude mcp add --transport http --scope user audienceful https://api.audienceful.com/v2/mcp
```

Then run `/mcp` inside Claude Code to sign in.

### ChatGPT

Turn on Developer mode, create an app with the URL above and choose OAuth for authentication. [Full guide](https://www.audienceful.com/integrations/chatgpt)

### Codex CLI

```bash
codex mcp add audienceful --url https://api.audienceful.com/v2/mcp
```

### Gemini CLI

```bash
gemini mcp add --transport http --scope user audienceful https://api.audienceful.com/v2/mcp
```

Then run `/mcp auth audienceful`. [Full guide](https://www.audienceful.com/integrations/gemini)

### Cursor

Add this to `~/.cursor/mcp.json`, or use the **Add to Cursor** button on the [Cursor guide](https://www.audienceful.com/integrations/cursor):

```json
{
  "mcpServers": {
    "Audienceful": {
      "url": "https://api.audienceful.com/v2/mcp"
    }
  }
}
```

### VS Code

```json
{
  "servers": {
    "audienceful": {
      "type": "http",
      "url": "https://api.audienceful.com/v2/mcp"
    }
  }
}
```

### Mistral Le Chat

**Intelligence → Connectors → Add Connector → Custom MCP Connector**, name it `audienceful` and paste the URL. [Full guide](https://www.audienceful.com/integrations/mistral)

### Other apps

Any client that supports remote MCP servers with OAuth works. Give it the URL above.

## Example prompts

- "Give me an overview of my workspace: list size, recent sends and active automations."
- "Find customers who haven't ordered in 90 days and build a three-email win-back sequence for them. Leave it paused so I can review it."
- "Make an audience of everyone who joined this month and hasn't opened an email yet. How many people is that?"
- "Turn my latest blog post into a newsletter draft using my theme."
- "How did last week's newsletter do? Which links got the most clicks?"
- "Send me a test of the product launch draft, then schedule it for Tuesday at 9am to the Customers audience."

More in the [agent recipes](https://www.audienceful.com/agent-recipes).

## Tools

47 tools. Your assistant only sees the ones the permissions you granted allow.

| Area | View | Create and edit | Send |
|---|---|---|---|
| Workspace | `get_workspace_overview`, `list_fields`, `list_tags` | `create_field` | |
| Contacts | `search_contacts`, `get_contact` | `upsert_contact`, `unsubscribe_contact`, `delete_contact` | |
| Imports | `list_imports`, `get_import_status` | `import_contacts` | |
| Audiences | `list_audiences`, `get_audience`, `preview_audience`, `get_audience_rule_reference` | `create_audience`, `update_audience` | |
| Emails | `list_drafts`, `get_draft`, `list_email_templates`, `list_components`, `list_images`, `get_theme`, `get_email_block_reference` | `create_draft`, `edit_draft_blocks`, `replace_draft_content`, `edit_draft_html`, `update_draft_settings`, `duplicate_draft`, `update_theme` | |
| Automations | `list_automations`, `get_automation`, `list_custom_events`, `get_automation_reference` | `create_automation` | `set_automation_active`, `trigger_custom_event` |
| Reports | `list_sends`, `get_send_report`, `query_send_analytics` | | |
| Sending | `list_scheduled_sends` | | `send_test_email`, `preview_send`, `schedule_send`, `cancel_scheduled_send` |

Every tool has a title and `readOnlyHint` / `destructiveHint` / `idempotentHint` / `openWorldHint` annotations.

## Safety

An outside assistant can't show Audienceful's own confirm buttons, so the server enforces these rules itself:

- **Nothing sends without a preview.** `preview_send` returns the recipients, count and timing along with a one-time approval token. `schedule_send` accepts only that token. It is tied to exactly what was previewed, expires after 15 minutes and works once, so a retried call can't send twice.
- **New automations start paused.** Turning one on runs the same checks as the app.
- **Unsubscribed contacts stay unsubscribed.** Adding or importing a contact never re-subscribes them.
- **Edits are all-or-nothing.** If any part of a draft edit or audience rule is invalid, nothing changes and the assistant is told why.
- **Permissions are yours to set.** Each connection is limited to one workspace and the permissions you chose when you connected. You can disconnect it at any time.

## Privacy and support

- Privacy policy: [audienceful.com/legal/privacy](https://www.audienceful.com/legal/privacy)
- Terms: [audienceful.com/legal/terms](https://www.audienceful.com/legal/terms)
- Support: [support@audienceful.com](mailto:support@audienceful.com)

Found a problem with the server? [Open an issue](../../issues) or email support.
