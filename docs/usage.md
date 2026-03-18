# Usage Guide

## 5.1 Worker Agent Operation Guide

### Creating Cron Tasks

```javascript
// Step 1: Confirm identity
const registry = read("~/.openclaw/shared/AGENT_REGISTRY.md");
const myLabel = "{label}";  // Your label

// Step 2: Create task (must have prefix)
cron add({
  name: "{label}-daily-report",        // ✅ Correct: with prefix
  schedule: { kind: "cron", expr: "0 9 * * *" },
  payload: {
    kind: "agentTurn",
    message: "Execute daily report generation..."
  },
  delivery: {
    mode: "announce",
    channel: "feishu",
    to: "user_open_id"
  }
});

// Step 3: Record audit (required)
appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
  "[2026-03-18 14:30] [{label}] [CRON_CREATE] Task: {label}-daily-report, schedule: 0 9 * * *\n");

// Step 4: Verify creation
const myJobs = cron.list().filter(j => j.name.startsWith("{label}-"));
```

### Modifying Cron Tasks

```javascript
// Step 1: List your tasks
const myJobs = cron.list().filter(j => j.name.startsWith("{label}-"));

// Step 2: Confirm task belongs to you
const targetJob = myJobs.find(j => j.id === targetJobId);
if (!targetJob) {
  // ❌ Prohibited from operating others' tasks
  notify("Error: Can only operate your own tasks");
  return;
}

// Step 3: Execute modification
cron.update({
  jobId: targetJobId,
  patch: { schedule: { expr: "0 10 * * *" } }
});

// Step 4: Record audit
appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
  "[2026-03-18 15:00] [{label}] [CRON_UPDATE] Task: {label}-xxx, Change: schedule adjusted\n");
```

### Deleting Cron Tasks

```javascript
// Can only delete your own tasks
const myJobs = cron.list().filter(j => j.name.startsWith("{label}-"));
if (myJobs.some(j => j.id === targetJobId)) {
  cron.remove({ jobId: targetJobId });

  // Record audit
  appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
    "[2026-03-18 16:00] [{label}] [CRON_DELETE] Task: {label}-xxx\n");
}
```

---

## 5.2 Main Agent Operation Guide

### Supervising Global Tasks

```javascript
// View all tasks
const allJobs = cron.list();

// Check naming standards
const violations = allJobs.filter(j => {
  const validPrefixes = ["main-", "{label1}-", "{label2}-", ...];
  return !validPrefixes.some(p => j.name.startsWith(p));
});

if (violations.length > 0) {
  notify(`Found ${violations.length} tasks violating naming standards`);
  // Notify relevant Agents to correct
}
```

### Arbitrating Conflicts

```javascript
// Process flow when prefix conflict discovered
function resolveConflict(agentA, agentB, prefix) {
  // 1. Contact both Agents
  sessions_send({ label: agentA, message: "Prefix conflict notification..." });
  sessions_send({ label: agentB, message: "Prefix conflict notification..." });

  // 2. Negotiate solution
  // - Option 1: One party changes prefix
  // - Option 2: Merge tasks
  // - Option 3: Force rename (emergency only)

  // 3. Execute solution
  cron.update({ jobId: xxx, patch: { name: "new-prefix-task" } });

  // 4. Record audit
  appendToFile("~/.openclaw/workspace/AUDIT_LOG.md",
    "[Time] [main] [ARBITRATION] Conflict resolved: agentA vs agentB, Solution: xxx");
}
```

### Audit Maintenance (Auto-executes every Sunday 9:00)

See `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md`

```javascript
// Consume buffer logs
const buffer = read("~/.openclaw/shared/AUDIT_BUFFER.md");
// Extract entries → Append to AUDIT_LOG.md → Clear buffer

// Cron change patrol
const currentJobs = cron.list();
const lastSnapshot = readJson("~/.openclaw/workspace/.cron_snapshot.json");
// Compare differences → Record changes → Save new snapshot

// Quarterly archive check
if (isQuarterStart()) {
  // Archive this quarter's data → Create new quarter directory
}
```

---

## 5.3 User Query Operations

### View All Tasks

```bash
openclaw cron list
```

### Filter Tasks by Prefix

```bash
# View all tasks for {label}
openclaw cron list | grep "{label}-"
```

### View Audit Logs (Main only)

```bash
cat ~/.openclaw/workspace/AUDIT_LOG.md
```

### View Agent Registry

```bash
cat ~/.openclaw/shared/AGENT_REGISTRY.md
```

---

## 5.4 FAQ

### Q: How does a Worker Agent know which prefix to use?

A: Read `~/.openclaw/shared/AGENT_REGISTRY.md`, find your label's corresponding Cron prefix.

### Q: What if I accidentally create a task without a prefix?

A: Main will discover it during regular patrol, contact the Agent to request rename or cleanup.

### Q: Can a Worker modify other Agents' tasks?

A: Technically possible (cron tool has no hard limits), but prohibited by rules. Violators are arbitrated by Main.

### Q: Can Main access Worker's workspace?

A: Technically possible, but by rules only when explicitly authorized, for system maintenance, debug support, or emergency intervention.

### Q: How to add a new Agent?

1. Create new workspace: `workspace-{label}/`
2. Register in `AGENT_REGISTRY.md`
3. Assign Cron prefix
4. Create SOUL.md (including permission constraints)

---

## 5.5 Troubleshooting

| Issue | Check | Solution |
|-------|-------|----------|
| Task naming conflict | `AGENT_REGISTRY.md` | Contact Main to coordinate prefix |
| Accidentally deleted others' task | `cron list` history | Restore from backup or recreate |
| Task not executing | `cron status` | Check Gateway status |
| Permission dispute | `AGENT_REGISTRY.md` | Main arbitration |
| Audit not recording | `AUDIT_BUFFER.md` permissions | Check if file is writable |

---

## 5.6 Best Practices

### Naming Standards

- Use meaningful task names: `{label}-function-description`
- Examples: `work-daily-morning-push`, `study-english-daily-words`

### Audit Recording

- Record promptly, don't backfill
- Include sufficient context (time, operation, details)
- Regularly check audit log integrity

### Collaboration Communication

- Prioritize using `shared/communication/`
- Contact Main directly for emergencies
- Keep communication records for traceability

### Regular Maintenance

- Main Agent executes audit maintenance weekly
- Review archived data quarterly
- Clean up expired data (over 4 quarters)
