# 个人 3 Agent 配置示例

本示例展示一位个人用户如何配置 3 个 Agent（Main + 2 Worker）。

---

## 场景

- **用户**: 一位需要平衡工作与学习的个人用户
- **Agent 角色**:
  - `main`: 协调者，拥有全局视图
  - `work`: 工作助手，处理职业相关任务
  - `study`: 学习助手，管理学习计划

---

## 目录结构

```
~/.openclaw/
├── workspace/                      # Main Agent
│   ├── SOUL.md
│   ├── AUDIT_LOG.md               # 审计日志
│   └── AUDIT_MAINTENANCE_GUIDE.md # 维护手册
│
├── workspace-work/                 # Work Agent
│   └── SOUL.md
│
├── workspace-study/                # Study Agent
│   └── SOUL.md
│
├── agents/
│   ├── main/
│   ├── work/
│   └── study/
│
└── shared/                         # 共享区
    ├── AGENT_REGISTRY.md
    ├── AGENT_CONSTRAINTS.md
    ├── GOVERNANCE.md
    ├── AUDIT_BUFFER.md
    ├── COMPLIANCE_CHECKLIST.md
    └── communication/
```

---

## Agent 注册表

| Label | 名称 | Cron 前缀 | 工作区 | 权限 |
|-------|------|-----------|--------|------|
| main | Main Agent | `main-` | `workspace/` | 全局管理 |
| work | Work Agent | `work-` | `workspace-work/` | 自治 |
| study | Study Agent | `study-` | `workspace-study/` | 自治 |

---

## Cron 任务示例

### Work Agent 任务
```
work-日报提醒      每天 9:00
work-周报生成      每周五 17:00
work-会议准备      工作日 8:30
```

### Study Agent 任务
```
study-英语学习     每天 7:00
study-复习提醒     每天 20:00
study-进度检查     每周日 21:00
```

### Main Agent 任务
```
main-审计维护      每周日 9:00
```

---

## 数据隔离示例

### Work Agent 的私有数据
- `workspace-work/projects/` - 项目文档
- `workspace-work/meetings/` - 会议记录
- `workspace-work/contacts/` - 工作联系人

### Study Agent 的私有数据
- `workspace-study/courses/` - 课程资料
- `workspace-study/notes/` - 学习笔记
- `workspace-study/goals/` - 学习目标

### 共享数据
- `shared/knowledge/` - 知识库（工作方法、学习技巧）
- `shared/skills/` - 共享技能
- `shared/communication/` - 沟通空间

---

## 设置步骤

```bash
# 1. 创建工作区
mkdir -p ~/.openclaw/workspace-work
mkdir -p ~/.openclaw/workspace-study

# 2. 复制 SOUL.md
cp templates-zh/SOUL_WORKER.md ~/.openclaw/workspace-work/SOUL.md
cp templates-zh/SOUL_WORKER.md ~/.openclaw/workspace-study/SOUL.md

# 3. 修改 SOUL.md 中的 {label} 为实际值

# 4. 配置注册表（编辑 shared/AGENT_REGISTRY.md）

# 5. 重启 Gateway
openclaw gateway restart
```

---

## 使用示例

### Work Agent 创建任务
```javascript
// Work Agent 执行
cron.add({
  name: "work-日报提醒",
  schedule: { expr: "0 9 * * *" }
});

// 记录审计
appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
  "[2026-03-18 09:00] [work] [CRON_CREATE] 任务: work-日报提醒\n");
```

### Study Agent 访问共享知识
```javascript
// Study Agent 可以读取共享知识
const tips = read("~/.openclaw/shared/knowledge/学习技巧.md");
```

### Main Agent 监督
```javascript
// Main Agent 查看所有任务
const allJobs = cron.list();
// 检查命名规范
const violations = allJobs.filter(j => !j.name.match(/^(main|work|study)-/));
```

---

## 注意事项

1. **命名规范**: Work Agent 的任务必须以 `work-` 开头
2. **审计上报**: 创建/修改/删除 Cron 时必须记录审计
3. **数据边界**: Work Agent 不应访问 `workspace-study/`
4. **冲突处理**: 命名冲突时联系 Main 协调
