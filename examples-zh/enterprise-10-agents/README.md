# 企业 10 Agent 配置示例

本示例展示一个 10 人部门如何配置多 Agent 环境。

---

## 场景

- **组织**: 企业某部门（如市场部）
- **人数**: 10 人（1 经理 + 2 组长 + 7 成员）
- **Agent 角色**:
  - `main`: 部门管理员
  - `manager`: 部门经理助手
  - `lead1`, `lead2`: 组长助手
  - `member1-7`: 组员个人 Agent

---

## 目录结构

```
~/.openclaw/
├── workspace/                      # Main Agent（部门管理员）
│   ├── SOUL.md
│   ├── AUDIT_LOG.md
│   ├── AUDIT_MAINTENANCE_GUIDE.md
│   └── .cron_snapshot.json
│
├── agents/
│   ├── main/                       # 管理员元数据
│   ├── manager/                    # 经理元数据
│   ├── lead1/                      # 组长1元数据
│   ├── lead2/                      # 组长2元数据
│   └── member1-7/                  # 成员元数据
│
└── shared/                         # 部门共享区
    ├── AGENT_REGISTRY.md           # 注册表
    ├── AGENT_CONSTRAINTS.md        # 权限约束
    ├── GOVERNANCE.md               # 治理规范
    ├── AUDIT_BUFFER.md             # 审计缓冲
    ├── COMPLIANCE_CHECKLIST.md     # 合规检查
    ├── communication/              # 沟通空间
    │   ├── inbox/
    │   ├── outbox/
│   │   └── tasks/
    ├── skills/                     # 共享技能
    ├── knowledge/                  # 部门知识库
    └── assets/                     # 公共资源
```

---

## Agent 注册表

| Label | 名称 | 角色 | Cron 前缀 | 权限 |
|-------|------|------|-----------|------|
| main | Main Agent | 管理员 | `main-` | 全局管理 |
| manager | Manager Agent | 经理 | `manager-` | 自治 |
| lead1 | Lead 1 Agent | 组长1 | `lead1-` | 自治 |
| lead2 | Lead 2 Agent | 组长2 | `lead2-` | 自治 |
| m1 | Member 1 Agent | 成员1 | `m1-` | 自治 |
| m2 | Member 2 Agent | 成员2 | `m2-` | 自治 |
| ... | ... | ... | ... | ... |
| m7 | Member 7 Agent | 成员7 | `m7-` | 自治 |

---

## Cron 任务示例

### 经理 Agent 任务
```
manager-周报汇总      每周五 16:00
manager-月度报告      每月1日 9:00
manager-团队提醒      工作日 9:00
```

### 组长 Agent 任务
```
lead1-组内日报        每天 18:00
lead1-周会准备        每周一 9:00

lead2-组内日报        每天 18:00
lead2-项目跟进        每周三 14:00
```

### 成员 Agent 任务
```
m1-日报提交           每天 17:30
m1-任务提醒           工作日 10:00

m2-日报提交           每天 17:30
m2-学习提醒           每周二/四 19:00

# ... 其他成员类似
```

### Main Agent 任务
```
main-审计维护         每周日 9:00
main-合规检查         每月1日 10:00
```

---

## 权限矩阵

| 操作 | Manager | Lead | Member | Main |
|------|---------|------|--------|------|
| 创建自己的 Cron | ✅ | ✅ | ✅ | ✅ |
| 修改自己的 Cron | ✅ | ✅ | ✅ | ✅ |
| 查看所有 Cron | ✅ | ✅ | ✅ | ✅ |
| 修改他人 Cron | ❌ | ❌ | ❌ | ✅ (仲裁时) |
| 访问 shared/ | ✅ 只读 | ✅ 只读 | ✅ 只读 | ✅ 读写 |
| 访问 audit 缓冲 | ✅ 追加 | ✅ 追加 | ✅ 追加 | ✅ 读写 |
| 访问 audit 日志 | ❌ | ❌ | ❌ | ✅ |
| 修改注册表 | ❌ | ❌ | ❌ | ✅ |

---

## 治理规范要点

### 数据分类

**私有数据**（严格保护）:
- 个人工作区 `agents/{label}/`
- 个人笔记、草稿、配置

**共享数据**（部门资产）:
- `shared/knowledge/` - 部门知识库
- `shared/skills/` - 共享技能
- `shared/communication/` - 沟通记录

### 命名规范

```
格式: {label}-{业务}-{描述}

示例:
  manager-周报-汇总
  lead1-组内-日报收集
  m3-个人-任务提醒
```

### 审计要求

- **所有 Agent** 必须在创建/修改/删除 Cron 时上报审计
- **Main Agent** 每周自动执行审计维护
- **季度审查** 人工检查审计执行效果

---

## 设置步骤

```bash
# 1. 创建所有 agents 目录
mkdir -p ~/.openclaw/agents/{main,manager,lead1,lead2,m1,m2,m3,m4,m5,m6,m7}

# 2. 创建共享区
mkdir -p ~/.openclaw/shared/communication/{inbox,outbox,tasks}
mkdir -p ~/.openclaw/shared/{skills,knowledge,assets}
mkdir -p ~/.openclaw/archive

# 3. 复制模板文件
cp templates-zh/AGENT_REGISTRY.md ~/.openclaw/shared/
cp templates-zh/AGENT_CONSTRAINTS.md ~/.openclaw/shared/
cp templates-zh/GOVERNANCE.md ~/.openclaw/shared/
cp templates-zh/AUDIT_BUFFER.md ~/.openclaw/shared/
cp templates-zh/COMPLIANCE_CHECKLIST.md ~/.openclaw/shared/
cp templates-zh/AUDIT_LOG.md ~/.openclaw/workspace/
cp templates-zh/AUDIT_MAINTENANCE_GUIDE.md ~/.openclaw/workspace/

# 4. 编辑注册表，配置所有 Agent
vim ~/.openclaw/shared/AGENT_REGISTRY.md

# 5. 初始化审计系统
openclaw cron list > ~/.openclaw/workspace/.cron_snapshot.json

# 6. 重启 Gateway
openclaw gateway restart
```

---

## 冲突处理示例

### 场景: 命名冲突

```
m1 想创建 "daily-report" 任务，但发现没有前缀
```

**处理流程**:
1. m1 停止操作，联系 Main
2. Main 检查发现该任务无前缀，违规
3. Main 联系 m1，要求改为 `m1-daily-report`
4. m1 删除旧任务，创建新任务
5. Main 记录审计日志

### 场景: 误删他人任务

```
m2 不小心删除了 m3 的任务
```

**处理流程**:
1. m2 立即停止操作，通知 Main 和 m3
2. Main 从备份或历史中恢复任务
3. Main 调查原因（是否为恶意行为）
4. 记录审计日志
5. 根据违规级别处理（警告/暂停权限等）

---

## 合规检查清单

- [ ] 所有 Agent 已注册
- [ ] 所有 Cron 任务带正确前缀
- [ ] 审计缓冲文件可写
- [ ] 审计日志文件由 Main 管理
- [ ] 共享区目录权限正确
- [ ] 维护任务已配置（每周日 9:00）
- [ ] 归档目录已创建
- [ ] 治理规范已传达给所有成员

---

## 注意事项

1. **严格命名**: 所有 Cron 任务必须带 `{label}-` 前缀
2. **及时审计**: 操作后立即上报审计缓冲
3. **数据隐私**: 不得访问他人 workspace
4. **定期维护**: Main 每周执行审计维护
5. **季度审查**: 人工检查自动化执行效果
6. **冲突上报**: 发现问题立即联系 Main
