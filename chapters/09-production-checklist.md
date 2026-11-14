# 09 · Production checklist

What I check before an agent is allowed to touch real systems. Each line links back to the chapter that explains it.

## Design

- [ ] Could this be a fixed workflow instead of an open agent loop? ([01](01-workflows-vs-agents.md))
- [ ] There is a hard step limit, a timeout and a per-run cost budget. ([01](01-workflows-vs-agents.md), [07](07-guardrails-and-security.md))
- [ ] It's a single agent unless a specific reason to split applies. ([08](08-multi-agent.md))

## Tools and permissions

- [ ] Every tool has a clear description, including when *not* to use it. ([02](02-tool-use.md))
- [ ] Every call is validated against its schema before it runs. ([02](02-tool-use.md))
- [ ] Irreversible or external actions need human approval. ([02](02-tool-use.md))
- [ ] The agent's credentials allow only what its tools need. ([07](07-guardrails-and-security.md))
- [ ] MCP servers are from trusted sources, pinned, and separately scoped. ([03](03-mcp.md))

## Context and data

- [ ] Tool results are trimmed and paginated; nothing can flood the context. ([04](04-context-and-memory.md))
- [ ] Retrieval is measured (hit@k, MRR) on real questions. ([05](05-retrieval.md))
- [ ] PII and secrets are redacted before they reach the model or the logs. ([07](07-guardrails-and-security.md))
- [ ] Untrusted text (tickets, emails, web pages) is marked and can't trigger outbound actions on its own. ([07](07-guardrails-and-security.md))

## Quality

- [ ] An eval set built from real inputs runs in CI on every change. ([06](06-evals.md))
- [ ] Evals check the trajectory and the cost, not just the final answer. ([06](06-evals.md))
- [ ] Prompt-injection and misuse cases are in the eval set. ([06](06-evals.md))

## Operations

- [ ] Every tool call is logged with its arguments and a run ID.
- [ ] Dashboards show success rate, steps per run, cost and latency.
- [ ] There is a kill switch that disables the agent without a deploy.
- [ ] Someone owns it: a named team gets the alerts.
- [ ] A rollback plan exists for prompt, model and tool changes.
