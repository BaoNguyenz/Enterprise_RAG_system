# Enterprise RAG System for TechDocs Inc.

<p align="left">
  <a href="#-evaluation-metrics--performance">
    <img src="https://img.shields.io/badge/Context_Relevance-0.4130-green" alt="Context Relevance">
  </a>
  <a href="#-evaluation-metrics--performance">
    <img src="https://img.shields.io/badge/Faithfulness-83.3%25-brightgreen" alt="Answer Faithfulness">
  </a>
  <a href="#-evaluation-metrics--performance">
    <img src="https://img.shields.io/badge/Avg_Latency-4.91s-orange" alt="Average Latency">
  </a>
</p>

<p align="left">
  <a href="#-technology-stack">
    <img src="https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white" alt="Python">
  </a>
  <a href="#-technology-stack">
    <img src="https://img.shields.io/badge/FastAPI-0.100%2B-009688?logo=fastapi&logoColor=white" alt="FastAPI">
  </a>
  <a href="#-technology-stack">
    <img src="https://img.shields.io/badge/Qdrant-Vector_DB-red" alt="Qdrant">
  </a>
  <a href="#-technology-stack">
    <img src="https://img.shields.io/badge/Neo4j-Graph_DB-008CC1?logo=neo4j&logoColor=white" alt="Neo4j">
  </a>
  <a href="#-technology-stack">
    <img src="https://img.shields.io/badge/OpenAI-GPT--4o--mini-412991?logo=openai&logoColor=white" alt="OpenAI">
  </a>
</p>

An advanced, production-grade Retrieval-Augmented Generation (RAG) system designed to solve search challenges in large technical repositories. The system features semantic chunking, hybrid search (Vector + BM25 with RRF), query transformation (HyDE & Decomposition), post-retrieval processing (Cross-Encoder Reranker & MMR), and GraphRAG (Neo4j).


---

## 🏗️ Architecture Overview

The system operates on an End-to-End pipeline combining Vector Search, Keyword Search, and Graph Databases:

```mermaid
flowchart TD
    %% Theme Styling
    classDef storage fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px,color:#0d47a1;
    classDef action fill:#e8f5e9,stroke:#43a047,stroke-width:2px,color:#1b5e20;
    classDef routing fill:#fffde7,stroke:#fbc02d,stroke-width:2px,color:#f57f17;
    classDef input fill:#ffe0b2,stroke:#fb8c00,stroke-width:2px,color:#e65100;
    classDef output fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px,color:#4a148c;

    subgraph INGESTION ["OFFLINE DATA INGESTION PIPELINE"]
        Docs[Technical Docs] --> Chunk[Semantic Chunking]
        
        %% Vector & BM25
        Chunk --> Embed[Text Embeddings] --> VecDB[(Qdrant Vector DB)]
        Chunk --> BuildBM25[BM25 Indexing] --> BM25DB[(BM25 Retriever)]
        
        %% GraphRAG
        Chunk --> GraphExtract[LLM Entity-Relation Extract] --> GraphDB[(Neo4j Graph DB)]
    end

    subgraph RETRIEVAL ["ONLINE RETRIEVAL PIPELINE (Real-Time)"]
        Q[User Query] --> Router{Query Router}
        
        %% Vector / Hybrid
        Router -- "Hybrid Search" --> QTrans[Query Transformation]
        QTrans --> HyDE[HyDE - Hypothetical Document]
        QTrans --> Decompose[Decomposer - Subqueries]
        
        HyDE & Decompose --> SearchEngine(Hybrid Search Engine)
        SearchEngine --> |Vector Search| VecDB
        SearchEngine --> |Keyword Search| BM25DB
        
        %% Merging & Reranking
        VecDB & BM25DB --> RRF[RRF Fusion]
        RRF --> |Top 50 Chunks| Rerank[Cross-Encoder Reranker]
        Rerank --> |Top 20 Chunks| MMR[MMR Diversity Filter]
        MMR --> |Top 10 Chunks| ContextA[Context A: Detailed Text]

        %% GraphRAG
        Router -- "GraphRAG" --> GraphSearch[GraphRAG Search]
        GraphSearch --> |Entity Linking| GraphDB
        GraphDB --> ContextB[Context B: Graph Subgraph]
    end

    subgraph GENERATION ["GENERATION PIPELINE"]
        ContextA & ContextB --> Assemble[Context & Prompt Assembly]
        Assemble --> LLM[GPT-4o Generator]
        LLM --> Ans([Answer Output])
    end

    class VecDB,BM25DB,GraphDB storage;
    class Chunk,Embed,BuildBM25,GraphExtract,RRF,Rerank,MMR,GraphSearch,Assemble action;
    class Router,QTrans,HyDE,Decompose routing;
    class Q input;
    class LLM,Ans output;
```

---

## 🛠️ Technology Stack

| Layer | Technologies / Frameworks | Purpose |
|---|---|---|
| **Programming Language** | Python 3.10+, JavaScript (Vanilla) | Backend logic, pipeline, and interactive web frontend. |
| **Package Manager** | `uv` (by Astral) | Fast dependency management and virtual environments. |
| **Vector Database** | Qdrant | Dense vector retrieval using HNSW indexing. |
| **Graph Database** | Neo4j (GraphRAG) | Knowledge Graph representation of documents, entities, and relations. |
| **Keyword Search** | BM25 (Rank-BM25) | Classical lexical search for error codes, API URLs, and product catalogs. |
| **Embeddings & Reranking** | SentenceTransformers (`all-MiniLM-L6-v2`, `cross-encoder/ms-marco-MiniLM-L-6-v2`) | Local embedding generation and Cross-Encoder passage reranking. |
| **Orchestration / LLM** | OpenAI API (`gpt-4o-mini`), LangChain | Intent routing, query decomposition, HyDE, entity extraction, and final generation. |
| **Backend Framework** | FastAPI, Uvicorn | Production-ready asynchronous API server. |
| **Frontend UI** | HTML5, Vanilla CSS (Modern glassmorphic theme), JavaScript | Sleek web chat UI with real-time response streams and source citations. |

---

## 🌟 Key Features

*   **Semantic Chunking:** Cuts documents based on semantic similarity of contiguous sentences instead of static token length, preserving context boundaries.
*   **Hybrid Search & RRF:** Merges dense vector representations (Qdrant) and lexical keyword matching (BM25) using **Reciprocal Rank Fusion (RRF)** to retrieve both semantic meaning and technical terms (e.g., error codes, API methods).
*   **Query Transformation:** 
    *   **HyDE (Hypothetical Document Embeddings):** Generates hypothetical answers to enrich short user queries before searching.
    *   **Decomposition:** Breaks down complex, multi-part questions into simpler sub-queries.
*   **Post-Retrieval Processing:**
    *   **Cross-Encoder Reranker:** Scores the exact query-chunk pairs using a cross-attention transformer, correcting ordering flaws.
    *   **MMR (Maximal Marginal Relevance):** Filters out duplicate information and enforces context diversity.
*   **GraphRAG (Neo4j):** Extracts entities (Policies, Stakeholders, Products, etc.) and relations to answer global, macro-level questions that traditional vector databases struggle with.
*   **FastAPI & Modern Web UI:** Serves a production-ready HTTP API accompanied by a modern, responsive web chat client.

---

## 📁 Project Structure

```text
├── data/                       # Seeded documents (.md files)
├── sample_data/                # Raw sample documents
├── src/                        # Core codebase
│   ├── config.py               # Settings & configuration (Pydantic)
│   ├── indexing/               # loaders, semantic chunker, vector store
│   ├── retrieval/              # BM25, hybrid search
│   ├── query_transformation/   # Router, HyDE, Decomposer
│   ├── post_retrieval/         # Cross-Encoder reranker, MMR
│   ├── graph/                  # Neo4j Graph DB operations, entity extraction
│   └── orchestrator/           # End-to-end pipeline, evaluator
├── scripts/                    # Utilities for seeding, indexing & validation
├── frontend/                   # HTML/CSS/JS Chat Interface
├── app.py                      # FastAPI App definition
├── main.py                     # CLI Interactive Interface
├── docker-compose.yml          # Qdrant & Neo4j Docker services
└── pyproject.toml              # Project dependencies & environment configuration
```

---

## 🛠️ Getting Started

### Prerequisites
*   [Python 3.10+](https://www.python.org/downloads/)
*   [Docker & Docker Compose](https://www.docker.com/products/docker-desktop/)
*   [uv](https://github.com/astral-sh/uv) (Recommended, Python package manager)

### 1. Environment Setup

Copy `.env.example` to `.env` and fill in your API keys and service credentials:
```bash
cp .env.example .env
```
Ensure `OPENAI_API_KEY` is configured correctly if you plan to run GraphRAG or Query Transformation.

### 2. Start Services (Docker)

Spin up Qdrant Vector Database and Neo4j Graph Database:
```bash
docker compose up -d
```
*   **Qdrant Console:** `http://localhost:6333/dashboard`
*   **Neo4j Console:** `http://localhost:7474` (Default username/password: `neo4j` / `password123`)

### 3. Seed & Index Documents

Initialize the database indexes with the following steps:

```bash
# Step A: Copy sample documents to the active data directory
uv run python scripts/seed_data.py

# Step B: Run semantic chunking, embedding, and load into Qdrant
uv run python scripts/index_documents.py

# Step C: Extract entities via LLM and build the Neo4j Graph
uv run python scripts/build_graph.py
```

---

## 🚀 Running the Project

### Interactive CLI

You can chat with the RAG pipeline directly via a terminal console:
```bash
uv run python main.py
```
*Useful commands inside the CLI:*
*   `/help` - View commands
*   `/mode <auto|hybrid|vector|bm25|graph>` - Switch retrieval modes
*   `/eval` - Evaluate the search quality metrics (generates `evaluation_report.md`)
*   `/quit` - Exit

### Backend API Server

Start the FastAPI backend:
```bash
uv run uvicorn app:app --reload
```
API Documentation will be available at: `http://localhost:8000/docs`

### Frontend Web UI

To chat using the Web UI, open the frontend:
*   On Windows, simply double-click or open `frontend/index.html` in your browser.
*   Or serve it locally using any static web server.

---

## 📊 Evaluation Metrics & Performance

The RAG pipeline is evaluated end-to-end using Ragas-like metrics (Context Relevance & Answer Faithfulness) evaluated via LLM-as-a-judge.

### Summary Metrics (Average across 6 benchmark queries)

| Metric | Score / Value | Description |
|---|---|---|
| **Context Relevance** | **0.4130** | Assesses how relevant the retrieved context chunks (after Post-Retrieval MMR and Reranking) are to the user query. |
| **Answer Faithfulness** | **0.8333** | Measures whether all claims in the generated response are strictly grounded in the retrieved context (no hallucinations). |
| **Average Latency** | **4.91s** | Total end-to-end round trip time. |

### Average Latency Breakdown per Stage
*   **Query Classification (Router):** `0.000s` (Deterministic/Fast prompt mapping)
*   **Vector/Keyword Retrieval:** `1.018s` (Qdrant & BM25)
*   **GraphRAG Search (Neo4j):** `1.509s` (Entity extraction & traversal)
*   **Post-Retrieval Processing:** `0.583s` (Cross-Encoder & MMR)
*   **LLM Generation:** `1.803s` (Response generation)

---

## 🧪 Testing & Verification

The project includes test scripts for verifying each task separately:

```bash
# Verify Task 2: Vector + BM25 Hybrid Search (no API key needed)
uv run python scripts/test_hybrid_search.py

# Verify Task 3: Query Routing, HyDE, and Decomposition
uv run python scripts/test_query_transformation.py

# Verify Task 4: Cross-Encoder Reranking and MMR (no API key needed)
uv run python scripts/test_post_retrieval.py

# Verify Task 5: Neo4j Graph Database extraction and GraphRAG queries
uv run python scripts/test_graph.py
```
