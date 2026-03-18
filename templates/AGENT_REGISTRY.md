# Agent Identity and Permission Registry

## Physical Isolation Structure

```
~/.openclaw/
├── workspace/                  # Main Agent private area
├── workspace-{label}/          # Independent private area for each Agent
├── agents/{label}/             # Agent metadata
└── shared/                     # Only shared area (where this file is located)
```

## Agent List

| Label | Name | Cron Prefix | Workspace Path | Permissions |
|-------|------|-------------|----------------|-------------|
| main | Main Agent | `main-` | `workspace/` | Global Management |
| {label1} | Agent 1 | `{label1}-` | `workspace-{label1}/` | Autonomous |
| {label2} | Agent 2 | `{label2}-` | `workspace-{label2}/` | Autonomous |
| ... | ... | ... | ... | ... |

**Note**: Replace {label1}, {label2} with actual Agent identifiers (e.g., work, study, zhangsan, etc.)

## Filesystem Permission Boundaries

### Private Areas (Strict Isolation)
- **Main**: `~/.openclaw/workspace/` — Main R/W only
- **{label1}**: `~/.openclaw/workspace-{label1}/` — {label1} R/W only
- **Other Agents**: Same principle

### Shared Area (All read-only, Main can write)
- **Path**: `~/.openclaw/shared/`
- **Access Rules**:
  - All Agents: Read-only access (to read this file, etc.)
  - Main Agent: Read/Write permissions (registry maintenance)

### Main's Special Permissions (Requires Self-Discipline)
- ✅ **Technically can** access all workspaces (filesystem permissions)
- ❌ **Prohibited by rules** from casually accessing other Agents' private areas
- ⚠️ **Only accessible in following cases**:
  1. Received explicit authorization
  2. System maintenance needed
  3. Debug/troubleshooting
  4. Emergency security intervention

## Cron Autonomy Rules (All Agents Must Follow)

### Identity Identification
Each Agent determines identity through current session's `label`.

### Creating Tasks
- ✅ Can create new tasks
- ⚠️ **Mandatory**: Task name must have `{label}-` prefix
- ✅ Examples:
  - `{label1}-TaskA`
  - `{label2}-TaskB`

### Modifying/Deleting Tasks
- ✅ Can only operate tasks starting with `{label}-`
- ❌ **Prohibited**: Operating other prefix tasks
- Before modifying, must first `cron list` and filter for own prefix tasks

### Namespace Conflicts
If attempting to create a task and finding the prefix already occupied:
1. Check if it's your own old task (can update)
2. If it's another Agent's task, contact Main to coordinate
3. Strictly prohibited from forcefully overwriting or deleting others' tasks

## Main Agent's Reserved Permissions

Even when other Agents are autonomous, Main retains:
- View all tasks (supervision)
- Force intervention when conflicts occur
- Cleanup prefixless/zombie tasks
- Modify this registry
- Maintain audit logs (automated)

## Audit and Governance Configuration

| Configuration Item | Path | Description |
|-------------------|------|-------------|
| Audit Log | `~/.openclaw/workspace/AUDIT_LOG.md` | Formal audit record (Main only) |
| Audit Buffer | `~/.openclaw/shared/AUDIT_BUFFER.md` | Worker reporting buffer (all can write) |
| Governance Standards | `~/.openclaw/shared/GOVERNANCE.md` | Enterprise behavior standards |
| Permission Constraints | `~/.openclaw/shared/AGENT_CONSTRAINTS.md` | Worker Agent permission definitions |
| Maintenance Guide | `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` | Main Agent operation manual |
| Maintenance Task | `main-audit-maintenance` (Cron) | Auto-executes every Sunday 9:00 |
| Quarterly Archive | `~/.openclaw/archive/YYYY-Qx/` | Archive directory |
| Log Rotation | Quarterly | Keep last 4 quarters |

## Required Reading for All Agents

All Worker Agents should read at startup:
1. `~/.openclaw/shared/AGENT_REGISTRY.md` — Confirm identity and permissions
2. `~/.openclaw/shared/AGENT_CONSTRAINTS.md` — Understand constraint rules
3. `~/.openclaw/shared/GOVERNANCE.md` — Understand governance standards (if applicable)
