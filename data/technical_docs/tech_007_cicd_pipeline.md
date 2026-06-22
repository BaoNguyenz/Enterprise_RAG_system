# CI/CD Pipeline Specification
**Document Type:** Technical Documentation
**Version:** 1.9.0
**Tool:** GitHub Actions + ArgoCD

## Pipeline Overview
```
Code Push → Lint & Test → Build Image → Security Scan → Push Registry → Deploy Staging → Integration Tests → Deploy Production
```

## GitHub Actions Workflow
```yaml
name: TechDocs CI/CD
on:
  push:
    branches: [main, release/*]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: |
          pip install -r requirements.txt
          pytest tests/ --cov=src --cov-report=xml
      - name: Coverage check
        run: coverage report --fail-under=80

  build:
    needs: test
    steps:
      - name: Build and push image
        run: |
          docker build -t techdocs/$SERVICE:${{ github.sha }} .
          docker push techdocs/$SERVICE:${{ github.sha }}

  security-scan:
    needs: build
    steps:
      - name: Trivy scan
        uses: aquasecurity/trivy-action@master
        with:
          severity: CRITICAL,HIGH
          exit-code: 1
```

## Deployment Gates
| Gate | Requirement |
|------|-------------|
| Unit test coverage | ≥ 80% |
| Security scan | No CRITICAL CVEs |
| Integration tests | 100% pass |
| Performance test | p99 latency < 500ms |

## Error Codes
| Code | Description |
|------|-------------|
| ERR_CICD_001 | Test coverage below threshold |
| ERR_CICD_002 | Critical CVE detected in image |
| ERR_CICD_003 | Deployment rollout timeout (>10 min) |
| ERR_CICD_004 | Integration test suite failed |

## Rollback Procedure
```bash
argocd app rollback techdocs-prod --revision {previous_revision}
```
Automatic rollback triggers if error rate > 10% within 5 minutes of deploy.
