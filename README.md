# Intelligent In-Cabin Automotive Knowledge Assistant (RAG)

This document describes an **Intelligent In-Cabin Automotive Knowledge Assistant** project. The system is a **Large Language Model (LLM) + Retrieval-Augmented Generation (RAG)** application that builds a **domain knowledge base** from **car owner manuals** to ground LLM responses and improve factuality. It covers the repository structure, project overview, and the end-to-end solution design.

## Key Highlights

1. **Repository / Codebase Structure**
   - Includes standard project artifacts such as `Dockerfile`, `README.md`, etc.
   - Contains directories for datasets, preprocessing outputs, and model assets (e.g., pretrained LLM weights).

2. **Project Overview**
   - An LLM-centric Q&A system focused on automotive topics such as **vehicle operation, maintenance, and troubleshooting**.
   - The primary knowledge source is the **Lynk & Co owner manual** (used as the corpus for retrieval and grounding).

3. **PDF Parsing / Ingestion Pipeline**
   - Uses a hybrid parsing strategy combining:
     - **Chunk-based parsing**
     - **Sliding-window chunking**
     - **Non-sliding (fixed) chunking**
   - Parsed chunks are deduplicated and then fed into the retrieval/indexing modules.

4. **Retrieval Algorithms**
   - **Dense (vector) retrieval** using **FAISS** + **M3E-large** embeddings.
   - **Sparse retrieval** using **BM25** (via **LangChain BM25 Retrievers**).

5. **Re-ranking**
   - Applies **bge-reranker-large** to improve retrieval relevance.
   - Trade-off: higher relevance/precision at the cost of increased compute/inference overhead.

6. **Inference Optimization**
   - Accelerates **Qwen-7B** inference using **vLLM**.
   - Achieves roughly **~2× speedup** compared to baseline inference.

7. **Planned Improvements**
   - Multi-route retrieval (hybrid retrieval strategies and routing policies)
   - Upgrading the LLM backbone
   - Knowledge base cleanup/refinement (better chunking, metadata, deduplication)
   - Prompt engineering / prompt optimization for improved answer quality
  

## Codebase Structure

```text
.
├── Dockerfile                 # Container image definition
├── README.md                  # Project documentation
├── bm25_retriever.py          # BM25-based sparse retriever
├── build.sh                   # Build script (image build / packaging)
├── data/                      # Dataset directory
│   ├── result.json            # Output / submission results
│   ├── test_question.json     # Test set questions
│   └── train_a.pdf            # Training corpus (PDF manual)
├── faiss_retriever.py         # FAISS-based dense (vector) retriever
├── vllm_model.py              # vLLM inference wrapper for LLM acceleration
├── pdf_parse.py               # PDF parser / chunking pipeline
├── pre_train_model/           # Pretrained model assets
│   ├── Qwen-7B-Chat/          # Qwen-7B-Chat backbone
│   │   └── download.py        # Model download helper
│   ├── bge-reranker-large/    # Cross-encoder reranker model
│   └── m3e-large/             # Embedding model for vector search
├── qwen_generation_utils.py   # Utilities for Qwen response generation
├── requirements.txt           # Python third-party dependencies
├── rerank_model.py            # Reranking logic (bge-reranker integration)
├── run.py                     # Main entry point
└── run.sh                     # Main run script

