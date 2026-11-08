---
name: lastscanned
description: Track parcels and ocean containers across 549 carriers, list carrier ids, and compare USPS, UPS, and FedEx rate quotes between US ZIP codes. Use when the user asks where a shipment is, wants to track a tracking or container number, or wants to price a parcel. Quotes buy nothing.
compatibility: Needs a client that can connect to the remote MCP server at https://mcp.lastscanned.com/mcp. This package ships no local program.
metadata:
  package-version: "0.1.0"
  remote-mcp-server: "https://mcp.lastscanned.com/mcp"
---

# lastscanned

Use the lastscanned MCP tools to answer shipment questions with live carrier data.

## Tools

Clients may prefix these names. Hermes shows them as `mcp__lastscanned__track_package` and so on.

### track_package

Current status, estimated delivery, and scan history for one tracking number or one ocean container number.

- Required: `tracking_number` (string).
- Optional: `carrier` (carrier id). Omit it to detect the carrier from the number. Pass it when detection fails, when the wrong carrier is detected, or for a leased container.

### track_packages

Up to 100 numbers at once, at most 35 per carrier. One bad number does not fail the others. The result is `{ results }`, one entry per number with `result` or `error`.

- Required: `tracking_numbers` (array of strings).
- Optional: `carrier`, as above.

### get_rates

USPS, UPS, and FedEx price quotes for one parcel between two US ZIP codes, cheapest first. Nothing is bought and no label is created. A source that cannot quote is listed in `errors`.

- Required: `from` and `to` (postal codes), `weight` (number).
- Optional: `weight_unit` (`lb`, `oz`, `kg`, `g`; default `lb`), `residential` (boolean), `length`, `width`, `height` (give all three together), `dimension_unit` (`in` or `cm`; default `in`), `carriers` (array of carrier ids), `from_country` and `to_country` (two letters; default `US`).

Other countries are accepted but currently return `no_quotes`, so do not promise a quote outside the United States.

### list_carriers

No arguments. Returns the carrier ids accepted by `carrier`. It is free and needs no sign-in.

## Usage and cost

A user asking to track a number or get a quote is the go-ahead to make that call.

- Each tracked number and each quote counts toward the user's plan or credits. The tools are read-only, which means they change nothing at the carrier. It does not mean they are free.
- Only look up the numbers the user gave you. Ask before running a large batch the user did not explicitly request.
- Do not poll a number in a loop. For ongoing monitoring, lastscanned offers watches in its REST API, which are not an MCP tool. The guide is at https://docs.lastscanned.com/webhooks.

Without sign-in, each client gets one free tracking lookup and five free quotes a day. Requests from cloud and hosting networks do not get the free tracking lookup, so a hosted agent usually needs sign-in. Plans are at https://docs.lastscanned.com/authentication.

## Reading results

Statuses: `label_created`, `in_transit`, `out_for_delivery`, `delivered`, `exception`, `returned`, `unknown`.

A `not_found` error can mean a mistyped number, a carrier that has not scanned it yet, or the wrong carrier. Treat it as "no record yet", not as a lost shipment.

## Ocean containers

Container numbers use the same tracking tools. Supported lines are Maersk including Hamburg Süd (`maersk`), MSC (`msc`), COSCO (`cosco`), Evergreen (`evergreen`), ONE (`one`), and Yang Ming (`yangming`). CMA CGM, Hapag-Lloyd, HMM, and ZIM are not supported yet.

Leased containers, with owner codes such as `TGHU` and `TCNU`, can sail with any line. Pass the shipping line as `carrier`. If the user has not said which line it is, ask rather than guess.

For a container, `delivered` means the empty box was returned, not that the consignee received the cargo.

## When sign-in is needed

If a tool returns `unauthorized`, `free_lookup_used`, `auth_required`, or `rate_limited`, explain that the free allowance is used up or the credentials were rejected, and offer these options:

- Sign in through the MCP client's OAuth flow, if the client supports it.
- Create an API key at https://app.lastscanned.com/keys and add it to the client's MCP settings. Hermes users can follow `documentation/NATIVE-SETUP.md`.

Keys belong in the client's private settings. Do not ask the user to paste a key into the conversation, and do not write one into this package.

## Privacy

Tool arguments are sent to lastscanned at `https://mcp.lastscanned.com/mcp`. Send only the numbers and quote fields needed for the request.
