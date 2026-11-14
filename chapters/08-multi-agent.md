# 08 · Multi-agent systems

A multi-agent system splits work across several LLM agents: a lead that plans and delegates, and workers (sub-agents) that each handle part of the task with their own context and tools.

It is powerful and often overused. More agents means more calls, more cost, more latency and more places to fail.

## When splitting helps

- **One context can't hold it.** The task needs reading far more material than fits, so sub-agents each read a slice and return a short answer.
- **Tool selection is degrading.** One agent with 50 tools picks the wrong one; three agents with ~15 focused tools each do better.
- **Clear domain boundaries.** Billing, network diagnostics and customer messaging have different data, permissions and owners.
- **Real parallelism.** Independent sub-tasks that can run at the same time.

## When it doesn't

If none of the above applies, a single agent with good tools is cheaper and easier to debug. Every hand-off adds model calls, latency and a new place for context to get lost, and practitioner reports consistently find the accuracy gain is often small compared with the extra cost. Measure both before committing to the split (chapter 06).

## Common shapes

| Shape | How it works |
|---|---|
| **Orchestrator–workers** | A lead breaks the task down at run time, delegates, and merges results. |
| **Pipeline** | Fixed hand-offs: researcher → writer → reviewer. Really a workflow (chapter 01). |
| **Specialist router** | A front agent hands the whole conversation to the right specialist. |

## Making it work

- **Write delegation briefs like tickets:** goal, constraints, what to return, and in what format.
- **Return summaries, not transcripts.** The point of a sub-agent is to keep the lead's context small (chapter 04).
- **Give each agent only its own tools and credentials.**
- **Trace everything** with one run ID across all agents, so you can follow a failure through the hand-offs.
- **Cap fan-out**: limit how many sub-agents one lead can spawn.

## Sources

- Anthropic, [Building effective agents](https://www.anthropic.com/research/building-effective-agents): the orchestrator–workers pattern.
- urandom.io, [Agentic AI orchestration patterns that hold up in production](https://urandom.io/blog/2026-02-17-agentic-ai-orchestration-patterns-that-hold-up).
- Mastra, [AI agent architecture](https://mastra.ai/articles/ai-agent-architecture): when to move from one agent to several.

## Try it

Before adding a second agent, write down which of the four "when splitting helps" reasons applies. If you can't name one, improve the single agent's tools instead.
