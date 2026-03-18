# Applicable Organization Types

## 2.1 Personal Users

### Scenario Description
- 1 person using multiple Agents
- Each Agent represents a different role (work, study, life, etc.)
- Self is the "owner" of Main Agent

### Typical Configuration
```
Agents:
- main: Coordinator, has global view
- work: Work assistant, handles career-related tasks
- study: Study assistant, manages learning plans
- life: Life assistant, handles daily reminders
```

### Suitability Assessment
| Dimension | Rating | Description |
|-----------|--------|-------------|
| Data Isolation | ⭐⭐⭐⭐⭐ | Self-managed, high trust |
| Collaboration Needs | ⭐⭐⭐ | Mainly self-coordination |
| Audit Needs | ⭐⭐ | Personal use, low audit requirements |
| Governance Complexity | ⭐ | Self is administrator |

### Recommended Configuration
- **Isolation Level**: Standard (directory isolation + naming prefix)
- **Audit Frequency**: Monthly checks
- **Archive Strategy**: Keep last 2 quarters

---

## 2.2 Small Teams (3-20 people)

### Scenario Description
- Small team sharing OpenClaw gateway
- Each member has one Agent
- Needs shared knowledge base and collaboration space
- Lightweight governance is sufficient

### Typical Configuration
```
Agents:
- main: Team coordinator (admin)
- member1: Member 1 personal Agent
- member2: Member 2 personal Agent
- ...
- shared: Shared knowledge base
```

### Suitability Assessment
| Dimension | Rating | Description |
|-----------|--------|-------------|
| Data Isolation | ⭐⭐⭐⭐ | Member data needs isolation |
| Collaboration Needs | ⭐⭐⭐⭐⭐ | High-frequency collaboration |
| Audit Needs | ⭐⭐⭐ | Basic audit needed |
| Governance Complexity | ⭐⭐⭐ | Clear standards required |

### Recommended Configuration
- **Isolation Level**: Standard
- **Audit Frequency**: Weekly auto + monthly review
- **Archive Strategy**: Keep last 4 quarters
- **Required Documents**: GOVERNANCE.md, COMPLIANCE_CHECKLIST.md

---

## 2.3 Enterprise Departments (20-100 people)

### Scenario Description
- Enterprise department use, may have compliance requirements
- Needs complete audit and governance
- May need integration with existing IT systems
- Clear roles and permission levels

### Typical Configuration
```
Agents:
- main: Department admin
- manager: Department manager assistant
- team-lead1: Team lead 1 assistant
- team-lead2: Team lead 2 assistant
- member1-N: Team member personal Agents
- shared: Department knowledge base
```

### Suitability Assessment
| Dimension | Rating | Description |
|-----------|--------|-------------|
| Data Isolation | ⭐⭐⭐⭐⭐ | Enterprise data strictly isolated |
| Collaboration Needs | ⭐⭐⭐⭐⭐ | Complex collaboration workflows |
| Audit Needs | ⭐⭐⭐⭐⭐ | Compliance required |
| Governance Complexity | ⭐⭐⭐⭐⭐ | Complete governance system needed |

### Recommended Configuration
- **Isolation Level**: Strict
- **Audit Frequency**: Weekly auto + monthly review + quarterly report
- **Archive Strategy**: Keep last 8 quarters (2 years)
- **Required Documents**: All governance documents
- **Extension Recommendation**: Consider integrating with enterprise SIEM

---

## 2.4 AI Service Providers (Multi-tenant)

### Scenario Description
- Providing Agent services to external customers
- Each customer has one or more Agents
- Strict tenant isolation requirements
- Needs billing and usage statistics

### Suitability Assessment
| Dimension | Rating | Description |
|-----------|--------|-------------|
| Data Isolation | ⭐⭐⭐⭐⭐ | Absolute isolation between tenants |
| Collaboration Needs | ⭐⭐ | Tenants usually don't collaborate |
| Audit Needs | ⭐⭐⭐⭐⭐ | Operations required |
| Governance Complexity | ⭐⭐⭐⭐⭐ | Commercial-grade governance |

### Suitability: ⚠️ Use with Caution

**Limitations of this solution for this scenario**:
- Soft isolation cannot prevent malicious tenants
- Lacks billing and usage statistics features
- Requires additional multi-tenant management features

**Recommendations**:
- Only for trusted customers (e.g., different departments within enterprise)
- For external customers, recommend hard isolation solutions
- Requires development of additional management backend

---

## 2.5 Not Suitable Scenarios

The following scenarios are **NOT recommended** for this solution:

### High Security Compliance Industries
- Finance, healthcare, military, etc.
- Require SOC2, ISO27001, etc. certifications
- Regulatory requirements for hard permission controls

**Alternative Solutions**:
- Wait for OpenClaw official hard ACL support
- Develop permission middleware
- Use independent gateway instances

### Zero-Trust Environments
- Cannot assume Agents are trustworthy
- Require mandatory isolation, not relying on self-discipline

**Alternative Solutions**:
- OS-level sandboxing
- Containerized isolation
- Independent virtual machines

### Large-Scale Deployment (>100 Agents)
- This solution's design limit is approximately 100 Agents
- Management complexity grows linearly with Agent count

**Alternative Solutions**:
- Sharded deployment with multiple gateways
- Develop central management console
- Use enterprise multi-tenant solutions

---

## 2.6 Selection Guide

```
What type of user are you?
    │
    ├─ Personal User ──────→ Use Standard Config
    │
    ├─ Small Team (<20)────→ Use Standard Config + Full Audit
    │
    ├─ Enterprise Dept (<100)→ Use Strict Config + Full Governance
    │
    ├─ Multi-tenant Service ─→ ⚠️ Use with caution, requires extra dev
    │
    └─ High Compliance ────→ ❌ Not suitable, wait for hard ACL
```

---

## 2.7 Organization Type Quick Reference

| Organization Type | Agent Count | Isolation Level | Audit Frequency | Archive Retention | Recommendation |
|-------------------|-------------|-----------------|-----------------|-------------------|----------------|
| Personal | 2-5 | Standard | Monthly | 2 quarters | ⭐⭐⭐⭐⭐ |
| Small Team | 3-20 | Standard | Weekly auto + Monthly | 4 quarters | ⭐⭐⭐⭐⭐ |
| Enterprise Dept | 20-100 | Strict | Weekly auto + Monthly + Quarterly | 8 quarters | ⭐⭐⭐⭐ |
| Multi-tenant | Variable | Not suitable | - | - | ⭐⭐ |
| High Compliance | Variable | Not suitable | - | - | ❌ |
