# Data Backup and Recovery Procedures
**Document Type:** Technical Documentation
**Version:** 2.1.0
**Criticality:** P0 — Must Follow

## Backup Schedule
| Data Store | Type | Frequency | Retention | Storage |
|-----------|------|-----------|-----------|---------|
| PostgreSQL (primary) | Full snapshot | Daily 02:00 UTC | 30 days | S3 techdocs-backups |
| PostgreSQL (primary) | WAL streaming | Continuous | 7 days | S3 techdocs-wal |
| Neo4j Graph DB | Full export | Daily 03:00 UTC | 14 days | S3 techdocs-graph |
| Vector Index (Chroma) | Snapshot | Weekly Sunday | 4 weeks | S3 techdocs-vectors |
| Object Storage (docs) | Cross-region replication | Real-time | Indefinite | S3 us-east + eu-west |

## Recovery Procedures

### ERR_BACKUP_001: Snapshot Failed
1. Check S3 bucket permissions and available space
2. Verify backup agent is running: `systemctl status techdocs-backup`
3. Trigger manual backup: `./scripts/backup.sh --target postgresql --force`
4. Alert on-call engineer if retry fails

### ERR_BACKUP_002: Restore Validation Failed
Checksums must match after restore:
```bash
pg_restore --validate-only -d techdocs_prod backup_2024_01_15.dump
```

### RTO and RPO Targets
| Scenario | RTO | RPO |
|----------|-----|-----|
| Single service failure | 5 minutes | 0 (HA failover) |
| Database corruption | 1 hour | 1 hour (WAL recovery) |
| Full region outage | 4 hours | 24 hours |

## Error Codes
| Code | Description |
|------|-------------|
| ERR_BACKUP_001 | Scheduled backup failed |
| ERR_BACKUP_002 | Restore validation failed |
| ERR_BACKUP_003 | Replication lag exceeded threshold (>60s) |
| ERR_BACKUP_004 | Backup decryption key not found |
