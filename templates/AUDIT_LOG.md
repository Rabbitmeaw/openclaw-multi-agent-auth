# Audit Log

**Location**: `~/.openclaw/workspace/AUDIT_LOG.md` (Main Agent only)
**Purpose**: Record key operations for compliance review and issue tracing
**Update Principle**: Only record change operations, not normal reads

---

## Log Standards

### Recording Scope
| Operation Type | Record? | Description |
|----------------|---------|-------------|
| Cron Task Creation | ✅ Record | Who created which task |
| Cron Task Modification | ✅ Record | Who modified which task, change summary |
| Cron Task Deletion | ✅ Record | Who deleted which task |
| Cross-Agent Access | ✅ Record | Who accessed whose workspace, reason |
| Registry Change | ✅ Record | Who modified AGENT_REGISTRY |
| Compliance Check | ✅ Record | Check result summary |
| Normal Read | ❌ Not recorded | E.g., viewing own task list |
| Own Workspace Operation | ❌ Not recorded | Personal file operations |

### Format Standard
```
[YYYY-MM-DD HH:MM] [ACTION] [AGENT] Details
```

### Archive Strategy
- **Active Log**: Current quarter, retained in this file
- **Historical Logs**: Last quarter automatically archived to `~/.openclaw/archive/YYYY-Qx/`
- **Retention Period**: 4 quarters (1 year), can cleanup after expiration

---

## Log Entries

### 2026-Q1

