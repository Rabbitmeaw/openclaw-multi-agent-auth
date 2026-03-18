# Personal 3-Agent Configuration Example

## Scenario Description

Personal user using 3 Agents for different domains:
- **Main**: Coordinator with global view
- **Work**: Work assistant handling career-related tasks
- **Life**: Life assistant handling daily reminders and plans

## Directory Structure

```
~/.openclaw/
├── workspace/                  # Main Agent
├── workspace-work/             # Work Agent
├── workspace-life/             # Life Agent
└── shared/                     # Shared area
```

## Agent Registry Configuration

```markdown
| Label | Name | Cron Prefix | Workspace Path | Permissions |
|-------|------|-------------|----------------|-------------|
| main | Main Agent | `main-` | `workspace/` | Global Management |
| work | Work Agent | `work-` | `workspace-work/` | Autonomous |
| life | Life Agent | `life-` | `workspace-life/` | Autonomous |
```

## Typical Cron Tasks

### Work Agent
- `work-daily-report` - Daily 9:00 reminder to write daily report
- `work-weekly-report` - Every Friday 17:00 generate weekly report draft
- `work-meeting-reminder` - Remind based on calendar

### Life Agent
- `life-water-reminder` - Every 2 hours remind to drink water
- `life-exercise-plan` - Every Mon/Wed/Fri 18:00 remind to exercise
- `life-bill-reminder` - Monthly 1st remind to check bills

## Audit Configuration

- **Frequency**: Monthly check (can reduce frequency for personal use)
- **Archive**: Keep last 2 quarters
- **Report**: Send to your Feishu/email

## Setup Steps

1. Follow base setup in main document [setup.md](../../docs/setup.md)
2. Modify `AGENT_REGISTRY.md` using above label configuration
3. Create workspaces and SOUL.md for work and life respectively
4. Create audit maintenance task
5. Test Cron creation functionality for each Agent
