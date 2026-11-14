# 01 · Workflows vs. agents

"Agent" gets used for anything with an LLM in it. A more useful split:

- **Workflow:** your code decides the sequence of steps. The LLM fills in steps (classify this, summarise that), but the path is fixed in advance.
- **Agent:** the LLM decides the next step at run time, usually by choosing which tool to call, and loops until it decides it is done.

Both are "agentic systems". The difference is who owns the control flow.

## The common workflow shapes

| Pattern | How it works | Good for |
|---|---|---|
| **Prompt chaining** | Step N's output is step N+1's input, with code checks between steps. | Tasks that split cleanly: draft → check → translate. |
| **Routing** | A first call classifies the input and sends it to a specialised prompt or model. | Mixed traffic: billing vs. technical vs. complaint tickets. |
| **Parallelisation** | Run independent sub-tasks at once (sectioning), or the same task several times and vote. | Speed, or more confidence on high-stakes checks. |
| **Orchestrator–workers** | A lead call decides the sub-tasks at run time and hands them to workers, then merges results. | Tasks where you can't list the sub-tasks up front. |
| **Evaluator–optimiser** | One call produces, another critiques, and the loop repeats until the critique passes. | Writing or code where "good" is checkable. |

## When to reach for a real agent

Use an open-ended agent loop only when **the number and order of steps can't be predicted**: debugging, research, operating a system whose state you only learn by poking it.

Everything else is cheaper, faster and easier to test as a workflow. A single well-built LLM call with retrieval and a few examples is often enough.

## The minimal agent loop

```python
messages = [user_task]
for step in range(MAX_STEPS):              # hard cap: never loop forever
    reply = llm(messages, tools=TOOLS)
    if reply.stop_reason != "tool_use":
        return reply.text                  # the model says it's done
    for call in reply.tool_calls:
        result = run_tool(call)            # validated, permission-checked
        messages.append(tool_result(call.id, result))
raise StepLimitExceeded()
```

Three details matter more than the framework you pick: the step cap, how tool errors are reported back, and what ends up in `messages` (chapter 04).

## Example from my work

Turning monitoring alarms into ServiceNow incidents is a **workflow**: group by account, create or update a ticket, resolve on recovery. Every path is known, so it is plain code plus an LLM only for the free-text summary. An assistant that *explains why* a SIM was capped needs to look things up in an order nobody can script, so that part is an **agent**, with read-only tools.

## Sources

- Anthropic, [Building effective agents](https://www.anthropic.com/research/building-effective-agents): the workflow/agent split and the five patterns above.
- Simon Willison, [notes on "Building effective agents"](https://simonwillison.net/2024/Dec/20/building-effective-agents).
- Spring AI, [Effective agents reference](https://docs.spring.io/spring-ai/reference/api/effective-agents.html): the same patterns implemented in Java.

## Try it

Take one process you own and draw it as a workflow. Mark the single step where you genuinely can't predict what happens next. That step is the only candidate for an agent.
