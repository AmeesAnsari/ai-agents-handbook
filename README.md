# AI Agents Handbook

My working notes on designing, building and running LLM agents in production. Each chapter has a short explanation, the trade-offs I care about, and links to the primary sources.

These notes come from building AI and automation services (speech scoring, incident automation, IoT pipelines) and from reading the sources listed in each chapter. Last reviewed **November 2026**.

## Chapters

| # | Topic | One-line takeaway |
|---|---|---|
| 01 | [Workflows vs. agents](chapters/01-workflows-vs-agents.md) | Start with a fixed workflow; give the model control only where the path can't be known in advance. |
| 02 | [Tool use](chapters/02-tool-use.md) | Tools are the agent's API surface: narrow scopes, strict schemas, readable errors. |
| 03 | [Model Context Protocol (MCP)](chapters/03-mcp.md) | A standard way to plug tools and data into any agent. The 2026-07-28 spec made it stateless. |
| 04 | [Context and memory](chapters/04-context-and-memory.md) | The context window is a budget. Spend it on what the next step needs. |
| 05 | [Retrieval (RAG)](chapters/05-retrieval.md) | Retrieval quality caps answer quality. Measure it on its own, before the LLM. |
| 06 | [Evals](chapters/06-evals.md) | Test the trajectory, not just the final answer, and run evals on every prompt or tool change. |
| 07 | [Guardrails and security](chapters/07-guardrails-and-security.md) | Enforce limits in the runtime, not in the prompt. Assume tool output is hostile. |
| 08 | [Multi-agent systems](chapters/08-multi-agent.md) | Split into several agents only when one agent's context or tool list stops working. |
| 09 | [Production checklist](chapters/09-production-checklist.md) | What I check before an agent touches real systems. |

## How to use these notes

- Read 01, 02 and 07 first: they cover most real-world failures.
- Each chapter ends with **Sources** (primary material first) and **Try it** (a small exercise).
- The retrieval chapter pairs with [rag-playground](https://github.com/AmeesAnsari/rag-playground), which uses this handbook as its test corpus.

## License

Text is [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Code snippets are MIT.
