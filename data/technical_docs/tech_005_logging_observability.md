# Logging and Observability Guide
**Document Type:** Technical Documentation
**Version:** 1.5.2
**Team:** SRE / Platform

## Logging Standards

### Log Levels
| Level | When to Use |
|-------|-------------|
| ERROR | System failures, unhandled exceptions |
| WARN  | Recoverable issues, deprecated usage |
| INFO  | Service lifecycle events, key business actions |
| DEBUG | Detailed flow for troubleshooting (disable in prod) |

### Structured Log Format (JSON)
```json
{
  "timestamp": "2024-01-15T10:23:45.123Z",
  "level": "ERROR",
  "service": "search-service",
  "trace_id": "abc123def456",
  "span_id": "7890abcd",
  "message": "Vector index query failed",
  "error_code": "ERR_SEARCH_004",
  "duration_ms": 1523,
  "user_id": "usr_9182"
}
```

## Metrics (Prometheus)
Key metrics to expose per service:
```
http_requests_total{method, endpoint, status}
http_request_duration_seconds{method, endpoint, quantile}
db_connection_pool_active
db_query_duration_seconds
cache_hit_ratio
```

## Distributed Tracing
All services must propagate W3C Trace Context headers:
- `traceparent: 00-{trace_id}-{span_id}-01`
- Use OpenTelemetry SDK for instrumentation

## Alerting Rules
| Alert | Threshold | Severity |
|-------|-----------|---------|
| High Error Rate | >5% 5xx over 5min | Critical |
| High Latency | p99 > 2s over 5min | Warning |
| ERR_DB_001 spike | >10 pool exhausted/min | Critical |
| Service down | No heartbeat 2min | Critical |

## Error Codes
| Code | Description |
|------|-------------|
| ERR_OBS_001 | Metrics endpoint not reachable |
| ERR_OBS_002 | Trace context propagation failed |
| ERR_OBS_003 | Log shipper connection lost |
