# Microservices Deployment Specification
**Document Type:** Technical Documentation
**Version:** 3.1.0
**Team:** Platform Engineering

## Architecture Overview
TechDocs platform is composed of 6 core microservices communicating via REST and gRPC:
- **doc-service**: Document storage and retrieval
- **search-service**: Full-text and semantic search
- **auth-service**: Authentication and authorization
- **notification-service**: Email and webhook notifications
- **analytics-service**: Usage metrics and reporting
- **gateway-service**: API gateway and rate limiting

## Deployment Requirements

### Resource Specifications
| Service | CPU Request | CPU Limit | Memory Request | Memory Limit | Replicas |
|---------|------------|-----------|---------------|--------------|---------|
| doc-service | 250m | 1000m | 512Mi | 2Gi | 3 |
| search-service | 500m | 2000m | 1Gi | 4Gi | 3 |
| auth-service | 100m | 500m | 256Mi | 512Mi | 2 |
| notification-service | 100m | 300m | 128Mi | 256Mi | 2 |
| analytics-service | 200m | 800m | 512Mi | 1Gi | 1 |
| gateway-service | 250m | 1000m | 256Mi | 512Mi | 2 |

## Kubernetes Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: search-service
  namespace: techdocs-prod
spec:
  replicas: 3
  selector:
    matchLabels:
      app: search-service
  template:
    spec:
      containers:
        - name: search-service
          image: techdocs/search-service:3.1.0
          resources:
            requests:
              cpu: 500m
              memory: 1Gi
```

## Error Codes
| Code | Description |
|------|-------------|
| ERR_DEPLOY_001 | Image pull failed — check registry credentials |
| ERR_DEPLOY_002 | Insufficient cluster resources |
| ERR_DEPLOY_003 | Health check failed — service not ready |
| ERR_DEPLOY_004 | ConfigMap or Secret not found |

## Health Check Endpoints
Every service must expose:
- `GET /health/live` — liveness probe
- `GET /health/ready` — readiness probe
