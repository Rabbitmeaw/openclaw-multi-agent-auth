# 企业 10 Agent 配置示例

## 场景描述

企业部门使用，约 10 名员工，需要完整的审计和治理。

## 组织架构

```
部门
├── 经理 (1人)
├── 组长 A (1人) - 带 3 人小组
├── 组长 B (1人) - 带 3 人小组
└── 独立成员 (1人)
```

## Agent 注册表配置

```markdown
| Label | 名称 | Cron 前缀 | 工作区路径 | 权限 | 角色 |
|-------|------|-----------|------------|------|------|
| main | Main Agent | `main-` | `workspace/` | 全局管理 | 部门管理员 |
| manager | Manager Agent | `manager-` | `workspace-manager/` | 自治 | 部门经理 |
| lead-a | Lead A | `lead-a-` | `workspace-lead-a/` | 自治 | 组长A |
| member-a1 | Member A1 | `member-a1-` | `workspace-member-a1/` | 自治 | 组员 |
| member-a2 | Member A2 | `member-a2-` | `workspace-member-a2/` | 自治 | 组员 |
| member-a3 | Member A3 | `member-a3-` | `workspace-member-a3/` | 自治 | 组员 |
| lead-b | Lead B | `lead-b-` | `workspace-lead-b/` | 自治 | 组长B |
| member-b1 | Member B1 | `member-b1-` | `workspace-member-b1/` | 自治 | 组员 |
| member-b2 | Member B2 | `member-b2-` | `workspace-member-b2/` | 自治 | 组员 |
| member-b3 | Member B3 | `member-b3-` | `workspace-member-b3/` | 自治 | 组员 |
```

## 共享知识库

```
shared/knowledge/
├── department/           # 部门级知识
│   ├── handbook.md      # 员工手册
│   ├── processes.md     # 业务流程
│   └── contacts.md      # 通讯录
├── team-a/              # A组知识
├── team-b/              # B组知识
└── projects/            # 项目知识
```

## 审计配置

- **频率**: 每周日 9:00 自动审计 + 每月人工审查
- **归档**: 保留最近 8 季度（2年）
- **报告**: 发送给部门经理和 IT 管理员
- **合规**: 启用 GOVERNANCE.md 全部条款

## 治理要点

1. **入职流程**: 新员工 → 创建 Agent → 注册 → 培训
2. **离职流程**: 备份数据 → 转移任务 → 注销 Agent → 归档
3. **冲突仲裁**: 命名冲突由 Main 协调，必要时上报经理
4. **违规处理**: 分级处理，从警告到暂停权限

## 设置步骤

1. 部门管理员作为 Main Agent 所有者
2. 按照主文档执行完整设置（严格模式）
3. 制定部门级 GOVERNANCE.md 补充条款
4. 为每位员工创建 Agent 并培训
5. 试运行 1 周后全面启用
