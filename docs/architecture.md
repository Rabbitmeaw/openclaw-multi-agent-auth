# Overall Permission Structure

## 3.1 Physical Directory Structure

```
~/.openclaw/
│
├── workspace/                          # Main Agent private area
│   ├── SOUL.md                         # Agent core identity definition
│   ├── MEMORY.md                       # Long-term memory
│   ├── AUDIT_LOG.md                    # Audit log (Main only)
│   ├── AUDIT_MAINTENANCE_GUIDE.md      # Maintenance operation manual
│   └── .cron_snapshot.json             # Cron task snapshot
│
├── workspace-{label}/                  # Independent private area for each Agent
│   ├── workspace-work/                 # Work assistant private area
│   ├── workspace-study/                # Study assistant private area
│   └── ...
│
├── agents/                             # Agent metadata directory
│   ├── main/                           # Main metadata
│   ├── member1/                        # Member 1 metadata
│   └── ...
│
├── shared/                             # Global shared area
│   ├── AGENT_REGISTRY.md               # Agent registry
│   ├── AGENT_CONSTRAINTS.md            # Permission constraints definition
│   ├── GOVERNANCE.md                   # Governance standards
│   ├── AUDIT_BUFFER.md                 # Audit buffer (all can write)
│   ├── COMPLIANCE_CHECKLIST.md         # Compliance checklist
│   ├── communication/                  # Communication directory
│   │   ├── inbox/
│   │   ├── outbox/
│   │   └── tasks/
│   ├── skills/                         # Shared skills
│   ├── knowledge/                      # Shared knowledge
│   └── assets/                         # Public resources
│
├── cron/                               # Cron scheduler data
│   └── jobs.json                       # Global task storage
│
└── archive/                            # Audit archive
    └── 2026-Q1/                        # Quarterly archive
        ├── audit-logs/
        ├── cron-snapshots/
        └── agent-reports/
```

---

## 3.2 Three-Layer Isolation Model

```
┌─────────────────────────────────────────────────────────────┐
│                    Application Layer                         │
│  - Cron task naming prefix: {label}-{task-name}             │
│  - Agents only operate their own tasks via prompt constraints│
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                    Shared Layer                              │
│  - shared/: Global config, registry, communication space    │
│  - cron/jobs.json: Global task storage                      │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                    Private Layer (Physical Isolation)        │
│  - workspace/: Main private                                 │
│  - workspace-{label}/: Each Agent private                   │
│  - agents/{label}/: Each Agent metadata                     │
└─────────────────────────────────────────────────────────────┘
```

---

## 3.3 Permission Matrix

### 3.3.1 Filesystem Permissions

| Area | Main Agent | Worker Agent |
|------|------------|--------------|
| `~/.openclaw/workspace/` | ✅ Full R/W | ❌ Prohibited |
| `~/.openclaw/workspace-{label}/` | ⚠️ Technically accessible, rule-prohibited | ✅ Own workspace only |
| `~/.openclaw/agents/{label}/` | ⚠️ Technically accessible, rule-prohibited | ✅ Own agents directory only |
| `~/.openclaw/shared/` | ✅ R/W (maintainer) | ✅ Read-only (partial write) |
| `~/.openclaw/cron/jobs.json` | ✅ Global management | ✅ Self-governance (prefix-limited) |
| `~/.openclaw/archive/` | ✅ R/W | ❌ Prohibited |

### 3.3.2 Cron Permissions

| Operation | Main Agent | Worker Agent |
|-----------|------------|--------------|
| Create Task | ✅ Any name | ✅ Must have `{label}-` prefix |
| Modify Task | ✅ Any task | ✅ Only own prefix tasks |
| Delete Task | ✅ Any task | ✅ Only own prefix tasks |
| View Tasks | ✅ All | ✅ All (for self-check) |
| Operate Others' Tasks | ✅ Can do (when arbitrating) | ❌ Strictly prohibited |

### 3.3.3 Audit Permissions

| File | Main Agent | Worker Agent |
|------|------------|--------------|
| `workspace/AUDIT_LOG.md` | ✅ R/W | ❌ Prohibited |
| `shared/AUDIT_BUFFER.md` | ✅ R/W | ✅ Append |
| `archive/` | ✅ R/W | ❌ Prohibited |

---

## 3.4 Main Agent's Special Status

### Technical Capability vs Rule Permission

```
┌─────────────────────────────────────────┐
│  Technical Layer                         │
│  - Filesystem allows Main to access any │
│  - Tool calls have no hard ACL limits   │
└─────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────┐
│  Rule Layer (Prompt Constraints)         │
│  - Main respects physical isolation      │
│  - Worker only operates own cron tasks   │
│  - All Agents self-constrain via naming  │
└─────────────────────────────────────────┘
```

### Conditions for Main Agent to Access Other Agents' Private Areas

Only in the following cases:
1. **Explicit Authorization**: Other Agent or user explicitly requests
2. **System Maintenance**: Cleanup, backup, troubleshooting
3. **Debug Support**: Assisting with other Agent issues
4. **Emergency Intervention**: System-level security issues

**Default Prohibition**: Proactively reading/modifying other Agents' private files during daily tasks

---

## 3.5 Namespace Standards

### Cron Task Naming

```
Format: {label}-{task-name}

Examples:
  work-daily-report
  work-weekly-report
  study-learning-plan
  life-water-reminder
```

### Label Assignment Rules

| Agent Type | Label Examples | Description |
|------------|----------------|-------------|
| Main | `main` | Fixed, coordinator |
| Personal Roles | `work`, `study`, `life` | Named by role |
| Team Members | `zhangsan`, `lisi` | Named by pinyin name |
| Enterprise Depts | `sales`, `hr`, `dev` | Named by department |

### Naming Conflict Resolution

- Check prefix uniqueness when registering new Agent
- Main Agent intervenes to coordinate when conflicts are found
- Later registrants usually need to change labels

---

## 3.6 Communication Mechanisms

### 3.6.1 Direct Messages

```
┌─────────┐      sessions_send       ┌─────────┐
│ Agent A │ ────────────────────────▶│ Agent B │
└─────────┘                        └─────────┘
```

Use cases: Urgent notifications, instant collaboration

### 3.6.2 Shared Files

```
┌─────────┐    write to shared/    ┌─────────┐
│ Agent A │ ──────────────────────▶│ Agent B │
│         │◄───────────────────────│         │
└─────────┘    read from shared/   └─────────┘
```

Use cases: Non-instant communication, document sharing

### 3.6.3 Cron Task Coordination

```
- Each Agent independently creates tasks
- Main periodically checks global status
- Main intervenes to arbitrate when conflicts occur
```

---

## 3.7 Audit Architecture

### Data Flow

```
Worker Agent executes operation
       ↓
Writes to shared/AUDIT_BUFFER.md
       ↓
       │ Every Sunday 9:00
       ↓
Main Agent consumes buffer
       ↓
Consolidates to workspace/AUDIT_LOG.md
       ↓
Quarterly archive to archive/YYYY-Qx/
```

### Audit Record Types

| Type | Recorder | Location | Purpose |
|------|----------|----------|---------|
| Operation Report | Worker | AUDIT_BUFFER.md | Real-time recording |
| Buffer Consolidation | Main | AUDIT_LOG.md | Centralized audit |
| Patrol Discovery | Main | AUDIT_LOG.md | Change detection |
| Quarterly Archive | Main | archive/ | Historical retention |

---

## 3.8 Key Design Decisions

### Decision 1: Soft Isolation vs Hard Isolation

**Choice**: Soft isolation (prompt constraints)

**Rationale**:
- Feasible in OpenClaw environments lacking hard ACL
- Low implementation cost, no development required
- Suitable for trusted environments

**Trade-offs**:
- Cannot prevent malicious Agents
- Relies on Agent self-discipline

### Decision 2: Centralized vs Distributed Cron

**Choice**: Distributed autonomy + Main supervision

**Rationale**:
- Eliminates Main single-point bottleneck
- Improves response speed
- Each Agent can independently manage tasks

**Trade-offs**:
- Requires namespace management
- Arbitration needed when conflicts occur

### Decision 3: Audit Log Location

**Choice**: Main workspace (Main private)

**Rationale**:
- Prevents Workers from tampering with audit records
- Main responsible for audit integrity

**Trade-offs**:
- Workers cannot directly view audit status
- Need to query through Main
