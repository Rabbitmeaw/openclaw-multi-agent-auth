# Enterprise 10-Agent Configuration Example

## Scenario Description

Enterprise department use, approximately 10 employees, requiring complete audit and governance.

## Organization Structure

```
Department
├── Manager (1 person)
├── Team Lead A (1 person) - leads 3-person team
├── Team Lead B (1 person) - leads 3-person team
└── Independent Member (1 person)
```

## Agent Registry Configuration

```markdown
| Label | Name | Cron Prefix | Workspace Path | Permissions | Role |
|-------|------|-------------|----------------|-------------|------|
| main | Main Agent | `main-` | `workspace/` | Global Management | Department Admin |
| manager | Manager Agent | `manager-` | `workspace-manager/` | Autonomous | Department Manager |
| lead-a | Lead A | `lead-a-` | `workspace-lead-a/` | Autonomous | Team Lead A |
| member-a1 | Member A1 | `member-a1-` | `workspace-member-a1/` | Autonomous | Team Member |
| member-a2 | Member A2 | `member-a2-` | `workspace-member-a2/` | Autonomous | Team Member |
| member-a3 | Member A3 | `member-a3-` | `workspace-member-a3/` | Autonomous | Team Member |
| lead-b | Lead B | `lead-b-` | `workspace-lead-b/` | Autonomous | Team Lead B |
| member-b1 | Member B1 | `member-b1-` | `workspace-member-b1/` | Autonomous | Team Member |
| member-b2 | Member B2 | `member-b2-` | `workspace-member-b2/` | Autonomous | Team Member |
| member-b3 | Member B3 | `member-b3-` | `workspace-member-b3/` | Autonomous | Team Member |
```

## Shared Knowledge Base

```
shared/knowledge/
├── department/           # Department-level knowledge
│   ├── handbook.md      # Employee handbook
│   ├── processes.md     # Business processes
│   └── contacts.md      # Contact directory
├── team-a/              # Team A knowledge
├── team-b/              # Team B knowledge
└── projects/            # Project knowledge
```

## Audit Configuration

- **Frequency**: Weekly Sunday 9:00 auto audit + monthly manual review
- **Archive**: Keep last 8 quarters (2 years)
- **Report**: Send to department manager and IT admin
- **Compliance**: Enable all clauses in GOVERNANCE.md

## Governance Key Points

1. **Onboarding Process**: New employee → Create Agent → Register → Training
2. **Offboarding Process**: Backup data → Transfer tasks → Deregister Agent → Archive
3. **Conflict Arbitration**: Naming conflicts coordinated by Main, escalate to manager if necessary
4. **Violation Handling**: Graded handling, from warning to permission suspension

## Setup Steps

1. Department admin as Main Agent owner
2. Execute full setup per main document (strict mode)
3. Develop department-level GOVERNANCE.md supplementary clauses
4. Create Agent and train for each employee
5. Pilot run for 1 week then fully enable
