# OpenClaw Multi-Agent Auth / OpenClaw 单网关多 Agent 权限治理方案

<div align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Compatible-blue.svg)](https://openclaw.dev)

**English** | [中文](#中文文档)

</div>

---

## English

> A permission isolation and collaborative governance solution for multi-Agent environments on OpenClaw single gateway

### ⚠️ Security Notice

**This solution adopts a "soft isolation" design. Please be aware of the following limitations:**

- 🔓 **No Hard ACL Enforcement** - Relies on Agent self-discipline and prompt constraints; technically Agents can access all directories
- 🤝 **Trust-Based Model** - Suitable for trusted environments, NOT for zero-trust/high-compliance scenarios
- 🚫 **DO NOT USE for** - Financial, military, medical, or other high-security requirement scenarios

For hard security guarantees, please use systems with mandatory access controls. See [SECURITY.md](SECURITY.md) for details.

### Overview

When running multiple Agents in an OpenClaw single-gateway environment, how do you ensure:
- 🔒 **Data Isolation**: Each Agent's private data remains inaccessible to others
- 🤝 **Collaboration Capability**: Necessary cross-Agent communication and sharing
- 📊 **Auditability**: Operations are traceable and accountability is assignable
- ⚖️ **Governance Mechanism**: Conflicts have arbitration and violations have handling

This solution provides a complete governance framework from physical isolation to application-layer governance.

### Quick Start

```bash
# 1. Clone this repository
cd ~/.openclaw

# 2. Copy template files
cp -r /path/to/this/repo/templates/* ./

# 3. Edit registry to configure your Agents
vim shared/AGENT_REGISTRY.md

# 4. Restart Gateway
openclaw gateway restart
```

### Documentation

| Document | Description |
|----------|-------------|
| [docs/goal.md](docs/goal.md) | Design goals and core principles |
| [docs/organizations.md](docs/organizations.md) | Applicable organization types |
| [docs/architecture.md](docs/architecture.md) | Architecture design |
| [docs/setup.md](docs/setup.md) | Setup instructions |
| [docs/usage.md](docs/usage.md) | Usage guide |

---

## 中文文档

> 一套面向个人和企业的 OpenClaw 单网关多 Agent 权限隔离与协作治理方案

### ⚠️ 安全声明

**本方案采用"软隔离"设计，请注意以下限制：**

- 🔓 **无硬 ACL 强制** - 依赖 Agent 自律和提示词约束，技术上 Agent 可访问所有目录
- 🤝 **基于信任模型** - 适用于信任环境，不适用于零信任/高合规场景
- 🚫 **请勿用于** - 金融、军工、医疗等高安全要求场景

如需硬安全保证，请使用具有强制访问控制的系统。详见 [SECURITY.md](SECURITY.md)。

### 方案概述

在 OpenClaw 单网关环境下运行多个 Agent 时，如何确保：
- 🔒 **数据隔离**：各 Agent 的私有数据互不侵犯
- 🤝 **协作能力**：必要的跨 Agent 通信与共享
- 📊 **可审计性**：操作可追溯，责任可归属
- ⚖️ **治理机制**：冲突有仲裁，违规有处理

本方案提供从物理隔离到应用层治理的完整解决方案。

### 快速开始

```bash
# 1. 克隆本仓库到 OpenClaw 配置目录
cd ~/.openclaw

# 2. 复制模板文件（中文版本使用 templates-zh/）
cp -r /path/to/this/repo/templates-zh/* ./

# 3. 编辑注册表，配置你的 Agent
vim shared/AGENT_REGISTRY.md

# 4. 启动 Gateway
openclaw gateway restart
```

### 中文文档索引

| 文档 | 内容 |
|------|------|
| [docs-zh/goal.md](docs-zh/goal.md) | 设计目标与核心原则 |
| [docs-zh/organizations.md](docs-zh/organizations.md) | 适用组织类型与场景分析 |
| [docs-zh/architecture.md](docs-zh/architecture.md) | 整体权限结构与架构设计 |
| [docs-zh/setup.md](docs-zh/setup.md) | 详细设置方案与执行步骤 |
| [docs-zh/usage.md](docs-zh/usage.md) | 日常使用指南与故障排查 |

---

## Project Structure / 项目结构

```
openclaw-multi-agent-auth/
├── README.md                    # This file / 本文件
├── LICENSE                      # MIT License
├── SECURITY.md                  # Security policy / 安全政策
├── CONTRIBUTING.md              # Contribution guidelines / 贡献指南
├── CHANGELOG.md                 # Changelog / 更新日志
├── SECURITY_AUDIT_REPORT.md     # Audit report / 安全性审计报告
├── docs/                        # English Documentation / 英文文档
│   ├── goal.md
│   ├── organizations.md
│   ├── architecture.md
│   ├── setup.md
│   └── usage.md
├── docs-zh/                     # 中文文档
│   ├── goal.md                 # 设计目标
│   ├── organizations.md        # 适用组织
│   ├── architecture.md         # 架构设计
│   ├── setup.md                # 设置方案
│   └── usage.md                # 使用指南
├── templates/                   # English Templates / 英文模板
│   ├── AGENT_REGISTRY.md
│   ├── AGENT_CONSTRAINTS.md
│   ├── GOVERNANCE.md
│   ├── AUDIT_BUFFER.md
│   ├── AUDIT_LOG.md
│   ├── COMPLIANCE_CHECKLIST.md
│   ├── AUDIT_MAINTENANCE_GUIDE.md
│   └── SOUL_WORKER.md
├── templates-zh/                # 中文模板
│   ├── AGENT_REGISTRY.md       # Agent 注册表模板
│   ├── AGENT_CONSTRAINTS.md    # 权限约束模板
│   ├── GOVERNANCE.md           # 治理规范模板
│   ├── AUDIT_BUFFER.md         # 审计缓冲模板
│   ├── AUDIT_LOG.md            # 审计日志模板
│   ├── COMPLIANCE_CHECKLIST.md # 合规检查模板
│   ├── AUDIT_MAINTENANCE_GUIDE.md # 维护手册模板
│   └── SOUL_WORKER.md          # Worker SOUL 模板
├── examples/                    # English Examples / 英文示例
│   ├── personal-3-agents/
│   └── enterprise-10-agents/
└── examples-zh/                 # 中文示例
    ├── personal-3-agents/      # 个人3 Agent示例
    └── enterprise-10-agents/   # 企业10 Agent示例
```

---

## Core Features / 核心特性

| Feature / 特性 | Description / 说明 |
|----------------|-------------------|
| **Physical Isolation / 物理隔离** | Directory-level workspace isolation / 目录级 workspace 隔离 |
| **Namespace Isolation / 命名空间隔离** | Cron tasks via `{label}-` prefix / Cron 任务通过前缀区分 |
| **Soft Constraints / 软权限约束** | Prompt-based self-discipline / 基于提示词的自律机制 |
| **Audit Trail / 审计追踪** | Automated log collection / 自动化审计日志收集 |
| **Governance / 监督仲裁** | Main Agent arbitration / Main Agent 负责冲突协调 |

---

## Solution Comparison / 方案对比

| Dimension / 维度 | This Solution / 本方案 | Hard ACL / 硬 ACL | No Isolation / 无隔离 |
|------------------|------------------------|-------------------|----------------------|
| Complexity / 实现复杂度 | Low / 低 | High / 高 | Minimal / 极低 |
| Security / 安全性 | Medium (Trust) / 中 | High / 高 | Low / 低 |
| Flexibility / 灵活性 | High / 高 | Medium / 中 | Very High / 极高 |

---

## License / 许可证

MIT License - See [LICENSE](LICENSE) file / 详见 [LICENSE](LICENSE) 文件

---

## Contributing / 贡献

Issues and PRs are welcome! / 欢迎提交 Issue 和 PR！

See [CONTRIBUTING.md](CONTRIBUTING.md) for details. / 详见 [CONTRIBUTING.md](CONTRIBUTING.md)

---

## Acknowledgments / 致谢

This solution is based on best practices from the OpenClaw community. / 本方案基于 OpenClaw 社区的最佳实践总结而成。