# 05 · Retrieval (RAG)

Retrieval-augmented generation means finding the relevant documents first, then giving them to the model with the question. It is how an agent answers from *your* runbooks, tickets and policies instead of from training data.

The key point: **the model can't use a document it was never shown.** Retrieval quality puts a ceiling on answer quality, so measure it on its own before blaming the LLM.

## The pipeline

1. **Chunk** documents into passages (by heading or paragraph, not a fixed character count, where you can).
2. **Index** them, for keyword search, vector search, or both.
3. **Retrieve** the top-k passages for the question.
4. **Optionally re-rank** them with a stronger, slower model.
5. **Generate** an answer from the passages, citing which ones it used.

## Keyword vs. embeddings vs. hybrid

| Method | Strong at | Weak at |
|---|---|---|
| **Keyword (BM25)** | Exact terms: error codes, IDs, product names. Cheap, explainable. | Synonyms and paraphrases ("outage" vs. "service down"). |
| **Embeddings** | Meaning and paraphrase. | Rare exact tokens; can return "related" but wrong passages. |
| **Hybrid** | Usually the best of both. Combine the two ranked lists, e.g. with reciprocal rank fusion. | One more moving part. |

For operations data full of IDs and error codes, hybrid is my default.

## Measuring retrieval

Build a small labelled set: 20–50 real questions, each with the passage that should come back. Then track:

- **Hit@k:** did the right passage appear in the top k?
- **MRR (mean reciprocal rank):** how high did it appear, on average?

Compare methods on the same set. My [rag-playground](https://github.com/AmeesAnsari/rag-playground) does exactly this over the chapters of this handbook.

## Common failures

- **Chunks too small** lose context; **too large** dilute the match. Split on structure.
- **Stale index:** documents changed, embeddings didn't. Re-index on change.
- **Retrieving from untrusted sources** brings prompt-injection text into context (chapter 07).
- **No citations:** without them nobody can check the answer.

## Sources

- Robertson & Zaragoza, [The Probabilistic Relevance Framework: BM25 and Beyond](https://www.staff.city.ac.uk/~sbrp622/papers/foundations_bm25_review.pdf).
- Cormack et al., [Reciprocal Rank Fusion](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf): the simple fusion method used for hybrid search.
- Lewis et al., [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401): the original RAG paper.

## Try it

Write 10 questions your team actually asks, note where each answer lives, and check whether your search puts that document in the top 3.
