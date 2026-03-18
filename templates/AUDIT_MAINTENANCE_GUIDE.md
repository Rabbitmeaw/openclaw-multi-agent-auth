# Audit Maintenance Operation Manual

**Task Name**: main-audit-maintenance
**Execution Frequency**: Every Sunday 9:00
**Trigger Method**: Cron scheduled task

---

## Maintenance Objectives

1. Collect audit logs from all Agents
2. Check Cron task changes
3. Verify permission compliance
4. Generate maintenance report

---

## Execution Steps

### Step 1: Collect Audit Buffer

Read audit records written by each Agent:

```bash
# Check if AUDIT_BUFFER.md exists
if [ -f ~/.openclaw/shared/AUDIT_BUFFER.md ]; then
    cat ~/.openclaw/shared/AUDIT_BUFFER.md
else
    echo "No new audit records"
fi
```

**Collection Content**:
- Cron operations reported by each Agent
- Abnormal operation records
- Namespace conflict records

### Step 2: Consolidate to Audit Log

Append collected information to main audit log:

```
~/.openclaw/workspace/AUDIT_LOG.md
```

**Record Format**:
```markdown
## Audit Record - 2026-03-18

### Time Range
Last Sunday to This Saturday

### Collected Audit Items
- [Item 1]
- [Item 2]

### Cron Task Changes
- New: [Task name]
- Modified: [Task name]
- Deleted: [Task name]

### Abnormal Items
- [Abnormal description]

### Handling Measures
- [Measures taken]
```

### Step 3: Check Cron Task Changes

1. **Get Current Task List**:
   ```bash
   openclaw cron list
   ```

2. **Compare with Last Week's Snapshot**:
   - Read `~/.openclaw/workspace/.cron_snapshot.json`
   - Compare differences: new, modified, deleted tasks

3. **Check Naming Standards**:
   - Verify all tasks have correct prefixes
   - Violation formats: `no-prefix-task`, `wrong-prefix-task`

4. **Save This Week's Snapshot**:
   ```bash
   openclaw cron list > ~/.openclaw/workspace/.cron_snapshot.json
   ```

### Step 4: Check Quarterly Archive

1. **Check Archive Directory**:
   ```
   ~/.openclaw/archive/
   └── 2026-Q1/          # Current quarter
       ├── audit-logs/
       ├── cron-snapshots/
       └── agent-reports/
   ```

2. **Archive Condition Judgment**:
   - If it's the last week of quarter, execute archive
   - Move this quarter's audit logs to archive directory

3. **Create New Quarter Directory** (if first week of new quarter):
   ```
   ~/.openclaw/archive/2026-Q2/
   ```

### Step 5: Generate and Send Report

**Report Content**:
```
🔍 Audit Maintenance Report - [Date]

📊 Cron Task Statistics:
- Total tasks: [Count]
- New this week: [Count]
- Modified this week: [Count]
- Naming standard violations: [Count]

📋 Audit Items:
- Records collected: [Count]
- Pending: [Count]
- Processed: [Count]

✅ Maintenance Status: Normal / Attention Needed

📁 Quarterly Archive: [Progress]
```

**Send Method**:
Use `message` tool to send to specified channel

---

## Quick Checklist

- [ ] AUDIT_BUFFER.md has been read
- [ ] AUDIT_LOG.md has been updated
- [ ] Cron list has been compared
- [ ] Naming standards have been checked
- [ ] Quarterly archive has been checked
- [ ] Report has been generated and sent

---

## Exception Handling

| Exception | Handling Measure |
|-----------|------------------|
| AUDIT_BUFFER.md doesn't exist | Record "No new audit records", continue execution |
| Naming standard violation found | Record violation task, notify relevant Agent |
| Permission dispute found | Record details, prepare for arbitration |
| Archive directory permission error | Check file permissions, fix manually if necessary |

---

## Related Files

| File | Path | Description |
|------|------|-------------|
| Audit Buffer | `~/.openclaw/shared/AUDIT_BUFFER.md` | Audit records written by each Agent |
| Audit Log | `~/.openclaw/workspace/AUDIT_LOG.md` | Consolidated main audit log |
| Last Week's Snapshot | `~/.openclaw/workspace/.cron_snapshot.json` | Cron task last week status |
| This Guide | `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` | This file |

---

**Last Updated**: 2026-03-18
**Version**: 1.0
