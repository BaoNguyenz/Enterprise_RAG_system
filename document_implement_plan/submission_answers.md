# Final Exam: Enterprise RAG System - Answers

## 1. Explain the difference between Bi-Encoder and Cross-Encoder and why we use both in Task 4.

**Bi-Encoder:**
- **How it works:** A Bi-Encoder processes the query and the document independently. It embeds both into vectors in a shared embedding space using a dual-encoder architecture (e.g., SentenceTransformers).
- **Pros:** Extremely fast retrieval. Because documents are processed independently, we can pre-compute their embeddings and index them (e.g., using HNSW in Qdrant/ChromaDB), enabling us to search through millions of documents in milliseconds using cosine similarity or dot product.
- **Cons:** Less accurate. Since the query and document never "see" each other during encoding, the model cannot capture deep contextual interactions between the specific words in the query and the specific words in the document.

**Cross-Encoder:**
- **How it works:** A Cross-Encoder processes the query and the document *together* simultaneously. It concatenates them (e.g., `[CLS] query [SEP] document [SEP]`) and passes the combined sequence through the transformer layers.
- **Pros:** Highly accurate. The self-attention mechanism computes attention across both the query and document tokens simultaneously, allowing it to deeply understand the semantic relationship and relevance between the two.
- **Cons:** Extremely slow. We cannot pre-compute embeddings because the output depends on both the query and document pair. We must run the transformer for every single query-document pair at inference time.

**Why we use both in Task 4:**
We use a **two-stage pipeline** to get the best of both worlds (speed and accuracy):
1. **First Stage (Bi-Encoder / Hybrid Search):** We use fast vector search (Bi-Encoder) and BM25 to quickly retrieve a broad set of candidate chunks (e.g., top 50) from the entire corpus.
2. **Second Stage (Cross-Encoder):** We use the Cross-Encoder to re-rank only these top 50 candidates. Since 50 is a small number, the Cross-Encoder can score them quickly while providing highly accurate relevance scores, yielding the best final top 10 results.

---

## 2. Why is HNSW more efficient than flat index search? What are the trade-offs of increasing the `M` parameter?

**Why HNSW is more efficient:**
A flat index (k-NN) performs an exhaustive search, computing the distance between the query vector and *every single vector* in the database. This scales linearly $O(N)$, which becomes unacceptably slow for large datasets.
HNSW (Hierarchical Navigable Small World) is an Approximate Nearest Neighbor (ANN) algorithm. It builds a multi-layered graph structure:
- The top layers contain fewer nodes with long-range links, acting as "expressways" for fast traversal.
- The bottom layers contain all nodes with short-range links for local, fine-grained searches.
During a search, HNSW starts at the top layer, rapidly navigating towards the target region, and progressively descends to lower layers for precise matching. This logarithmic scaling $O(\log N)$ enables sub-millisecond retrieval speeds even with millions of vectors, making it vastly more efficient than exhaustive flat search.

**Trade-offs of increasing the `M` parameter:**
The `M` parameter defines the maximum number of bi-directional links (edges) created for every new element during insertion into the graph.
- **Pros of increasing `M`:** Higher recall and accuracy. More edges mean the graph is more densely connected, reducing the chance of the search getting stuck in a local minimum and improving the likelihood of finding the true nearest neighbors.
- **Cons of increasing `M`:**
  1. **Higher Memory Usage:** Storing more edges for every node significantly increases the RAM footprint of the index.
  2. **Slower Indexing (Insertion):** Building the graph takes longer because the algorithm must evaluate and create more connections for each new vector.
  3. **Slower Search Speed (in some cases):** While navigating the graph, the algorithm has to evaluate more neighbor connections at each node, which can slightly increase query latency.

---

## 3. How does GraphRAG help solve the global query problem (e.g., "What are the main themes across all policy documents?") compared to standard Vector RAG?

**The limitation of Standard Vector RAG:**
Standard Vector RAG is excellent for "needle-in-a-haystack" queries (local queries) where the answer exists in one or a few specific chunks (e.g., "What is the error code for invalid token?"). 
However, it struggles with **global queries** (e.g., "What are the main themes across all policy documents?"). For global queries, Vector RAG simply retrieves the top $k$ chunks that are most semantically similar to the query. This almost never represents the entire dataset—it only retrieves a fragmented, narrow slice of information, completely missing the broader themes, relationships, and structure of the corpus.

**How GraphRAG solves this:**
GraphRAG represents the corpus as a Knowledge Graph containing entities (Nodes) and their relationships (Edges). 
1. **Holistic View of Relationships:** Instead of treating documents as isolated text chunks, GraphRAG explicitly maps how entities connect (e.g., Policy A *COMPLIES_WITH* GDPR, Stakeholder B *OWNS* Policy C). 
2. **Aggregation and Summarization:** For global queries, GraphRAG can traverse the graph or execute aggregate Cypher queries (e.g., `MATCH (p:Policy) RETURN p.themes`). It can retrieve all policy nodes, cluster their themes, or summarize connected communities.
3. **Structured Retrieval:** By translating natural language into graph queries (NL-to-Cypher), the system can deterministically gather data across the *entire* graph structure rather than relying on vector similarity. This allows the LLM to reason over a comprehensive, structured dataset, providing an accurate macro-level answer that standard semantic search cannot achieve.
