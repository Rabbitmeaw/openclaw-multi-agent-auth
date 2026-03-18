# Security Policy

## Security Notice

**⚠️ Important Notice**

This solution adopts a **"Soft Isolation"** design:

- Relies on Agent self-discipline and prompt constraints, **no hard ACL enforcement**
- Suitable for **trusted environments**, NOT for zero-trust/high-compliance scenarios
- **DO NOT USE for** financial, military, medical, or other high-security requirement scenarios

### Solution Limitations

| Limitation | Description |
|------------|-------------|
| No Mandatory Access Control | Agents can technically access all directories, relies on self-discipline |
| Trust-Based Model | Assumes Agents will comply with prompt constraints |
| Audit Can Be Bypassed | Worker Agents can choose not to report to audit logs |

For hard security guarantees, please use systems with ACL support.

---

## Reporting Security Issues

If you discover security issues or vulnerabilities:

### Do Not Report Publicly

Please do NOT report security issues via public GitHub Issues.

### Private Reporting Process

1. Send email to: [Security contact email to be filled]
2. Email subject: `[Security] OpenClaw Security Issue Report`
3. Include:
   - Issue description
   - Reproduction steps (if applicable)
   - Potential impact
   - Suggested fix (if any)

### Response Process

| Time | Action |
|------|--------|
| Within 24 hours | Acknowledge receipt |
| Within 72 hours | Initial assessment |
| Within 7 days | Fix plan or explanation |
| After fix | Public acknowledgment (if permitted) |

---

## Known Limitations

This solution has the following known security limitations:

### 1. Soft Isolation Relies on Self-Discipline

- **Risk**: Agents can technically bypass constraints
- **Mitigation**: Deploy only in trusted environments

### 2. Audit Log Integrity

- **Risk**: Workers may not report operations
- **Mitigation**: Main Agent regular patrol discovers anomalies

### 3. No Encryption Protection

- **Risk**: Data stored in plaintext
- **Mitigation**: Ensure filesystem itself is secure

---

## Security Best Practices

When using this solution, recommended measures:

### Deployment Environment

- [ ] Ensure OpenClaw gateway deployed in secure environment
- [ ] Restrict filesystem access to `~/.openclaw/` directory
- [ ] Regularly backup audit logs

### Usage Standards

- [ ] Regularly check `AUDIT_LOG.md` for anomalies
- [ ] Promptly cleanup data for deactivated Agents
- [ ] Restrict Main Agent access scope

### Monitoring Recommendations

- [ ] Set audit log file change alerts
- [ ] Regularly check Cron task naming standards
- [ ] Quarterly review Agent registry

---

## Version History

| Date | Changes |
|------|---------|
| 2026-03-18 | Initial security policy |

---

**Last Updated**: 2026-03-18
