# mcp-tx-procurement

Texas State Procurement MCP — State cooperative-contract awards & vendor sales (keyless).

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1866+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

| Tool | Description |
|------|-------------|
| `tx_procurement_awards` | Search TEXAS STATE government contract awards & purchases from the official Texas Department of Information Resources (DIR) Cooperative Contract Sales data on data.texas.gov. Each result is a purchase a Texas public entity made against a DIR statewide cooperative contract: the awarding/purchasing customer (agency, university, city, county, or school district), the winning vendor (supplier), the dollar amount, the DIR contract number, the RFO/product description, brand, quantity, and order dates. Filter by vendor, agency/customer, keyword, fiscal year, contract number, and/or a minimum amount; results are ranked by dollar amount (biggest purchases first) by default. Use this for questions like "who won Texas state IT contracts", "biggest vendors to the Texas Health and Human Services Commission", or "Texas state cooperative-contract purchases for Oracle". This is TEXAS STATE data (not federal). |
| `tx_procurement_vendor` | Aggregate a vendor's TEXAS STATE cooperative-contract sales from the DIR Vendor Sales Report data on data.texas.gov: total dollars sold, number of purchase records, and a breakdown by awarding/purchasing agency (customer), by DIR contract, and by fiscal year. Matches the vendor name as a case-insensitive substring, so it also surfaces name variants (e.g. "Dell" matches "Dell Marketing LP"). Use this to size up a single vendor's business with the State of Texas. This is TEXAS STATE data (not federal). |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "tx-procurement": {
      "url": "https://gateway.pipeworx.io/tx-procurement/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/tx-procurement/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1866+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/tx_procurement_awards \
  -H 'Content-Type: application/json' \
  -d '{"vendor":"Dell","agency":"Health and Human Services","limit":20}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/tx_procurement_awards`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-tx-procurement` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/tx-procurement/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "tx-procurement": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-tx-procurement"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Tx Procurement data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
