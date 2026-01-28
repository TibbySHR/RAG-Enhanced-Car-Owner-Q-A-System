## 3.3 Re-ranking

A **reranker** is a critical component in modern information retrieval pipelines. It evaluates the candidate results returned by the initial retriever(s) and **re-orders** them to improve **relevance**.

In **RAG** systems, reranking is typically applied **after retrieval** to better estimate the semantic match between the user query and each retrieved document chunk. This produces a more accurate top-K context set, which ultimately improves retrieval quality and downstream answer generation.

### Why integrate a reranker in RAG?

Integrating a reranker into a RAG pipeline can significantly improve answer precision because it can select the document chunks that are most semantically aligned with the query—across either **single-path** retrieval or **multi-path** retrieval (e.g., dense + BM25).

A reranker also improves final quality when the pipeline intentionally increases recall (e.g., retrieving a larger candidate pool via multiple retrievers). With more candidates available, the reranker can perform finer-grained filtering to surface the best evidence.

Practically, reranking helps by:
- removing weakly related chunks from the first-stage retrieval results,
- narrowing the context passed to the LLM down to the most relevant subset,
- improving LLM attention over the provided context (less distraction),
- reducing token usage and therefore lowering inference cost.

### Model Architecture Note

Dense retrieval embeddings typically use a **bi-encoder** architecture, while **bge-reranker-large** uses a **cross-encoder** architecture. In general, cross-encoders can outperform bi-encoders for ranking because they jointly encode the *(query, document)* pair and model deeper interaction signals, at the cost of higher compute per candidate.

![RAG](./reranker.png)

### RAG Architecture with a Reranker (Two-Stage Retrieval + Re-ranking)

The figure illustrates a RAG pipeline augmented with a reranker. The retrieval system can be decomposed into **two stages**:

1. **Candidate Retrieval (Top-K)**
   - Retrieve **Top-K** relevant documents from a **vector database** (dense retrieval).
   - Optionally combine with **sparse retrieval / sparse representations** (e.g., TF-IDF-style sparse embeddings) to improve full-text coverage and lexical matching.

2. **Re-ranking (Scoring + Re-ordering)**
   - The reranker computes a relevance score between the **query** and each **retrieved candidate** and then re-orders them.
   - The top-ranked chunks are injected into the prompt as **context** for the LLM, yielding answers with higher relevance and quality.

---

### Trade-offs and Cost Considerations

Compared to a baseline RAG system that only performs vector retrieval, adding a reranker introduces additional overhead and operational challenges. The added cost mainly comes from:

- **Increased latency** (impact on user-facing response time)
- **Increased compute** (higher serving cost due to per-candidate scoring)

Therefore, it’s recommended to evaluate whether reranking is necessary based on business needs and to balance:
- retrieval quality
- end-to-end latency
- serving cost

---

### 3.3.1 Cross-Encoder Reranker

This project uses **bge-reranker-large** as the reranking model.

Among rerankers, some top-performing options (e.g., Cohere rerank models) are commercial/paid. In the open-source ecosystem, **BAAI’s bge-reranker-base** and **bge-reranker-large** are common choices. In many settings, **bge-reranker-large** achieves performance close to (and sometimes better than) proprietary alternatives.

Key notes:
- Re-ranking typically improves metrics like **hit rate / recall@K** and **MRR (Mean Reciprocal Rank)** across many embedding models.
- The best performance depends on the **pairing** between the embedding model and the reranker, so practitioners should empirically tune and validate the optimal combination in real workloads.


