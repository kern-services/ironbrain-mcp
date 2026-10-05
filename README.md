# Ironbrain MCP (Cursor plugin)

> **Store mirror note:** Project store cannot hold a `.cursor-plugin/` directory name. Before pushing to GitHub, either extract [`artifacts/ironbrain-mcp-listing.tar.gz`](../../artifacts/ironbrain-mcp-listing.tar.gz) (complete tree) or rename `cursor-plugin/` → `.cursor-plugin/`.

**Platform MCP for Ironbrain** — bots authenticate with an Ironbrain account (OAuth) and use the Outbox / HITL catalog. Adapter secrets (IMAP/SMTP/CalDAV) stay on the Ironbrain API, not on the bot host.

Product: [ironbrain.de](https://www.ironbrain.de/) · Overview: [ironbrain.de/mcp](https://www.ironbrain.de/mcp/) _(marketing page; path may land with the ironbrain.de PR)_ · API MCP: `https://app.ironbrain.de/api/mcp`

This public repo is the **Cursor community plugin** scaffold for [cursor.directory](https://cursor.directory) (`plugin.json` + `mcp.json` only). The private product source lives in [kern-services/ironbrain](https://github.com/kern-services/ironbrain) (not required to use the plugin).

Distinct from [ironbrain-mailcal](https://github.com/kern-services/ironbrain-mailcal) (self-hosted IMAP/SMTP/CalDAV with credentials on the bot host).

## Prefer HTTP / URL MCP

Root [`mcp.json`](mcp.json) wires the remote Streamable HTTP server with the public OAuth client (PKCE, empty client secret):

```json
{
  "mcpServers": {
    "ironbrain": {
      "url": "https://app.ironbrain.de/api/mcp",
      "auth": {
        "CLIENT_ID": "c086ddde-1812-41e1-8781-656e0f72ea0e"
      }
    }
  }
}
```

| Field | Value |
|-------|--------|
| Type | **URL** (not Command) |
| Server URL | `https://app.ironbrain.de/api/mcp` |
| Client ID | `c086ddde-1812-41e1-8781-656e0f72ea0e` |
| Client Secret | leave empty |

Login opens `https://app.ironbrain.de/login` → consent at `/oauth/consent`. After Connect succeeds, start a **new** Cursor Cloud Agent / thread so discovery refreshes.

### Cloud Agents UI path

1. Open [cursor.com/agents](https://cursor.com/agents)
2. In the composer, click **+** → **MCP Servers**
3. **Add MCP** / Edit → Type **URL**, paste Server URL + Client ID above

Not under dashboard → Plugins & MCPs.

## Optional: stdio .NET tool

For local desktop stdio (when you prefer a process on PATH instead of URL MCP), install the NuGet global tool **after** it is published via [Trusted Publishing (OIDC)](https://learn.microsoft.com/nuget/nuget-org/trusted-publishing) — **do not** use classic long-lived NuGet API keys.

| nuget.org version | Status |
|-------------------|--------|
| **0.2.1** | Published — use until 0.2.3 ships |
| **0.2.3** | Pending Trusted Publishing / publish workflow (see ironbrain PR [#54](https://github.com/kern-services/ironbrain/pull/54)) |

```bash
# Prefer latest published:
dotnet tool install -g Ironbrain.Mcp
# or pin while 0.2.3 is not on nuget.org yet:
dotnet tool install -g Ironbrain.Mcp --version 0.2.1

# Ensure ~/.dotnet/tools is on PATH, then:
# merge mcp.stdio.example.json into your Cursor MCP settings
```

See [`mcp.stdio.example.json`](mcp.stdio.example.json). HTTP MCP already exposes the full catalog (`list_available_actions`, `whoami`, connectors, enqueue tools) on production; prefer URL listing for Cloud Agents.

## Cursor plugin files

| Path | Role |
|------|------|
| [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json) | Cursor plugin manifest |
| [`plugin.json`](plugin.json) | Agent Plugins (open standard) manifest |
| [`mcp.json`](mcp.json) | Default MCP config — **HTTP URL + OAuth client id** |
| [`mcp.stdio.example.json`](mcp.stdio.example.json) | Optional stdio fallback (not auto-discovered) |
| [`docs/cursor-directory.md`](docs/cursor-directory.md) | Submission checklist for cursor.directory |

## What you get

Same tool surface as the hosted API MCP, including:

- `list_available_actions` / `whoami` (capabilities catalog)
- Mail/calendar reads + Outbox enqueue (`enqueue_*`) with HITL Freigabe
- Connector admin tools and external `enqueue_mcp_tool`
- `get_action` / `list_pending_actions` / `reject_action`

## License

MIT — see [LICENSE](LICENSE).
