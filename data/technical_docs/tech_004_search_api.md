# Search Service API Reference
**Document Type:** Technical Documentation
**Version:** 2.0.0
**Base URL:** `/api/v2/search`

## Endpoints

### Full-Text Search
```
GET /api/v2/search/text?q={query}&limit={n}&offset={n}
```
Returns ranked results using BM25 scoring.

**Response:**
```json
{
  "total": 142,
  "results": [
    {
      "doc_id": "DOC-2041",
      "title": "API Authentication Guide",
      "snippet": "...OAuth 2.0 with JWT tokens...",
      "score": 0.87,
      "metadata": { "type": "technical", "version": "2.3.1" }
    }
  ]
}
```

### Semantic Search
```
POST /api/v2/search/semantic
Content-Type: application/json

{
  "query": "how to handle expired tokens",
  "top_k": 10,
  "filters": { "document_type": "technical" }
}
```

### Hybrid Search
```
POST /api/v2/search/hybrid
{
  "query": "ERR_AUTH_002 fix",
  "alpha": 0.5,
  "top_k": 10
}
```
`alpha=0` → pure BM25, `alpha=1` → pure vector, `alpha=0.5` → balanced hybrid.

## Error Codes
| Code | Description |
|------|-------------|
| ERR_SEARCH_001 | Query too long (max 512 tokens) |
| ERR_SEARCH_002 | Invalid filter field |
| ERR_SEARCH_003 | Embedding service unavailable |
| ERR_SEARCH_004 | Index not found or corrupted |

## Rate Limits
- Unauthenticated: 10 requests/minute
- Standard tier: 100 requests/minute
- Enterprise tier: 1000 requests/minute
