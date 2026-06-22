# Technical Documentation & Policy Dataset (TechDocs Inc.)

This directory contains synthetic enterprise documents created to evaluate and benchmark the **Enterprise RAG System**.

> [!NOTE]
> All files in this dataset were generated synthetically using **Claude** (Anthropic) to mimic real-world technical and corporate documentation scenarios.

---

## 📁 Dataset Structure

The documents are divided into three logical domains:

### 1. `policy_docs/`
Corporate policy guidelines, compliance standards, and internal operating rules:
- `policy_001_data_privacy.md`: User data handling policies and GDPR compliance.
- `policy_002_information_security.md`: Password requirements, access control list (ACL) rules, and network security.
- `policy_003_acceptable_use.md`: Guidelines for using corporate equipment and software.
- `policy_004_vendor_management.md`: Compliance requirements for third-party service providers.
- `policy_005_incident_response.md`: Protocol for reporting and resolving security breaches.
- `policy_006_remote_work.md`: Remote access protocols, VPN usage, and device security.
- `policy_008_ai_tool_usage.md`: Corporate boundaries and safe guidelines for using LLMs and AI coding assistants.

### 2. `product_catalog/`
Product specifications, SLA limits, and tiers for TechDocs Inc. products:
- `product_001_techdocs_pro.md`: Premium documentation publishing platform.
- `product_002_techdocs_ai_assistant.md`: AI-powered query and search integration.
- `product_003_techdocs_lite.md`: Standard lightweight documentation hosting.
- `product_004_techdocs_analytics.md`: Analytics and dashboard tools.
- `product_005_techdocs_onprem.md`: On-premises hosting options.
- `product_006_support_plans.md`: Tiered customer support SLA models.

### 3. `technical_docs/`
Developer guides, system architecture reviews, API contracts, and troubleshooting manuals:
- `tech_001_api_authentication.md`: OAuth2 details, token lifetimes, and security headers.
- `tech_002_database_pooling.md`: Database connection pooling parameters and max connections.
- `tech_003_microservices_deployment.md`: Service topologies and Kubernetes configs.
- `tech_004_search_api.md`: Payload specs and endpoints for documentation search.
- `tech_005_logging_observability.md`: Logging formats and Prometheus metric setups.
- `tech_006_backup_recovery.md`: DB backup policies and recovery timelines (RTO/RPO).
- `tech_007_cicd_pipeline.md`: Jenkins build workflows, testing stages, and deployment triggers.

---

## 🎯 Purpose of Synthetic Data
Using Claude-generated data allows for:
1. **No Sensitive Information Leaks:** Completely clean, non-confidential mock data.
2. **Dense Semantic & Keyword Features:** Specifically engineered to have keyword overlap (for BM25 test) alongside semantic variations (for Vector & Reranking tests).
3. **Structured Entity-Relation Extraction:** Rich relationships between products, technologies, policies, and stakeholders to test Neo4j GraphRAG performance.
