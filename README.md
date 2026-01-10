# `rag-chunking-Stratrgies`

## Why Chunking Matters in RAG

Chunking is the process of **splitting source documents into retrievable units** before embedding and retrieval.

In a Retrieval-Augmented Generation (RAG) system, **retrieval quality is bounded by chunk quality**.
No retriever or reranker can surface evidence that was never represented as a coherent chunk.

From prior experiments (Weeks 1–4), we observed:

* Relevant evidence is often **present somewhere** in the corpus
* Retrieval frequently succeeds in **surfacing the right region**
* Ranking can improve **which chunk is prioritized**
* Yet answers still fail when **evidence is fragmented across chunks**

This indicates that chunking is not a preprocessing detail — it is a **core modeling decision**.

---

## What Chunking Controls

Chunking determines:

* **What can be retrieved at all**
* **How evidence is localized**
* **Whether a single chunk can fully answer a question**
* **How much irrelevant context is introduced**

Once documents are chunked and embedded, all downstream components
(retrieval, reranking, generation) operate **within those boundaries**.

---

## Common Chunking Strategies (High-Level)

This repository explores chunking strategies as **design choices**, not defaults.

### 1. Fixed-Size Chunking

Documents are split into chunks of a fixed token or character length.

* Simple and fast
* Often splits semantic units mid-thought
* Can fragment answers across multiple chunks

---

### 2. Overlapping Chunking

Chunks overlap by a fixed window to reduce boundary loss.

* Improves recall near chunk boundaries
* Increases redundancy and noise
* Does not solve deeper semantic fragmentation

---

### 3. Structural Chunking

Chunks are aligned to document structure (sections, headers, paragraphs).

* Preserves logical units
* Depends on document formatting quality
* May produce uneven chunk sizes

---

### 4. Semantic Chunking

Chunks are created based on semantic coherence rather than length.

* Better alignment with meaning
* More complex and expensive
* Requires additional heuristics or models

---

## Why This Repository Exists

Earlier repositories showed that:

* Retrieval can surface relevant evidence
* Reranking can prioritize it
* Yet answers still fail when **evidence is split across chunks**

This repository isolates chunking to answer a single question:

> **How does chunking choice constrain or enable retrieval and reranking success?**

The goal is **not** to find a universally “best” chunking strategy, but to understand:

* Which failure modes chunking introduces
* Which downstream problems chunking can and cannot fix
* How chunking interacts with retrieval and reranking

---

## Scope and Constraints

This repository focuses **only** on chunking.

It does **not**:

* Change retrieval algorithms
* Tune rerankers
* Evaluate generation quality
* Introduce agent behavior

Chunking is treated as a **first-class system boundary**, evaluated under a controlled retrieval and ranking setup.

---