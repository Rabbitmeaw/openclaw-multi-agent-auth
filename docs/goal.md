# Design Goals

## 1.1 Background

In an OpenClaw single-gateway multi-Agent environment, the following challenges are faced:

### Data Isolation Challenges
- Multiple Agents share the same filesystem; how to prevent mutual data access?
- Personal memories, configurations, and other sensitive information need protection
- In enterprise scenarios, employee data requires privacy boundaries

### Collaboration Challenges
- Some scenarios require cross-Agent knowledge sharing
- A unified scheduling and coordination mechanism is needed
- How to arbitrate when conflicts occur?

### Governance Challenges
- How to trace operations?
- How to discover and handle violations?
- How to onboard new Agents?

---

## 1.2 Core Objectives

| Objective | Description | Implementation |
|-----------|-------------|----------------|
| **Physical Isolation** | Each Agent has independent filesystem space | Independent workspace directories |
| **Data Protection** | Private data of Agents not accessible by other Agents | Directory permissions + prompt constraints |
| **Collaboration Capability** | Support necessary cross-Agent communication and sharing | shared/ directory |
| **Cron Autonomy** | Each Agent independently manages scheduled tasks | Namespace prefix soft isolation |
| **Supervision & Arbitration** | Main Agent retains global supervision and conflict arbitration | Prompt constraints + registry |
| **Audit Traceability** | Critical operations can be recorded and traced | Audit logging system |

---

## 1.3 Solution Evolution

```
Centralized Scheduling (Old)         Distributed Autonomy (New)
┌─────────┐                         ┌─────────┐
│ Worker  │──Request──┐            │ Worker  │──Direct Create
└─────────┘           │            └─────────┘
                      ▼                 │
┌─────────┐    ┌──────────┐            │
│  Main   │◄───│  Inbox   │            │
└────┬────┘    └──────────┘            │
     │                                  ▼
     ▼                            ┌─────────┐
┌─────────┐                       │  Cron   │
│ Create  │                       │Scheduler│
│  Task   │                       └─────────┘
└─────────┘
```

**Evolution Rationale**:
- Centralized had single-point bottlenecks; Workers waited for Main responses
- Distributed improves response speed; Agents can self-govern
- Namespace isolation replaces hard permission restrictions

---

## 1.4 Design Principles

### Principle 1: Least Privilege
Each Agent can only access:
- Its own workspace
- Shared areas (read-only or restricted read-write)
- Its own Cron tasks (limited by prefix)

### Principle 2: Default Isolation
- By default, Agent data is invisible to each other
- Sharing requires explicit placement in shared/ directory
- Cross-Agent access requires authorization

### Principle 3: Audit Traceability
- Critical operations (Cron changes, cross-area access) must be recorded
- Audit logs are centrally managed and regularly archived
- Accountability is traceable when conflicts occur

### Principle 4: Lightweight Implementation
- No additional systems or databases introduced
- Pure filesystem solution
- Relies on prompt constraints rather than hard ACL

---

## 1.5 Core Assumptions

This solution is based on the following assumptions:

1. **Agents are trustworthy**: Agents will comply with prompt constraints and won't maliciously bypass
2. **Main Agent is reliable**: Main Agent correctly performs supervision and arbitration duties
3. **User understands limitations**: User understands this is "soft isolation" not "hard security"

**Not Suitable For**:
- Zero-trust environments (require hard ACL)
- Malicious Agent scenarios
- High-security compliance requirements (e.g., finance, military)

---

## 1.6 Success Criteria

Success metrics for the solution:

| Criteria | Measurement |
|----------|-------------|
| Effective Data Isolation | No accidental data leakage incidents |
| Smooth Collaboration | Cross-Agent task coordination time < 5 minutes |
| Complete Audit | Critical operation recording rate > 95% |
| Resolvable Conflicts | Average namespace conflict resolution time < 1 hour |
| Controllable Resources | Audit log annual increment < 10MB |
