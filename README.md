# lastscanned plugin

Track parcels and ocean containers across 549 carriers, and compare USPS, UPS, and FedEx rate quotes, from any agent that supports Agent Plugins v1. The plugin connects to the lastscanned MCP server at `https://mcp.lastscanned.com/mcp` and adds a skill that teaches the agent how to use it.

The package has no local program, no dependencies, and no credentials.

## What's included

| Path | Role |
| --- | --- |
| `plugin.json` | Portable manifest |
| `mcp.json` | One Streamable HTTP server, with no headers |
| `skills/lastscanned/SKILL.md` | Agent skill. Instructions only |
| `documentation/NATIVE-SETUP.md` | Optional sign-in for Hermes |
| `LICENSE` | MIT |

## Tools

| Tool | What it does |
| --- | --- |
| `track_package` | Status, estimated delivery, and scan history for one number |
| `track_packages` | Up to 100 numbers at once, at most 35 per carrier |
| `get_rates` | USPS, UPS, and FedEx quotes between US ZIP codes. Quotes only, with no label purchase |
| `list_carriers` | Carrier ids. Free, with no sign-in needed |

Container numbers work in the tracking tools for Maersk (including Hamburg Süd), MSC, COSCO, Evergreen, ONE, and Yang Ming. Leased containers, such as `TGHU` and `TCNU` prefixes, need an explicit `carrier`.

Watches and webhooks are part of the [REST API](https://docs.lastscanned.com/webhooks), not this plugin.

## Install on Hermes

```text
hermes plugins install dragonhuntr/lastscanned-plugin --no-enable
hermes plugins enable lastscanned
```

The skill is namespaced as `lastscanned:lastscanned`, and the tools appear as `mcp__lastscanned__track_package` and so on.

## Sign-in and cost

The server connects without sign-in. Without it, each client gets one free tracking lookup and five free quotes a day. Requests from cloud and hosting networks do not get the free tracking lookup.

For more, sign in with OAuth or use an API key from [app.lastscanned.com/keys](https://app.lastscanned.com/keys). Each tracked number and each quote counts toward your plan or credits. See [authentication and pricing](https://docs.lastscanned.com/authentication).

Agent Plugins v1 sends `mcp.json` headers literally, so keys do not go in that file. Hermes users can follow [`documentation/NATIVE-SETUP.md`](documentation/NATIVE-SETUP.md).

All four tools set `readOnlyHint: true`. In Hermes, that skips the approval prompt even for an `untrusted` server, and the plugin sets no spend limit. If you need a cap, rely on your lastscanned plan.

## Privacy

A tool call sends its tracking number, container number, carrier id, or quote fields to `mcp.lastscanned.com`. Don't commit API keys or customer tracking numbers to this repository.

## License and contact

MIT. Maintained by dragonhuntr. Contact: wai@lastscanned.com

## Links

- [Website](https://lastscanned.com)
- [MCP guide](https://docs.lastscanned.com/mcp)
- [Container tracking](https://docs.lastscanned.com/container-tracking-api)
