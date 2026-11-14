# 06 · Evals

An eval is a repeatable test of how your LLM system behaves. Without evals, every prompt tweak or model upgrade is a guess.

## What to test

Agents fail in the middle, not just at the end, so test at three levels:

| Level | Question | Example check |
|---|---|---|
| **Final output** | Is the answer right and well-formed? | Matches the expected value; valid JSON; cites a source. |
| **Trajectory** | Did it take a sensible path? | Called `get_open_incidents` before `create_incident`; never called a write tool in read-only mode. |
| **Cost and latency** | Is it affordable and fast enough? | Under 8 steps, under 30 seconds, under a token budget. |

Scoring only the final text misses orchestration regressions: a new prompt that still gets the right answer but takes 3× the steps, or skips a safety check.

## Kinds of graders

- **Code checks:** exact match, regex, schema validation, "was tool X called". Cheap and reliable. Use these wherever possible.
- **LLM-as-judge:** a model scores against a rubric. Good for tone, completeness and relevance. Calibrate it against human labels first, and keep the rubric specific.
- **Human review:** the ground truth, used to build and spot-check the other two.

## Building the dataset

- Start with **real inputs** from logs: 30–100 cases beat 1,000 synthetic ones.
- Include the **hard and ugly cases**: empty input, mixed languages, missing IDs, hostile text.
- Every production bug becomes a new test case.
- Keep a held-out set you don't tune against.

## When to run them

On every change to the prompt, tools, model or retrieval, and on a schedule against production samples to catch drift. Treat a drop in the score like a failing unit test: it blocks the release.

## Sources

- SitePoint, [The definitive guide to agentic design patterns in 2026](https://www.sitepoint.com/the-definitive-guide-to-agentic-design-patterns-in-2026/): evals on every prompt or tool change.
- Mastra, [AI agent architecture](https://mastra.ai/articles/ai-agent-architecture): continuous evaluation and regression testing.
- Zheng et al., [Judging LLM-as-a-Judge](https://arxiv.org/abs/2306.05685): strengths and known biases of model graders.

## Try it

Take your last 20 real requests, write the expected outcome for each, and turn them into a script that runs in CI. That one file is worth more than any eval framework.
