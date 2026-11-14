# 03 · Model Context Protocol (MCP)

MCP is an open protocol for connecting AI applications to tools and data. Instead of writing a custom integration for every model and every app, you write one **MCP server** (for example, "ServiceNow" or "Grafana") and any MCP-capable client can use it.

It is now stewarded under the Linux Foundation's Agentic AI Foundation, and most major agent clients and IDEs support it.

## The three building blocks

| Server exposes | What it is | Example |
|---|---|---|
| **Tools** | Functions the model can call. | `create_incident`, `query_metrics` |
| **Resources** | Read-only data the client can pull into context. | A runbook, a config file, a dashboard snapshot |
| **Prompts** | Reusable prompt templates the user can pick. | "Summarise last night's alarms" |

The client (Claude Code, an IDE, your own app) discovers what a server offers and decides what to show the model.

## What changed in the 2026-07-28 spec

The current revision, finalised on 28 July 2026, is a big shift:

- **Stateless core.** The `initialize` handshake and the `Mcp-Session-Id` header are gone. Every request carries the protocol version and the client capabilities the server needs. Servers become much easier to scale behind a load balancer.
- **Extensions framework.** Optional features evolve outside the core spec, so the core can stay small and stable.
- **Feature lifecycle and deprecation policy.** Clear timelines for when features are added, deprecated and removed.
- **Auth hardening.** Clients must validate the `iss` (issuer) parameter in OAuth authorisation responses, which closes off "mix-up" attacks between authorisation servers.

If you run an older server that depends on sessions, plan a migration: anything stored per session needs to move to explicit state (a token, an ID in the request, or your own store).

## When to build an MCP server vs. a plain tool

- **Plain tool** in your agent code: one app, one model, you own both sides.
- **MCP server:** the same capability should work in several clients (your IDE, a chat app, an internal agent), or another team will maintain it.

## Security notes

An MCP server is code with access to your systems, and the text it returns lands in the model's context.

- Install servers from sources you trust, and pin versions.
- Give each server its own narrowly scoped credentials.
- Treat tool and resource output as untrusted input: it can contain prompt-injection text (chapter 07).

## Sources

- [MCP specification, 2026-07-28](https://modelcontextprotocol.io/specification/2026-07-28) and the [latest-spec index](https://modelcontextprotocol.io/specification/latest.md).
- [MCP blog: the 2026-07-28 release](https://blog.modelcontextprotocol.io/posts/2026-07-28/).
- Agentic AI Foundation, [MCP 2026 changes the rules](https://aaif.io/blog/mcp-2026-changes-the-rules).
- Composio, [The MCP 2026-07-28 update](https://composio.dev/blog/mcp-2026-07-28-update-statelessness-apps-auth): a practitioner summary. Check details against the official changelog.

## Try it

List three internal systems your team queries by hand every week. For each, write the one read-only tool an MCP server for it would need first.
