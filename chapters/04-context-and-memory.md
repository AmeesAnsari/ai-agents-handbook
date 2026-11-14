# 04 · Context and memory

Everything a model knows about the current task is in its context window: the system prompt, tool definitions, conversation history, tool results and retrieved documents. The window is large but not free. Long contexts cost more, run slower, and models pay less attention to material buried in the middle.

Treat context as a **budget**, and spend it on what the next step needs.

## Where the budget goes

| Item | Typical problem | Fix |
|---|---|---|
| Tool definitions | 40 tools loaded "just in case". | Load only the tools for this task; route first (chapter 01). |
| Tool results | A 2 MB JSON dump from one call. | Trim, paginate, summarise before returning. |
| History | Every step of a 60-step run kept verbatim. | Summarise older turns; keep decisions and open questions. |
| Retrieved docs | Top 20 chunks when 3 would do. | Better retrieval, fewer chunks (chapter 05). |

## Kinds of memory

- **Working memory:** the current context. Gone when the task ends.
- **Session memory:** a summary carried between turns of one conversation.
- **Long-term memory:** facts saved to a store (files, a database, a vector index) and loaded later. Examples: user preferences, past decisions, what worked last time.

Long-term memory needs the same care as any database: what gets written, who can read it, and how wrong entries get corrected or deleted. A model that saves a mistaken "fact" will keep acting on it.

## Patterns that work

- **Compaction:** when history gets long, replace old turns with a structured summary (goal, decisions so far, open issues, key IDs).
- **Scratchpad files:** let the agent write notes to a file and re-read them, instead of keeping everything in the conversation.
- **Sub-agents for exploration:** send a sub-agent to read 50 files and return a 10-line answer, so the main context only gets the answer (chapter 08).
- **Stable IDs over prose:** "incident INC0012345" survives summarisation better than "the ticket from earlier".

## Sources

- Anthropic, [Building effective agents](https://www.anthropic.com/research/building-effective-agents): keep agent designs simple and transparent.
- Practitioner write-up, [Building AI agents that actually work in production](https://tianpan.co/blog/2026-02-24-building-ai-agents-that-actually-work-in-production): context overflow as a top failure mode.
- Liu et al., [Lost in the Middle](https://arxiv.org/abs/2307.03172): models use information at the start and end of long contexts better than the middle.

## Try it

Log the token count of each part of one real agent run (system prompt, tools, history, tool results). Most teams find one category is more than half the total, and it's rarely the one they expected.
