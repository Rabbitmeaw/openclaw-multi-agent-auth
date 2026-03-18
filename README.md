# OpenClaw Multi-Agent Auth

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Compatible-blue.svg)](https://openclaw.dev)

> A permission isolation and collaborative governance solution for multi-Agent environments on OpenClaw single gateway

---

## ⚠️ Security Notice

**This solution adopts a "soft isolation" design. Please be aware of the following limitations:**

- 🔓 **No Hard ACL Enforcement** - Relies on Agent self-discipline and prompt constraints; technically Agents can access all directories
- 🤝 **Trust-Based Model** - Suitable for trusted environments, NOT for zero-trust/high-compliance scenarios
- 🚫 **DO NOT USE for** - Financial, military, medical, or other high-security requirement scenarios

For hard security guarantees, please use systems with mandatory access controls. See [SECURITY.md](SECURITY.md) for details.

---

## Overview

When running multiple Agents in an OpenClaw single-gateway environment, how do you ensure:
- 🔒 **Data Isolation**: Each Agent's private data remains inaccessible to others
- 🤝 **Collaboration Capability**: Necessary cross-Agent communication and sharing
- 📊 **Auditability**: Operations are traceable and accountability is assignable
- ⚖️ **Governance Mechanism**: Conflicts have arbitration and violations have handling

This solution provides a complete governance framework from physical isolation to application-layer governance.

---

## Core Features

| Feature | Description |
|---------|-------------|
| **Physical Isolation** | Directory-level workspace isolation with independent storage for each Agent |
| **Namespace Isolation** | Cron tasks distinguished via `{label}-` prefix |
| **Soft Permission Constraints** | Self-discipline mechanism based on prompts, no hard ACL required |
| **Audit Trail** | Automated audit log collection and archiving |
| **Supervision & Arbitration** | Main Agent responsible for conflict coordination and emergency intervention |
| **Lightweight Implementation** | Zero additional dependencies, pure filesystem solution |

---

## Use Cases

- 👤 **Personal Multi-Agent Assistants**: Isolation of Agents for different roles (work, study, life)
- 🏢 **Small Teams (3-50 people)**: Each member has one Agent, sharing gateway resources
- 🏭 **Enterprise Departments**: Agent groups divided by function requiring compliance auditing
- 🤖 **AI Service Providers**: Multi-tenant scenarios with Agent isolation

---

## Quick Start

```bash
# 1. Clone this repository to your OpenClaw config directory
cd ~/.openclaw

# 2. Copy template files
cp -r /path/to/this/repo/templates/* ./

# 3. Edit registry to configure your Agents
vim shared/AGENT_REGISTRY.md

# 4. Restart Gateway
openclaw gateway restart
```

See [Setup Guide](docs/setup.md) for detailed steps.

---

## Documentation Index

| Document | Content |
|----------|---------|
| [docs/goal.md](docs/goal.md) | Design goals and core principles |
| [docs/organizations.md](docs/organizations.md) | Applicable organization types and scenario analysis |
| [docs/architecture.md](docs/architecture.md) | Overall permission structure and architecture design |
| [docs/setup.md](docs/setup.md) | Detailed setup instructions |
| [docs/usage.md](docs/usage.md) | Daily usage guide and troubleshooting |

---

## Project Structure

```
openclaw-multi-agent-auth/
├── README.md                 # This file
├── LICENSE                   # MIT License
├── SECURITY.md              # Security policy
├── CONTRIBUTING.md          # Contribution guidelines
├── CHANGELOG.md             # Changelog
├── SECURITY_AUDIT_REPORT.md # Security audit report
├── docs/                     # Documentation
│   ├── goal.md              # Design goals
│   ├── organizations.md     # Applicable organizations
│   ├── architecture.md      # Architecture design
│   ├── setup.md             # Setup instructions
│   └── usage.md             # Usage guide
├── templates/               # Ready-to-use templates
│   ├── AGENT_REGISTRY.md    # Agent registry template
│   ├── AGENT_CONSTRAINTS.md # Permission constraints template
│   ├── GOVERNANCE.md        # Governance standards template
│   ├── AUDIT_BUFFER.md      # Audit buffer template
│   ├── AUDIT_LOG.md         # Audit log template
│   ├── COMPLIANCE_CHECKLIST.md # Compliance checklist template
│   └── AUDIT_MAINTENANCE_GUIDE.md # Maintenance guide template
└── examples/                # Example configurations
    ├── personal-3-agents/   # Personal 3-Agent example
    └── enterprise-10-agents/ # Enterprise 10-Agent example
```

---

## Solution Comparison

| Dimension | This Solution | Hard ACL Solution | No Isolation |
|-----------|---------------|-------------------|--------------|
| Implementation Complexity | Low (files + prompts) | High (requires development) | Minimal |
| Security | Medium (trust-based) | High (mandatory) | Low |
| Flexibility | High | Medium | Very High |
| Audit Capability | Yes | Yes | No |
| Suitable Phase | MVP/Growth | Mature | Prototype |

---

## Core Principles

> **"Technical Capability ≠ Permission"**

Even if the filesystem allows access to all directories, each Agent should respect the agreed permission boundaries. This is a trust-based governance model.

---

## Security

This project has passed a security audit. See [SECURITY_AUDIT_REPORT.md](SECURITY_AUDIT_REPORT.md) for details.

**Security Rating**: Low risk, safe to use.

---

## Contributing

Issues and PRs are welcome!

- Found a problem? Submit an Issue describing the scenario
- Improving documentation? Submit a PR directly
- Adding new templates? Please provide usage examples

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

## License

MIT License - See [LICENSE](LICENSE) file

---

## Acknowledgments

This solution is based on best practices from the OpenClaw community. Thanks to all developers who participated in discussions and validation.
