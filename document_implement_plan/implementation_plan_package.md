# Dockerization Implementation Plan

This plan outlines the steps to package the entire **Enterprise RAG System** (FastAPI backend, static HTML/CSS/JS frontend, Qdrant Vector Database, and Neo4j Graph Database) into a single, unified Docker-managed environment.

---

## User Review Required

> [!IMPORTANT]
> **Environment Variables Configuration:**
> When running inside Docker Compose, services communicate using their **container names** as hostnames instead of `localhost`.
> - Qdrant will be accessible at `http://qdrant:6333` (instead of `http://localhost:6333`).
> - Neo4j will be accessible at `bolt://neo4j:7687` (instead of `bolt://localhost:7687`).
> We will inject these overrides automatically via `docker-compose.yml` environment variables, keeping your local `.env` intact for local development.

> [!WARNING]
> **API Keys and Secrets:**
> Your `OPENAI_API_KEY` is required for LLM operations. We will map this from your local `.env` or host system environment directly into the web container.

---

## Proposed Changes

### 1. Docker Files

#### [NEW] [Dockerfile](file:///e:/LET_ME_COOK/Enterprise%20RAG%20system/Dockerfile)
Create a Dockerfile using a slim Python image to package the FastAPI application. We will use `uv` for fast, reliable package installation.

*   **Base Image:** `python:3.10-slim`
*   **Steps:**
    1.  Install system dependencies (`curl`, `build-essential`).
    2.  Install `uv` package manager (`pip install uv`).
    3.  Set working directory to `/app`.
    4.  Copy dependency files (`pyproject.toml`, `uv.lock`) and perform a frozen sync to build the virtual environment.
    5.  Copy code folders (`src/`, `frontend/`, `app.py`, `main.py`, `scripts/`, `sample_data/`).
    6.  Expose port `8000`.
    7.  Default Command: Run Uvicorn server bound to `0.0.0.0:8000`.

#### [NEW] [.dockerignore](file:///e:/LET_ME_COOK/Enterprise%20RAG%20system/.dockerignore)
Create a `.dockerignore` file to prevent heavy or local development-specific files from entering the Docker build context.
*   **Ignored paths:** `.git`, `.venv`, `__pycache__`, `cache/`, `data/`, `.env`, `*.log`, `*.zip`.

---

### 2. Multi-Container Orchestration

#### [MODIFY] [docker-compose.yml](file:///e:/LET_ME_COOK/Enterprise%20RAG%20system/docker-compose.yml)
Update the existing docker-compose configuration to add the FastAPI web application service (`web`), link it to the databases, and establish the internal docker network.

```yaml
services:
  qdrant:
    image: qdrant/qdrant:latest
    container_name: rag-qdrant
    ports:
      - "6333:6333"   # HTTP API + Dashboard
      - "6334:6334"   # gRPC
    volumes:
      - qdrant_data:/qdrant/storage
    restart: unless-stopped

  neo4j:
    image: neo4j:5-community
    container_name: rag-neo4j
    ports:
      - "7474:7474"   # Browser UI
      - "7687:7687"   # Bolt protocol
    environment:
      NEO4J_AUTH: neo4j/password123
      NEO4J_PLUGINS: '["apoc"]'
    volumes:
      - neo4j_data:/data
    restart: unless-stopped

  web:
    build: .
    container_name: rag-web
    ports:
      - "8000:8000"   # FastAPI Backend + Frontend Static UI
    environment:
      # Override hosts to use Docker internal DNS names
      - QDRANT_HOST=qdrant
      - QDRANT_PORT=6333
      - NEO4J_URI=bolt://neo4j:7687
      - NEO4J_USER=neo4j
      - NEO4J_PASSWORD=password123
    env_file:
      - .env          # Read OpenAI API keys and other configurations
    volumes:
      # Mount cache and data directories for persistence and inspectability
      - ./data:/app/data
      - ./cache:/app/cache
    depends_on:
      - qdrant
      - neo4j
    restart: unless-stopped

volumes:
  qdrant_data:
  neo4j_data:
```

---

## Verification Plan

### Automated Steps
1.  **Build the application containers:**
    ```bash
    docker compose build
    ```
2.  **Start all services in detached mode:**
    ```bash
    docker compose up -d
    ```
3.  **Inspect container status:**
    ```bash
    docker compose ps
    ```

### Manual Verification
*   **FastAPI API Docs:** Open `http://localhost:8000/docs` to verify endpoints.
*   **Web Chat UI:** Open `http://localhost:8000/` and run sample queries to check if the pipeline completes successfully.
*   **Database Admin Consoles:**
    *   Qdrant Dashboard: `http://localhost:6333/dashboard`
    *   Neo4j Browser: `http://localhost:7474`
