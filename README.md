# `rag-chunking-Stratrgies`

## Why Chunking Matters in RAG

Chunking is the process of **splitting source documents into retrievable units** before embedding and retrieval.

In a Retrieval-Augmented Generation (RAG) system, **retrieval quality is bounded by chunk quality**.
No retriever or reranker can surface evidence that was never represented as a coherent chunk.

From prior experiments, we observed:

* Relevant evidence is often **present somewhere** in the corpus
* Retrieval frequently succeeds in **surfacing the right region**
* Re-Ranking can improve **which chunk is prioritized**
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

## **How Chunking Is Evaluated in This Repository**

This repository **does not evaluate chunking via retrieval metrics alone**.

Instead, chunking is evaluated at the **ingestion / representability layer**.

### Why Retrieval Metrics Are Insufficient

Retrieval metrics (e.g. Recall@K, rank of first relevant chunk) answer the question:

> *“Given this chunking, can a retriever surface a relevant chunk?”*

They do **not** answer the more fundamental question:

> *“Does a chunk exist that can fully answer the question?”*

If no such chunk exists, retrieval quality is irrelevant —
the answer is **unrepresentable**, regardless of retriever or reranker quality.

#### Representability as the Primary Evaluation Criterion

For each question and each chunking strategy, we manually determine:

* Whether **a single chunk exists** that can answer the question
* Whether the answer requires:

  * One coherent unit
  * A section-level explanation
  * Multiple disjoint regions
* Or whether the answer only appears due to accidental overlap

Each *(question × chunking strategy)* pair is labeled as:

| Label             | Meaning                                          |
| ----------------- | ------------------------------------------------ |
| **Atomic**        | Answer exists entirely within one coherent chunk |
| **Structural**    | Answer aligns to a section-level unit            |
| **Compositional** | Answer requires multiple chunks                  |
| **Accidental**    | Previously answerable only due to overlap        |
| **✗**             | No chunk can answer the question                 |

These labels are assigned **independently of retrieval behavior**.

#### Role of Retrieval Metrics (Secondary)

Retrieval metrics are reported **only after representability is established**, and serve to answer a narrower question:

> *“Given that a valid chunk exists, can retrieval surface it?”*

This separation prevents conflating:

* **Chunking failures** with retrieval failures
* **Ingestion design errors** with ranking errors

---

## Scope and Constraints

This repository focuses **only** on chunking.

It does **not**:

* Change retrieval algorithms
* Tune rerankers
* Evaluate generation quality
* Introduce agent behavior

Chunking is treated as a **first-class system boundary**, evaluated under a controlled retrieval and ranking setup.
* This repository intentionally decouples **chunk representability** from **retrieval performance**.
* A chunking strategy may perform poorly under retrieval metrics while still being *correct* at the ingestion layer, if it changes the representational requirements of the task.
---


Perfect. Below are **three drop-in sections** you can paste directly into your README, in this order:

1. **Key Findings**
2. **Results Summary**
3. **How to Use This Repository**

They are written to satisfy your **Claim Integrity Checklist** — no overreach, no implied causality, no retrieval conflation.

---

## 📊 Results Summary

Evaluation is performed at the **(question × chunking strategy)** level using manual representability labels.

### Representability Transitions (Observed)

Across the evaluated question set:

* Several questions remain **Atomic** under all strategies
  → These are genuinely localized facts.

* Multiple questions transition:

  * **Atomic → Structural** under structural chunking
  * **Atomic → Compositional** under semantic chunking

* At least one question becomes **unanswerable (✗)** under semantic chunking
  → No single chunk contains sufficient information.

These transitions occur **without changing**:

* The question set
* The retrieval method
* The ranking method

This isolates chunking as the causal factor.

### Interpretation Boundary

These results demonstrate **what chunking enables or prevents**, not:

* Which strategy is “best”
* Which strategy improves answer quality
* Which strategy should be used in production

Those questions require downstream systems (e.g. agents, planners), explored in later weeks.

---

## 🧪 How to Use This Repository

This repository is designed for **analysis and inspection**, not end-user QA.

### Typical Workflow

1. **Ingest documents** using one of the chunking strategies:

   * Fixed
   * Structural
   * Semantic

2. **Inspect generated chunks**

   * Chunk size distributions
   * Boundary alignment
   * Content coherence

3. **Evaluate representability**

   * For each question, determine whether:

     * A single chunk can answer it
     * Multiple chunks are required
     * The answer is unrepresentable

4. **Optionally run retrieval**

   * Retrieval metrics are secondary
   * Used only after representability is confirmed

### What This Repo Is Best For

* Understanding **why retrieval fails even when evidence exists**
* Designing chunking strategies intentionally
* Stress-testing assumptions about “atomic” QA
* Preparing for multi-step or agentic retrieval systems
