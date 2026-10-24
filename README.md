<p align="center"><img src="assets/logo.png" width="96" alt="OpenBait"></p>

# OpenBait Lookalike Domains MCP

A hosted, read-only [MCP](https://modelcontextprotocol.io) server that finds lookalike domains of a brand's website: typos, swapped letters, look-alike characters, added words and other TLDs. For each candidate it checks whether the name resolves in DNS and what its front page shows.

- **Endpoint:** `https://openbait.com/api/mcp`
- **Transport:** Streamable HTTP
- **Auth:** none, no sign-up, no API key
- **Official MCP Registry:** [`com.openbait/lookalike-domains`](https://registry.modelcontextprotocol.io/v0/servers?search=com.openbait)
- **Web version:** [openbait.com/tools/typosquat-check](https://openbait.com/tools/typosquat-check)

The server is run by [OpenBait](https://openbait.com). This repository holds the documentation and the registry entry; there is nothing to install.

## Connect

### Claude Code

```bash
claude mcp add --transport http openbait-lookalike-domains https://openbait.com/api/mcp
```

### Claude (claude.ai / Claude Desktop)

Settings → Connectors → Add custom connector, then paste `https://openbait.com/api/mcp`.

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "openbait-lookalike-domains": {
      "url": "https://openbait.com/api/mcp"
    }
  }
}
```

### VS Code

`.vscode/mcp.json`:

```json
{
  "servers": {
    "openbait-lookalike-domains": {
      "type": "http",
      "url": "https://openbait.com/api/mcp"
    }
  }
}
```

### Cline, Windsurf and other clients

Add a remote (Streamable HTTP) server with the URL `https://openbait.com/api/mcp`. No headers or environment variables are needed.

## Tool

### `check_lookalike_domains`

| Argument | Type | Required | Description |
|---|---|---|---|
| `domain` | string | yes | The brand's website, e.g. `example.com` or `https://www.example.com` |

Returns the registrable domain that was checked, a summary, the lookalikes and a link to the same result on openbait.com. Example (trimmed to three lookalikes):

```json
{
  "domain": "example.com",
  "summary": { "totalCandidates": 121, "reviewNow": 10, "activeDns": 10, "watchlist": 0 },
  "lookalikes": [
    { "domain": "examplehub.shop", "severity": "high", "score": 100, "mutationType": "keyword", "activeDns": true, "site": "no_site" },
    { "domain": "examplestore.shop", "severity": "high", "score": 100, "mutationType": "keyword", "activeDns": true, "site": "no_site" },
    { "domain": "getexample.shop", "severity": "high", "score": 100, "mutationType": "keyword", "activeDns": true, "site": "no_site" }
  ],
  "resultUrl": "https://openbait.com/tools/typosquat-check?domain=example.com"
}
```

| Field | Values |
|---|---|
| `severity` | `high` or `medium` |
| `score` | 0–100, higher is more likely to be mistaken for the brand |
| `mutationType` | `homoglyph`, `keyword`, `omission`, `transposition`, `tld_swap` |
| `activeDns` | the name resolves in DNS |
| `site` | for names that resolve: `brand_page` (the page uses the brand's name), `redirects_to_you` (ends on the brand's own domain), `live_other` (another live page), `no_site` |

A name that resolves is not proof of a fake site; treat the result as a list to look at.

## Example prompts

- "Check lookalike domains for example.com."
- "Which lookalikes of our domain are live and show our brand name?"

## Limits

- Read-only: the tool only looks up names and front pages. It never sends anything on your behalf.
- Page titles are not returned, so text written by whoever registered a lookalike never reaches your model.
- Results are cached for a few hours. Requests are rate-limited per IP.
