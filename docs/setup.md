# Setup Instructions and Implementation Details

## 4.1 Prerequisites

- OpenClaw gateway installed and running
- At least one Main Agent workspace
- Familiar with OpenClaw basic operations

---

## 4.2 Quick Setup (5 minutes)

### Step 1: Copy Template Files

```bash
cd ~/.openclaw

# Create shared area directories
mkdir -p shared/communication/{inbox,outbox,tasks}
mkdir -p shared/{skills,knowledge,assets,temp}
mkdir -p archive

# Copy template files
cp /path/to/templates/AGENT_REGISTRY.md shared/
cp /path/to/templates/AGENT_CONSTRAINTS.md shared/
cp /path/to/templates/GOVERNANCE.md shared/
cp /path/to/templates/AUDIT_BUFFER.md shared/
cp /path/to/templates/COMPLIANCE_CHECKLIST.md shared/
cp /path/to/templates/AUDIT_LOG.md workspace/
cp /path/to/templates/AUDIT_MAINTENANCE_GUIDE.md workspace/
```

### Step 2: Configure Main Agent

Edit `~/.openclaw/workspace/SOUL.md`, add audit responsibility section:

```markdown
## Audit & Compliance Responsibilities

### Audit Log Maintenance (Automated)

#### Log Locations
- **Formal Log**: `~/.openclaw/workspace/AUDIT_LOG.md`
- **Buffer Log**: `~/.openclaw/shared/AUDIT_BUFFER.md`

#### Maintenance Tasks

Create scheduled task for every Sunday at 9:00:

```bash
openclaw cron add \
  --name "main-audit-maintenance" \
  --cron "0 9 * * 0" \
  --session isolated \
  --message "Execute audit maintenance, see AUDIT_MAINTENANCE_GUIDE.md" \
  --announce \
  --to "your_open_id"
```

#### Execution Content
1. Consume AUDIT_BUFFER.md to AUDIT_LOG.md
2. Cron change patrol (compare snapshot)
3. Quarterly archive check

See `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` for detailed steps
```

### Step 3: Register Worker Agents

Edit `~/.openclaw/shared/AGENT_REGISTRY.md`:

```markdown
| Label | Name | Cron Prefix | Workspace Path | Permissions |
|-------|------|-------------|----------------|-------------|
| main | Main Agent | `main-` | `workspace/` | Global Management |
| work | Work Agent | `work-` | `workspace-work/` | Autonomous |
| study | Study Agent | `study-` | `workspace-study/` | Autonomous |
```

### Step 4: Create Worker Agent Workspaces

```bash
# Create workspace for each Worker Agent
mkdir -p ~/.openclaw/workspace-work
mkdir -p ~/.openclaw/workspace-study

# Copy and modify SOUL.md
cp templates/SOUL_WORKER.md ~/.openclaw/workspace-work/SOUL.md
cp templates/SOUL_WORKER.md ~/.openclaw/workspace-study/SOUL.md

# Modify SOUL.md {label} to actual values (work, study, etc.)
```

### Step 5: Initialize Audit System

```bash
# Create initial snapshot
openclaw cron list > ~/.openclaw/workspace/.cron_snapshot.json

# Record initialization log
echo "## Audit Record - $(date +%Y-%m-%d) (Initialization)" >> ~/.openclaw/workspace/AUDIT_LOG.md
echo "" >> ~/.openclaw/workspace/AUDIT_LOG.md
echo "System initialization complete" >> ~/.openclaw/workspace/AUDIT_LOG.md
```

### Step 6: Restart Gateway

```bash
openclaw gateway restart
```

---

## 4.3 Detailed Setup (Full Version)

See original document for complete setup instructions including:
- Physical isolation structure creation
- Agent registry configuration
- Worker Agent permission constraints
- Worker Agent SOUL.md configuration
- Audit buffer and log file creation
- Maintenance guide setup

---

## 4.4 Verification

### 4.4.1 Filesystem Verification

```bash
# Check directory structure
ls -la ~/.openclaw/workspace/
ls -la ~/.openclaw/workspace-{label}/
ls -la ~/.openclaw/shared/

# Check files
head ~/.openclaw/shared/AGENT_REGISTRY.md
head ~/.openclaw/workspace/AUDIT_LOG.md
```

### 4.4.2 Permission Verification

```bash
# Test Worker accessing own workspace (should succeed)
cat ~/.openclaw/workspace-{label}/SOUL.md

# Test Worker reading shared (should succeed)
cat ~/.openclaw/shared/AGENT_REGISTRY.md

# Test Cron creation (requires actual execution)
openclaw cron add --name "{label}-test-task" --at "5m" ...
```

### 4.4.3 Audit Verification

```bash
# Check buffer file
cat ~/.openclaw/shared/AUDIT_BUFFER.md

# Check log file
cat ~/.openclaw/workspace/AUDIT_LOG.md

# Check snapshot
ls -la ~/.openclaw/workspace/.cron_snapshot.json
```

---

## 4.5 Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Cannot create Cron | Gateway not started | Check `openclaw gateway status` |
| Naming conflict | Duplicate prefix | Modify label, update registry |
| Audit not recording | Not appended to buffer | Check AUDIT_BUFFER.md path |
| Maintenance task not executing | Cron expression error | Check syntax, recreate task |
