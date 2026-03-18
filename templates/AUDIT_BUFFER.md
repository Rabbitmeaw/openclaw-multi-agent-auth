# Audit Buffer (All Agents Can Write)

**Purpose**: Worker Agents record their key operations for Main Agent consolidation
**Write Permissions**: All Agents can append
**Consumption**: Main Agent **automatically weekly** reads and archives (via Cron task)
**Cleanup**: Main Agent automatically clears (no manual handling needed)

---

## Recording Standards

### Who Needs to Record
All Worker Agents

### When to Record
| Operation | Example |
|-----------|---------|
| Cron Task Creation | Created new scheduled task |
| Cron Task Modification | Modified task time, content, etc. |
| Cron Task Deletion | Deleted scheduled task |
| Cross-Agent Access | Accessed other Agent's shared/ content |

### Format
```
[YYYY-MM-DD HH:MM] [AGENT_LABEL] [ACTION] Detail description
```

### Examples
```
[2026-03-18 14:30] [{label1}] [CRON_CREATE] Task: {label1}-evening-report, schedule: 0 18 * * *
[2026-03-18 15:00] [{label2}] [CRON_UPDATE] Task: {label2}-video-publish, Change: time changed to 20:00
[2026-03-18 16:00] [{label3}] [ACCESS] Read: shared/knowledge/enterprise-knowledge-base.md
```

---

## Log Entries

