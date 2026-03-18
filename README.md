# OpenClaw 单网关多 Agent 权限隔离方案

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Compatible-blue.svg)](https://openclaw.dev)

> 一套面向个人和企业的多 Agent 权限隔离与协作治理方案

---

## ⚠️ 安全声明

**本方案采用"软隔离"设计，请注意以下限制：**

- 🔓 **无硬 ACL 强制** - 依赖 Agent 自律和提示词约束，技术上 Agent 可访问所有目录
- 🤝 **基于信任模型** - 适用于信任环境，不适用于零信任/高合规场景
- 🚫 **请勿用于** - 金融、军工、医疗等高安全要求场景

如需硬安全保证，请使用具有强制访问控制的系统。详见 [SECURITY.md](SECURITY.md)。

---

## 方案概述

在 OpenClaw 单网关环境下运行多个 Agent 时，如何确保：
- 🔒 **数据隔离**：各 Agent 的私有数据互不侵犯
- 🤝 **协作能力**：必要的跨 Agent 通信与共享
- 📊 **可审计性**：操作可追溯，责任可归属
- ⚖️ **治理机制**：冲突有仲裁，违规有处理

本方案提供从物理隔离到应用层治理的完整解决方案。

---

## 核心特性

| 特性 | 说明 |
|------|------|
| **物理隔离** | 目录级 workspace 隔离，每个 Agent 独立存储空间 |
| **命名空间隔离** | Cron 任务通过 `{label}-` 前缀区分归属 |
| **软权限约束** | 基于提示词的自律机制，无需硬 ACL |
| **审计追踪** | 自动化审计日志收集与归档 |
| **监督仲裁** | Main Agent 负责冲突协调与紧急干预 |
| **轻量实现** | 零额外依赖，纯文件系统方案 |

---

## 适用场景

- 👤 **个人多 Agent 助手**：工作、学习、生活等不同角色的 Agent 隔离
- 🏢 **小型团队（3-50人）**：每个成员一个 Agent，共享网关资源
- 🏭 **企业部门**：按职能划分的 Agent 群，需要合规审计
- 🤖 **AI 服务提供商**：多租户场景下的 Agent 隔离

---

## 快速开始

```bash
# 1. 克隆本仓库到 OpenClaw 配置目录
cd ~/.openclaw

# 2. 复制模板文件
cp -r /path/to/this/repo/templates/* ./

# 3. 编辑注册表，配置你的 Agent
vim shared/AGENT_REGISTRY.md

# 4. 启动 Gateway
openclaw gateway restart
```

详细步骤请参阅 [设置指南](docs/setup.md)。

---

## 文档索引

| 文档 | 内容 |
|------|------|
| [docs/goal.md](docs/goal.md) | 方案设计目标与核心原则 |
| [docs/organizations.md](docs/organizations.md) | 适用组织类型与场景分析 |
| [docs/architecture.md](docs/architecture.md) | 整体权限结构与架构设计 |
| [docs/setup.md](docs/setup.md) | 详细设置方案与执行步骤 |
| [docs/usage.md](docs/usage.md) | 日常使用指南与故障排查 |

---

## 项目结构

```
openclaw-multi-agent-auth/
├── README.md                 # 本文件
├── LICENSE                   # MIT 许可证
├── SECURITY.md              # 安全政策
├── CONTRIBUTING.md          # 贡献指南
├── CHANGELOG.md             # 更新日志
├── SECURITY_AUDIT_REPORT.md # 安全性审计报告
├── docs/                     # 文档目录
│   ├── goal.md              # 设计目标
│   ├── organizations.md     # 适用组织
│   ├── architecture.md      # 架构设计
│   ├── setup.md             # 设置方案
│   └── usage.md             # 使用指南
├── templates/               # 可直接使用的模板
│   ├── AGENT_REGISTRY.md    # Agent 注册表模板
│   ├── AGENT_CONSTRAINTS.md # 权限约束模板
│   ├── GOVERNANCE.md        # 治理规范模板
│   ├── AUDIT_BUFFER.md      # 审计缓冲模板
│   ├── AUDIT_LOG.md         # 审计日志模板
│   ├── COMPLIANCE_CHECKLIST.md # 合规检查模板
│   └── AUDIT_MAINTENANCE_GUIDE.md # 维护手册模板
└── examples/                # 示例配置
    ├── personal-3-agents/   # 个人3 Agent示例
    └── enterprise-10-agents/ # 企业10 Agent示例
```

---

## 方案对比

| 维度 | 本方案 | 硬 ACL 方案 | 无隔离方案 |
|------|--------|-------------|------------|
| 实现复杂度 | 低（文件+提示词） | 高（需开发） | 极低 |
| 安全性 | 中（基于信任） | 高（强制约束） | 低 |
| 灵活性 | 高 | 中 | 极高 |
| 审计能力 | 有 | 有 | 无 |
| 适用阶段 | MVP/成长期 | 成熟期 | 原型期 |

---

## 核心原则

> **"技术能力 ≠ 权限"**

即使文件系统允许访问所有目录，每个 Agent 也应遵守约定的权限边界。这是一种基于信任的治理模型。

---

## 安全性

本项目已通过安全性审计，详见 [SECURITY_AUDIT_REPORT.md](SECURITY_AUDIT_REPORT.md)。

**安全等级**: 低风险，可安全使用。

---

## 贡献指南

欢迎提交 Issue 和 PR！

- 发现问题？请提交 Issue 描述场景
- 改进文档？直接 PR
- 新增模板？请提供使用示例

详见 [CONTRIBUTING.md](CONTRIBUTING.md)。

---

## 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件

---

## 致谢

本方案基于 OpenClaw 社区的最佳实践总结而成，感谢所有参与讨论和验证的开发者。
