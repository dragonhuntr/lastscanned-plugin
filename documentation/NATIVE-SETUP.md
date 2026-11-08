# Sign in on Hermes

The plugin works without sign-in, within the free daily allowance. To use your plan, add one of the entries below to your own Hermes profile. A same-name entry in `mcp_servers` replaces the plugin's server, including its trust and timeout settings.

Agent Plugins v1 sends `mcp.json` header values literally, so don't add an `Authorization` header to the plugin's `mcp.json`. Hermes native config does expand `${VAR}`, so the key can stay in your profile environment.

## API key

Create a key at <https://app.lastscanned.com/keys>. Set `LASTSCANNED_API_KEY` to it in your Hermes profile environment, then add this to the profile's `config.yaml`:

```yaml
mcp_servers:
  lastscanned:
    url: "https://mcp.lastscanned.com/mcp"
    headers:
      Authorization: "Bearer ${LASTSCANNED_API_KEY}"
```

## OAuth

```yaml
mcp_servers:
  lastscanned:
    url: "https://mcp.lastscanned.com/mcp"
    auth: oauth
```

Then run `hermes mcp login lastscanned` and sign in to lastscanned. The grant uses the `track` scope, on your account's plan limits.

OAuth tokens work on the MCP server only. The REST API takes API keys.

## Approvals and spend

All four tools set `readOnlyHint: true`, so Hermes skips its approval prompt for them even with `trust: untrusted`. A `spend_limit` of `0` and a missing limit both mean unlimited, so `0` won't block spending.

Tracking and quotes count toward your lastscanned plan or credits. `list_carriers` is free.
