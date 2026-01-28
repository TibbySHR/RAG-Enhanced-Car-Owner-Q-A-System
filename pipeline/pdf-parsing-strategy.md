## PDF Parsing Strategy

This project implements multiple PDF parsing approaches to maximize **content integrity** and minimize **context loss** during downstream retrieval. The key design principle is: **treat each visually coherent block (as highlighted in red) as an atomic sample whenever possible**, so the chunk preserves complete semantics (e.g., a heading + its associated paragraph, or a warning box + its bullet points).

---

### 3.1.1 Block-Based PDF Parsing (Layout-Aware Chunking)

**Goal:** Parse the PDF **according to page layout and visual blocks**, so each red boxed region becomes a self-contained chunk.

- We aim to follow the document’s visual structure as closely as possible.
- **Each highlighted block is treated as one training sample / chunk**, which helps preserve:
  - full warning statements,
  - bullet lists and their scope,
  - short paragraphs that would otherwise be fragmented by naive splitting.

**Example (as shown in the figure):**
A single page is segmented into multiple red-highlighted regions (labeled **1–6**), such as:
- **(1)** Main section text block (e.g., OEM accessories / installation guidance)
- **(2)** A warning box (high-salience safety/legal disclaimer)
- **(3–5)** Additional rule blocks / caution notes
- **(6)** A separate section block (e.g., ownership transfer / important notice)

Each numbered red box is treated as an individual chunk to preserve the full context within that block.

---

### 3.1.2 Sliding-Window PDF Parsing (Cross-Page Continuity)

**Problem:** Layout-based blocks can break continuity when content spans pages.

As shown in the figures (**Fig. 1** and **Fig. 2**), the text in highlighted regions can be **continuous across page boundaries**. To address this, we introduce a **sliding-window strategy** over sentence/segment units to preserve cross-page coherence.

**Core idea:**
1. Convert all PDF text into a single logical sequence (string/segments).
2. Split by sentence delimiters (or other stable separators).
3. Apply a **sliding window with overlap** (similar to convolution kernels), controlled by:
   - **window size (kernel size)**
   - **stride (step size)**

This overlap ensures we don’t “cut through” critical instructions or multi-step answers, and reduces the chance that retrieval misses the exact span needed for a question.

**Toy example (from the figure):**
Input segments:
```text
["aa", "bb", "cc", "dd"]
