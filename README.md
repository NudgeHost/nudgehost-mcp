# NudgeHost MCP server

NudgeHost is a file-hosting service for people and AI agents. Upload any file and get a link in seconds.

This repository describes NudgeHost's remote MCP server and holds its registry entry. The server runs at `https://www.nudgehost.com/api/mcp`, so there is nothing here to install or run. Full documentation is at [nudgehost.com/docs/api](https://www.nudgehost.com/docs/api).

## Registry entry

The server is listed in the official MCP Registry as `com.nudgehost/file-hosting`. [`server.json`](server.json) in this repository is that entry.

## You need an API key

The website takes uploads without an account. The MCP server is different, because its tools act on an account, so every request carries an API key. A free account is enough. Every plan can use the API, including the free one, and a free account can hold 2 keys at once.

1. Sign up at [nudgehost.com](https://www.nudgehost.com).
2. Open the API keys panel of your dashboard and create a key. It is shown once, so copy it before you close the panel.
3. Set the environment variable `NUDGEHOST_API_KEY` to that key, then start or restart your client.

Send every request to `www.nudgehost.com`. The apex, `nudgehost.com`, redirects to www, and the `Authorization` header is dropped on that hop, so a good key comes back 401. The setups below already use www.

## The four tools

| Tool | What it does |
|---|---|
| `share_text` | Publishes text as a file and returns the link, in one call. A filename ending in `.html` or `.htm` produces a page the recipient reads in the browser. A `.csv` or `.json` file opens in the table or JSON viewer. A `.md` or `.markdown` file shows as formatted text, with a Source view and a Copy source button, and HTML inside it shows as typed. Any other filename shows the text as plain text, with a Copy text button. Markdown and plain text also have the raw text at the link plus `/raw`. Content is capped at 2MB, measured in UTF-8 bytes. |
| `create_upload_url` | Starts an upload for a file the server does not hold, and returns the URL to send the bytes to. That URL expires after 300 seconds. |
| `complete_upload` | Finishes an upload once the bytes have been sent. Calling it twice is safe. A ZIP holding an index file becomes a hosted site at its own address. |
| `list_links` | Lists the account's links, newest first. `limit` takes a whole number from 1 to 100 and defaults to 50, and `cursor` fetches the next page. |

## Connecting a client

These setups are copied from [Connecting a client](https://www.nudgehost.com/docs/api#connecting-a-client) on the docs page.

Each setup below names an environment variable, NUDGEHOST_API_KEY, rather than the key itself, so the key stays out of the config file. Set that variable to an API key from your dashboard, then start or restart the client so it reads the new value.

**Claude Code.** Put this in .mcp.json in the folder you start Claude Code from. Tested 13 October 2026, on its default runtime and with MCP_PROTOCOL_NEGOTIATION set to legacy.

```json
{
  "mcpServers": {
    "nudgehost": {
      "type": "http",
      "url": "https://www.nudgehost.com/api/mcp",
      "headers": {
        "Authorization": "Bearer ${NUDGEHOST_API_KEY}"
      }
    }
  }
}
```

**Cursor.** Add this entry to ~/.cursor/mcp.json, which Cursor reads for every project. Tested 13 October 2026.

```json
{
  "mcpServers": {
    "nudgehost": {
      "url": "https://www.nudgehost.com/api/mcp",
      "headers": {
        "Authorization": "Bearer ${env:NUDGEHOST_API_KEY}"
      }
    }
  }
}
```

**Codex CLI.** Add this table to ~/.codex/config.toml. It needs no feature flag. Tested 13 October 2026.

```toml
[mcp_servers.nudgehost]
url = "https://www.nudgehost.com/api/mcp"
bearer_token_env_var = "NUDGEHOST_API_KEY"
```

The Codex CLI test covered connecting and listing links.

## Cursor plugin

This repository is also a Cursor plugin. Its mcp.json points Cursor at the server above, and .cursor-plugin/plugin.json declares one required setting, NUDGEHOST_API_KEY, for an API key from the API keys panel of your NudgeHost dashboard. The plugin holds no key itself.

## Example

[Publishing a ZIP as a site from Cursor](examples/publish-a-zip-with-cursor.md) walks through the three steps that turn a folder into a live site.

## Rate limits

Each key gets a fixed number of requests per 60 second window, set by the account's plan.

- Free: 20 requests per 60 seconds
- Pro: 60 requests per 60 seconds
- Studio: 150 requests per 60 seconds

The MCP tools and the REST API share one budget per key. Upload size and the number of live links also depend on the plan, and the [pricing page](https://www.nudgehost.com/pricing) has the current figures.

## Licence

The text and examples in this repository are under the [MIT licence](LICENSE). The NudgeHost service has its own [terms](https://www.nudgehost.com/terms).
