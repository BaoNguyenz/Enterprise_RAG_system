# TechDocs Lite — Product Specification
**Product ID:** TDDOC-LITE-2024
**Category:** Documentation Platform — Small Teams
**Version:** 3.0.1
**Release Date:** 2024-01-15
**Target Audience:** Startups, small teams (< 20 users)

## Overview
TechDocs Lite is a lightweight, affordable documentation platform for small engineering teams. It offers the core documentation management features without the enterprise complexity.

## Feature Comparison: Lite vs Pro
| Feature | TechDocs Lite | TechDocs Pro |
|---------|--------------|-------------|
| Document Storage | 10GB | Up to 10TB |
| Users | Up to 15 | Unlimited |
| Semantic Search | Basic keyword only | Full AI-powered |
| Version History | 30 days | Unlimited |
| API Access | Read-only | Full REST + GraphQL |
| SSO | Not included | SAML, OAuth, LDAP |
| Analytics | Basic page views | Full dashboard |
| SLA | 99.5% | 99.9% |
| Support | Email (48h response) | Priority (4h response) |
| AI Assistant (TDAI) | Not available | Available as add-on |

## Technical Specifications
| Spec | Value |
|------|-------|
| Deployment | SaaS only |
| Storage | 10GB per workspace |
| File formats | MD, PDF, DOCX |
| Max document size | 10MB |
| API rate limit | 100 req/min |

## Pricing
| Plan | Price | Users |
|------|-------|-------|
| Free | $0 | Up to 3 |
| Team | $19/month | Up to 15 |

## Upgrade Path
Teams outgrowing TechDocs Lite can migrate to TechDocs Pro (TDPRO-2024).
Migration tool: `techdocs-migrate --from=lite --to=pro`
All documents, users, and settings are preserved during migration.

## Known Limitations
- No real-time collaboration (only one editor at a time)
- No private cloud or on-premise deployment
- No custom domain support on Free plan
- TDAI-2024 AI Assistant not compatible

## Related Products
- TDPRO-2024: TechDocs Pro (upgrade path)
