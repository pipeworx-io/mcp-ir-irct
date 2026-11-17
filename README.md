# @pipeworx/ir-irct

The Iranian Registry of Clinical Trials (IRCT), Iran's national clinical trial registry and a WHO primary registry: search and fetch trial records with titles, status, phase, condition, intervention, sponsor organisation, sample size, countries and dates.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2014+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `ir_irct_trial_search(query, page?, limit?)` — free-text search in English or Persian over titles, conditions, interventions and sponsors, or by IRCT number. Each hit gives the IRCT number, the title and the record link; 25 results a page (`page`, `next_page`); `total` is the registry's own count. The list carries no status, so recruitment status, phase and dates come from the record.
- `ir_irct_trial_get(trial_id)` — one record by IRCT number: scientific and public titles, recruitment status, study type and design, phase, condition, intervention, sponsor organisation, contact organisations, target sample size, countries, registration, enrolment-start and completion dates, secondary identifiers (for example an NCT number) and the record link. An unknown number returns `found: false`.

Registrants', investigators' and contact persons' names, e-mail addresses, telephone numbers and street addresses are deliberately not returned; the record link carries what the registry publishes.

## Auth

Keyless.

## Data sources

- Iranian Registry of Clinical Trials (IRCT), Ministry of Health and Medical Education, Iran: read live, one request per search and two per record lookup, nothing stored.

The registry's search does not filter by recruitment status in this pack. English titles and text only; Persian-language registrations are matched by Persian queries but returned as the registry holds them in its English view. A trial can also sit in ClinicalTrials.gov (see `secondary_ids`).

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "ir-irct": {
      "url": "https://gateway.pipeworx.io/ir-irct/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/ir-irct/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 2014+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ir_irct_trial_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"gastric cancer","limit":3}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ir_irct_trial_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-ir-irct` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/ir-irct/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "ir-irct": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-ir-irct"]
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
ask_pipeworx({ question: "your question about Ir Irct data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
