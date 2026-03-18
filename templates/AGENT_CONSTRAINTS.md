# Agent Permission Constraints

**Applies To**: All Worker Agents
**Effective Date**: 2026-03-18

---

## Core Rules

### 1. Cron Task Autonomy

#### ✅ Allowed Operations
- Create scheduled tasks (must have `{label}-` prefix)
- Modify own tasks (starting with `{label}-`)
- Delete own tasks
- View all tasks (for self-check)

#### ❌ Prohibited Operations
- Create tasks without prefix
- Use others' prefixes
- Modify/delete others' tasks

#### Naming Standards
```
Format: {label}-{task-name}
Examples: {label1}-morning-report, {label2}-video-publish

How to determine your label:
Read ~/.openclaw/shared/AGENT_REGISTRY.md
```

### 2. Audit Reporting (Mandatory)

When executing the following operations, **must** append to `~/.openclaw/shared/AUDIT_BUFFER.md`:

| Operation | Format |
|-----------|--------|
| Create Task | `[YYYY-MM-DD HH:MM] [{label}] [CRON_CREATE] Task: xxx, schedule: yyy` |
| Modify Task | `[YYYY-MM-DD HH:MM] [{label}] [CRON_UPDATE] Task: xxx, Change: yyy` |
| Delete Task | `[YYYY-MM-DD HH:MM] [{label}] [CRON_DELETE] Task: xxx` |

### 3. Filesystem Boundaries

#### ✅ Accessible
- `workspace-{label}/` — Your private area
- `shared/` — Shared area (by subdirectory permissions)
- `shared/AUDIT_BUFFER.md` — Audit reporting

#### ❌ Prohibited Access
- `workspace/` — Main Agent private area
- Other Agents' workspaces

---

## Execution Flow

### Correct Steps for Creating Cron Tasks

```
1. Confirm Identity
   → Read ~/.openclaw/shared/AGENT_REGISTRY.md
   → Determine your label

2. Create Task
   → cron add --name "{label}-task-name" ...
   → Ensure name contains {label}- prefix

3. Record Audit
   → Append to ~/.openclaw/shared/AUDIT_BUFFER.md
   → Format: [Time] [label] [CRON_CREATE] Details

4. Verify
   → cron list | grep "{label}-"
   → Confirm task creation successful
```

### Correct Steps for Modifying/Deleting Tasks

```
1. List Your Tasks
   → cron list | grep "{label}-"

2. Confirm Target Task is Yours
   → Check if task name starts with {label}-

3. Execute Modify/Delete
   → cron update --jobId xxx ...
   → Or cron remove --jobId xxx

4. Record Audit
   → Append to AUDIT_BUFFER.md
```

---

## Conflict Handling

If you discover the following situations, **immediately stop operations and contact Main Agent**:
- Task name you want to create is already used by others
- You're unsure if a task belongs to you
- You accidentally deleted others' tasks
- You discover system anomalies

---

## Relationship with Main Agent

| Scenario | Your Action | Main Agent's Role |
|----------|-------------|-------------------|
| Daily Cron Management | Self-create/modify/delete | No intervention |
| Naming Conflict | Stop operation, contact Main | Arbitration coordination |
| Need Help | Contact via shared/communication/ or Feishu | Provide support |
| System Maintenance | Cooperate with Main's requirements | Unified scheduling |

---

## Key Files

| File | Path | Purpose |
|------|------|---------|
| This File | `~/.openclaw/shared/AGENT_CONSTRAINTS.md` | Permission constraints |
| Registry | `~/.openclaw/shared/AGENT_REGISTRY.md` | Identity confirmation |
| Audit Buffer | `~/.openclaw/shared/AUDIT_BUFFER.md` | Operation reporting |
| Audit Log | `~/.openclaw/workspace/AUDIT_LOG.md` | Formal audit record (Main only) |
| Governance | `~/.openclaw/shared/GOVERNANCE.md` | Enterprise standards |
| Maintenance Guide | `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` | Main Agent maintenance manual |
| Quarterly Archive | `~/.openclaw/archive/YYYY-Qx/` | Historical audit data archive |
