# Connecting an assistant to Buddhi

Read this when the user asks how to connect an assistant, or when no connector is present in the session. Give the user the steps for the app they use. You cannot complete these steps for them: each needs their own sign-in or their own key.

Two things recur below:

- **Buddhi address**: the company's Buddhi address, with no trailing slash. The MCP endpoint is that address followed by `/mcp`.
- **Key**: a personal key from Buddhi → **Gateway & Agents**. It starts with `crtx_`. Each person uses their own, so the assistant sees what that person may see and its proposals are attributed to them. Never ask the user to paste a key into the conversation; they enter it in the app's settings.

## Contents

- Claude Code (plugin)
- Claude on the web and desktop (connector)
- Claude Desktop, Cursor, Windsurf, VS Code (command line)
- Any other MCP app (manual entry)
- A product or script (Product API)
- Checking the connection
- When a connector stops working

## Claude Code (plugin)

One install adds the connection, two commands and reminders:

```
/plugin marketplace add <owner>/buddhi-claude-plugin
/plugin install buddhi-memory@buddhi
```

It asks for the Buddhi address and the key. `/mcp` then lists `plugin:buddhi-memory:buddhi` as connected.

## Claude on the web and desktop (connector)

No key to copy. In Claude: **Settings → Connectors → Add custom connector**, enter `<Buddhi address>/mcp`, then sign in to Buddhi and approve. Claude receives its own key through that sign-in.

## Claude Desktop, Cursor, Windsurf, VS Code (command line)

```
npm install -g @buddhi-ai/cli
buddhi login
```

`buddhi login` asks for the address and key, then writes the Buddhi entry into the settings of every one of these apps it finds on the machine: Claude Desktop, Claude Code (current project), Cursor (global and current project), Windsurf, and VS Code (current project). It merges into existing settings and keeps a backup of each file it changes. Restart the app afterwards.

`buddhi mcp install` repeats only the settings step. `buddhi whoami` shows which workspace the key belongs to.

## Any other MCP app (manual entry)

Add a remote (HTTP) MCP server with the endpoint and an authorization header:

```json
{
  "mcpServers": {
    "buddhi": {
      "url": "<Buddhi address>/mcp",
      "headers": { "Authorization": "Bearer crtx_..." }
    }
  }
}
```

VS Code uses a different shape, in `.vscode/mcp.json`:

```json
{
  "servers": {
    "buddhi": {
      "type": "http",
      "url": "<Buddhi address>/mcp",
      "headers": { "Authorization": "Bearer crtx_..." }
    }
  }
}
```

Apps that support sign-in for remote MCP servers can leave the header out and sign in to Buddhi instead.

## A product or script (Product API)

For code, not for a chat assistant. Base path `<Buddhi address>/api/v1`, with `Authorization: Bearer crtx_...`.

| Call | Purpose |
|---|---|
| `GET /me` | Which workspace and scopes this key has |
| `POST /recall` | Search memory |
| `POST /context` | Ready-made context for a task |
| `GET /facts`, `GET /facts/{key}` | Authoritative values |
| `POST /memory-candidates` | Propose a memory for review |
| `GET /memory-candidates?status=pending` | What this key proposed and what became of it |

Errors come back as `{ "error": { "code", "message" } }`. Keys are limited to 120 requests a minute. Keys created as strict also require signed requests; the full reference is in Buddhi's documentation under API.

## Checking the connection

- With the Buddhi tools: call `recall` with any question. A result or an empty list both mean it works.
- With the command line: `buddhi whoami`.
- With the API: `GET /api/v1/me`.

## When a connector stops working

| What you see | Likely cause | What to tell the user |
|---|---|---|
| Tools are missing | The app was not restarted, or the entry was not written | Restart the app; run `buddhi mcp install` again |
| Not authorised (401) | The key was revoked or has expired | Create a new key in Gateway & Agents and enter it again |
| A scope is denied | This person has no access to that scope | Request access in Buddhi under Access; do not retry with another scope |
| Too many proposals waiting (429) | The review inbox is full | Someone must review Agent Memory first |
| Too many requests (429) | The per-minute limit was reached | Wait a minute |
