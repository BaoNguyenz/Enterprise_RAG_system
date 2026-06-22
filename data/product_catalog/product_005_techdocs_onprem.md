# TechDocs On-Premise Edition — Product Specification
**Product ID:** TDONPREM-2024
**Category:** Enterprise — Self-Hosted
**Version:** 5.2.0
**Target Audience:** Enterprises with strict data residency or air-gapped requirements

## Overview
TechDocs On-Premise Edition provides the full TechDocs Pro feature set deployed entirely within the customer's own infrastructure. Suitable for government, financial services, healthcare, and defense industries.

## Deployment Options
| Option | Description |
|--------|-------------|
| Kubernetes (recommended) | Helm chart deployment, auto-scaling |
| Docker Compose | Single-node, suitable for < 100 users |
| Bare Metal | Custom installation via installer script |
| Air-Gapped | Full offline deployment with local model serving |

## System Requirements
### Minimum (up to 50 users)
| Component | Requirement |
|-----------|------------|
| CPU | 8 cores |
| RAM | 32GB |
| Storage | 500GB SSD |
| OS | Ubuntu 22.04 LTS or RHEL 8+ |

### Recommended (up to 500 users)
| Component | Requirement |
|-----------|------------|
| CPU | 32 cores |
| RAM | 128GB |
| Storage | 5TB SSD RAID |
| GPU | Optional: NVIDIA A10 for local AI |

## Included Components
- TechDocs Application Server
- PostgreSQL 15 (bundled)
- Elasticsearch 8 (bundled)
- Redis 7 (bundled)
- Nginx reverse proxy config
- Prometheus + Grafana monitoring stack

## AI Features (On-Premise)
| Model Option | Description |
|-------------|-------------|
| Cloud mode | Route to OpenAI/Azure OpenAI (requires internet) |
| Local mode | Run Mistral-7B or Llama 3 locally (requires GPU) |
| Hybrid mode | Local embedding, cloud LLM generation |

## Licensing
- Annual license fee (contact sales)
- License Model ID: LIC-ONPREM-ENT-2024
- Includes 12 months support and updates

## Related Products
- TDPRO-2024: SaaS equivalent
- TDAI-2024: AI Assistant (compatible in hybrid/local mode)
