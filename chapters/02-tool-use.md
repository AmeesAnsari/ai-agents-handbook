# 02 · Tool use

A tool is a function the model can ask you to run. The model never runs anything itself: it emits a structured request (tool name plus JSON arguments), your code executes it, and you send the result back. Every frontier model now supports this natively, so the real design work is deciding **which** tools to expose and **how narrow** each one is.

## Designing a tool

Treat tool definitions like a public API written for a new colleague.

- **Name and description do the routing.** The model picks tools from their descriptions. Say what it does, when to use it, and when *not* to.
- **Narrow beats generic.** `get_open_incidents(account_id)` is safer and more reliable than `run_sql(query)`.
- **Strict schemas.** Use enums, required fields and formats. Validate every call against the schema before running it; reject, don't guess.
- **Return what the next step needs.** Trim big payloads, keep stable IDs, and prefer short readable text or compact JSON.
- **Errors are output too.** Return a clear message the model can act on ("account_id 'ACME' not found; IDs look like AC-12345") instead of a stack trace.

```json
{
  "name": "get_open_incidents",
  "description": "List open incidents for one customer account. Use before creating a new incident to avoid duplicates. Read-only.",
  "input_schema": {
    "type": "object",
    "properties": {
      "account_id": { "type": "string", "pattern": "^AC-\\d{5}$" },
      "severity": { "type": "string", "enum": ["P1", "P2", "P3", "P4"] }
    },
    "required": ["account_id"]
  }
}
```

## Permissions

Sort tools by blast radius and treat the groups differently:

| Tier | Examples | Policy |
|---|---|---|
| Read | search, get status, list incidents | Run automatically. |
| Reversible write | add a work note, draft an email | Run automatically, log it. |
| Irreversible or external | close ticket, send email, deactivate SIM, pay | Require human approval, or keep out of the agent entirely. |

Least privilege applies to credentials as well: the agent's service account should only be able to do what its tools allow.

## Failure modes I watch for

- **Wrong tool, right intent:** two tools with overlapping descriptions. Merge them or sharpen the "when not to use" text.
- **Hallucinated arguments:** IDs the model invented. Schema patterns plus a "not found" error that teaches the right format fix most of these.
- **Loops:** calling the same failing tool again and again. Cap steps, and cap repeats of identical calls.
- **Oversized results:** one tool response that fills the context window. Paginate and summarise.

## Sources

- Anthropic, [Building effective agents](https://www.anthropic.com/research/building-effective-agents): appendix on prompt-engineering your tools.
- Mastra, [How to build AI agents](https://mastra.ai/articles/how-to-build-ai-agents): practitioner view on scoping and validation.
- OWASP, [Top 10 for LLM applications](https://genai.owasp.org/llm-top-10/): "excessive agency" is the risk this chapter is about.

## Try it

Write the description for one tool, then ask a colleague which situations it should *not* be used in. If they can't answer from the description alone, the model can't either.
