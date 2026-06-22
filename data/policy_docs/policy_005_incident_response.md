# Incident Response Policy
**Document Type:** Policy
**Policy ID:** POL-005
**Owner:** CISO / SOC Team
**Effective Date:** 2024-01-01

## Purpose
Define the process for detecting, responding to, and recovering from security incidents to minimize business impact.

## Stakeholders
| Role | Responsibility |
|------|---------------|
| SOC Team | First responder, triage |
| CISO | Escalation authority, executive briefing |
| IT Infrastructure | Containment and remediation |
| Legal Department | Regulatory notification decisions |
| PR/Communications | External communications |
| HR Department | Employee-related incidents |
| DPO | Breach notification to regulators |

## Incident Classification
| Category | Examples |
|---------|---------|
| Data Breach | Unauthorized exfiltration of personal data |
| Ransomware | Encryption of company systems |
| Account Compromise | Credential theft, unauthorized login |
| DDoS | Service availability attack |
| Insider Threat | Malicious or negligent employee action |
| Supply Chain Attack | Compromise via third-party vendor |

## Response Phases
### Phase 1: Detection & Analysis (0–30 min)
- SOC receives alert from SIEM (Splunk/Sentinel)
- Assign incident ID: INC-YYYY-NNNN
- Classify severity (P1–P4)

### Phase 2: Containment (30 min – 4 hours)
- Isolate affected systems
- Revoke compromised credentials
- Preserve forensic evidence (do NOT wipe)

### Phase 3: Eradication (4–24 hours)
- Remove malware, close vulnerability
- Reset all potentially compromised accounts

### Phase 4: Recovery (1–7 days)
- Restore from clean backup
- Monitor for re-infection

### Phase 5: Post-Incident Review (within 2 weeks)
- Root cause analysis
- Update runbooks
- Management report

## Regulatory Notification Timelines
| Regulation | Deadline |
|-----------|---------|
| GDPR (DPA) | 72 hours from discovery |
| CCPA | Without unreasonable delay |
| PCI DSS | Immediately to card brands |

## Related Documents
- POL-002: Information Security Policy
- RUN-001 through RUN-012: Incident Response Runbooks
