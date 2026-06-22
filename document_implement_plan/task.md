# Enterprise RAG System — Task Tracker

## Task 0: Project Setup & Infrastructure
- [x] `pyproject.toml` — uv project config + dependencies
- [x] `docker-compose.yml` — Qdrant + Neo4j
- [x] `.env.example` — template env vars
- [x] `.gitignore` — update for project
- [x] `src/config.py` — Pydantic Settings
- [x] `src/models.py` — Shared data models
- [x] `scripts/seed_data.py` — Copy sample data → data/
- [x] Verify: `uv sync` + `docker compose up -d`

## Task 1: Advanced Indexing Pipeline (20pts)
- [x] `src/indexing/document_loader.py`
- [x] `src/indexing/semantic_chunker.py`
- [x] `src/indexing/vector_store.py`
- [x] `scripts/index_documents.py`
- [x] Verify: index 20 docs, manual check payload OK ✓

## Task 2: Hybrid Search Implementation (20pts)
- [x] `src/retrieval/bm25_retriever.py`
- [x] `src/retrieval/hybrid_search.py`
- [x] `src/retrieval/query_router.py`
- [x] `scripts/test_hybrid_search.py` — verify script
- [x] Verify: all 4 tests PASS (keyword/semantic/hybrid/comparison) [OK]

## Task 3: Query Transformation Layer (15pts)
- [x] `src/transformation/hyde.py`
- [x] `src/transformation/query_decomposition.py`
- [x] `src/transformation/transformation_router.py`
- [x] `scripts/test_query_transformation.py` -- verify script
- [x] Verify: run `test_query_transformation.py` (needs OpenAI key)

## Task 4: Post-Retrieval Processing (15pts)
- [x] `src/post_retrieval/cross_encoder_reranker.py`
- [x] `src/post_retrieval/mmr.py`
- [x] `src/post_retrieval/post_retrieval_pipeline.py`
- [x] `scripts/test_post_retrieval.py` -- verify script
- [x] Verify: run `test_post_retrieval.py` (no API key needed)

## Task 5: GraphRAG Integration (20pts)
- [x] `src/graph/entity_models.py`
- [x] `src/graph/entity_extractor.py`
- [x] `src/graph/knowledge_graph.py`
- [x] `src/graph/graph_retriever.py`
- [x] `scripts/build_graph.py` -- populate Neo4j
- [x] `scripts/test_graph.py` -- verify script
- [x] Verify: run build_graph.py then test_graph.py (needs OpenAI key + Neo4j)


## Task 6: Integration & Orchestration (10pts)
- [x] `src/orchestrator/pipeline.py`
- [x] `src/orchestrator/evaluator.py`
- [x] `main.py` -- CLI
- [x] `app.py` -- FastAPI
- [x] `frontend/index.html` + `style.css` + `app.js`
- [x] `submission_answers.md`
- [x] `evaluation_report.md`
- [x] Verify: run FastAPI + open browser, test CLI
