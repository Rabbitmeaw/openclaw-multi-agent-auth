# Compliance Checklist

**Purpose**: Record Main Agent's automated audit execution
**Execution Method**: Automated (every Sunday 9:00, via Cron task `main-audit-maintenance`)
**Manual Review**: Quarterly (check automated execution effectiveness)
**Record Location**: This file records exceptions, normal execution records see `~/.openclaw/workspace/AUDIT_LOG.md`

---

## Check Item Template

Copy the following template each check, fill in date and results:

```markdown
## Check Record: YYYY-MM-DD

**Executor**: Main Agent
**Check Result**: ✅ Passed / ⚠️ Found N issues / ❌ Needs handling

### Check Items
- [ ] Audit log integrity check
- [ ] Prefixless task scan
- [ ] Abnormal access pattern check
- [ ] Log archive (when quarter switches)

### Issues Found
| No. | Issue Description | Severity | Handling Measure | Status |
|-----|-------------------|----------|------------------|--------|
| 1 | - | - | - | - |

### Notes
-
```

---

## Check Methods

### 1. Audit Log Integrity
```bash
# Check if log file exists and format is correct
cat ~/.openclaw/workspace/AUDIT_LOG.md

# Confirm this quarter has records (if quarter start, confirm last quarter archived)
```

### 2. Prefixless Task Scan
```bash
# List all tasks
openclaw cron list

# Check if any task names don't start with "{label}-"
# Valid prefix list:
# main-, {label1}-, {label2}-, ...
```

### 3. Abnormal Access Patterns
```bash
# Check audit log for:
# - Frequent cross-Agent access
# - Unrecorded access reasons
# - Same Agent multiple violations
```

### 4. Log Archive (Execute when quarter switches)
```bash
# First day of each quarter:
# 1. Create quarterly archive directory structure (if not exists)
# mkdir -p ~/.openclaw/archive/YYYY-Qx/{audit-logs,cron-snapshots,agent-reports}
#
# Example: ~/.openclaw/archive/2026-Q1/
#        ├── audit-logs/          # Audit log archive
#        ├── cron-snapshots/      # Cron task snapshots
#        └── agent-reports/       # Agent activity reports

# 2. Archive this quarter's data
# cp ~/.openclaw/workspace/AUDIT_LOG.md \
#    ~/.openclaw/archive/YYYY-Qx/audit-logs/
# cp ~/.openclaw/workspace/.cron_snapshot.json \
#    ~/.openclaw/archive/YYYY-Qx/cron-snapshots/

# 3. Create new block in AUDIT_LOG.md, start new quarter recording
```

---

## Historical Check Records

### Check Record: YYYY-MM-DD (Initialization)

**Executor**: Main Agent
**Check Result**: ✅ Passed

### Check Items
- [x] Audit log integrity check
- [x] Prefixless task scan
- [x] Abnormal access pattern check
- [ ] Log archive (when quarter switches) - Not quarter switch, skip

### Issues Found
None

### Notes
- Initial check, audit logging system just established
- All Cron tasks renamed per standards
- No prefixless tasks found

---

## Quarterly Archive Records

| Quarter | Archive Directory | Content | Status |
|---------|-------------------|---------|--------|
| - | - | - | - |

