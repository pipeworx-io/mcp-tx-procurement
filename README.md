# mcp-tx-procurement

Texas State Procurement MCP — State cooperative-contract awards & vendor sales (keyless).

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/tx_procurement_awards \
  -H 'Content-Type: application/json' \
  -d '{"vendor":"Dell","agency":"Health and Human Services","limit":20}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/tx_procurement_awards`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

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

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-tx-procurement
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

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
