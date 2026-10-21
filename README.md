# Public Feature Board and Roadmap Monitor MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-public-feature-board-roadmap-monitor)](https://smithery.ai/servers/mambabuilt/mcp-public-feature-board-roadmap-monitor) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-public-feature-board-roadmap-monitor/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-public-feature-board-roadmap-monitor) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-public-feature-board-roadmap-monitor%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-public-feature-board-roadmap-monitor&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-public-feature-board-roadmap-monitor)](https://www.npmjs.com/package/@mambalabsdev/mcp-public-feature-board-roadmap-monitor) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-public-feature-board-roadmap-monitor)](https://www.npmjs.com/package/@mambalabsdev/mcp-public-feature-board-roadmap-monitor) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-public-feature-board-roadmap-monitor)](https://github.com/mambalabsdev/mcp-public-feature-board-roadmap-monitor/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-public-feature-board-roadmap-monitor)

An MCP server for the Mamba Labs Public Feature Board and Roadmap Monitor actor on Apify. Give it a company domain. Each run finds the company's public feature board and returns the requests its customers vote on most.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Your keys](#your-keys)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

- **Finds the board**: a link on the homepage, a board on the company's own subdomain, or a Canny, Featurebase, Frill, or UserVoice board named after the company that links back to its domain.
- **One board row per company**: platform, board URL, total posts, top request, status counts, and `integration_targets`, the products customers ask the company to integrate with.
- **One request row per request**: title, votes, status, comments, board, link, and `is_integration_request` with `integration_target`.
- **Monitoring**: with `only_new` and a `state_key`, only new requests, status changes, and vote jumps since the last run.

A company with no public board comes back free with a `row_status` saying so.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-public-feature-board-roadmap-monitor": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-public-feature-board-roadmap-monitor"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `get_feature_board_requests` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "What are ClickUp's most requested features, and which integrations do its customers want?"
- "Find the public roadmap boards for descript.com, instantly.ai, and ahrefs.com and list their planned items."
- "Which new feature requests showed up on clickup.com's board since last week?"

## Tool

`get_feature_board_requests`. One tool. Example arguments:

```json
{
  "company_domain": "clickup.com",
  "max_requests": "20"
}
```

## Inputs

Every buyer facing input of the actor, unchanged. Omitted inputs take the actor's own defaults. Numbers and booleans are sent as strings.

| Input | Type | Description |
|---|---|---|
| `company_domain` | string | The company website domain, for example clickup.com. The actor reads the homepage for a link to a feedback board or public roadmap, then checks the common board addresses |
| `domains` | array | Several company domains, one per line. Each gets its own board row. Up to 200 per run |
| `board_urls` | array | Board addresses you already know, for example https://feedback.canny.io. With one domain and one board URL, the board is read as that company's board. A board URL on its own is read as a company of its own |
| `company_name` | string | With one domain only: the company name as its board shows it. Used to confirm a board found by name |
| `max_requests` | string | Request rows returned per company, most votes first, up to 100. 0 returns only the board row. Default 20 |
| `max_boards` | string | Boards or forums read per company, largest first, up to 5. Canny shows its top 10 and newest 10 requests on each board page. Default 2 |
| `integration_requests_only` | string | true to return only requests that ask for an integration. The board row still counts every request read. Default false |
| `probe_platform_slugs` | string | true (default) checks <name>.canny.io, <name>.featurebase.app, <name>.frill.co, and <name>.uservoice.com when the site links no board. A board found this way is used only when it links back to the company domain |
| `only_new` | string | true to return only requests that are new, changed status, or gained at least Min vote increase votes since the last run with the same state key. The board row is returned on every run. Default false |
| `min_vote_increase` | string | With only new on: the vote gain that makes a request count as changed. Default 5 |
| `state_key` | string | Names the saved request list, so separate schedules compare separately |
| `max_charge_usd` | string | Optional. The run stops before it would charge more than this |
| `proxy_mode` | string | auto (default) sends requests directly and moves a host to the Apify proxy default pool only when that host refuses. A reliability setting: proxy use adds nothing to your price |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `board-found`, one company whose public feature board was found and read, with its top requests | $0.0038 | $0.00361 | $0.00342 | $0.00323 |
| `apify-actor-start`, per run start | $0.0005 | $0.0005 | $0.0005 | $0.0005 |

A company with no public board, a blocked board, a board with no readable request, and a Productboard portal returned as a link are free. Request rows carry no charge of their own.

## Your keys

The actor needs no key. It reads public board pages and holds no key of any kind. Proxy traffic adds nothing to your price: Mamba Labs pays the platform costs of the run. `proxy_mode` is a reliability setting.

## Full actor documentation

[apify.com/mambalabs/public-feature-board-roadmap-monitor](https://apify.com/mambalabs/public-feature-board-roadmap-monitor)

## License

MIT. See [LICENSE](LICENSE).
