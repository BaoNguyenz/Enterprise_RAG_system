# Database Connection Pooling Configuration
**Document Type:** Technical Documentation
**Version:** 1.8.0
**Component:** TechDocs DB Layer

## Overview
Connection pooling reduces latency and improves throughput by reusing existing database connections. TechDocs uses HikariCP as the default connection pool manager.

## Configuration Parameters

### Recommended Settings (Production)
```yaml
datasource:
  hikari:
    maximum-pool-size: 20
    minimum-idle: 5
    idle-timeout: 300000       # 5 minutes
    connection-timeout: 30000  # 30 seconds
    max-lifetime: 1800000      # 30 minutes
    leak-detection-threshold: 60000
```

### Development Settings
```yaml
datasource:
  hikari:
    maximum-pool-size: 5
    minimum-idle: 1
    idle-timeout: 60000
```

## Error Codes
| Code | Description | Action |
|------|-------------|--------|
| ERR_DB_001 | Connection pool exhausted | Increase maximum-pool-size or optimize queries |
| ERR_DB_002 | Connection timeout | Check network latency or DB server load |
| ERR_DB_003 | Connection leak detected | Review transaction management in code |
| ERR_DB_004 | SSL handshake failed | Verify SSL certificate and DB server config |

## Monitoring
Monitor pool health using the `/actuator/metrics/hikaricp.connections` endpoint.
Key metrics: `active`, `idle`, `pending`, `timeout`.

## Tuning Guidelines
- Set `maximum-pool-size` = (CPU cores × 2) + number of spindle disks
- Never set `minimum-idle` to 0 in production
- Enable `leak-detection-threshold` in staging to catch connection leaks early
