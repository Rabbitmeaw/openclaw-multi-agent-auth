# 合规检查清单

**用途**: 记录 Main Agent 的自动化审计执行情况
**执行方式**: 自动化（每周日 9:00，通过 Cron 任务 `main-审计维护`）
**人工审查**: 每季度一次（检查自动化执行效果）
**记录位置**: 本文件记录异常，正常执行记录见 `~/.openclaw/workspace/AUDIT_LOG.md`

---

## 检查项模板

每次检查时复制以下模板，填写日期和结果：

```markdown
## 检查记录: YYYY-MM-DD

**执行人**: Main Agent
**检查结果**: ✅ 通过 / ⚠️ 发现 N 个问题 / ❌ 需要处理

### 检查项
- [ ] 审计日志完整性检查
- [ ] 无前缀任务扫描
- [ ] 异常访问模式检查
- [ ] 日志归档（季度切换时）

### 发现问题
| 序号 | 问题描述 | 严重程度 | 处理措施 | 状态 |
|------|----------|----------|----------|------|
| 1 | - | - | - | - |

### 备注
-
```

---

## 检查方法

### 1. 审计日志完整性
```bash
# 检查日志文件是否存在且格式正确
cat ~/.openclaw/workspace/AUDIT_LOG.md

# 确认本季度有记录（如果是季度初，确认上季度已归档）
```

### 2. 无前缀任务扫描
```bash
# 列出所有任务
openclaw cron list

# 检查是否有任务名不以 "{label}-" 开头
# 有效前缀列表:
# main-, {label1}-, {label2}-, ...
```

### 3. 异常访问模式
```bash
# 检查审计日志中是否有:
# - 频繁的跨 Agent 访问
# - 未记录原因的访问
# - 同一 Agent 多次违规
```

### 4. 日志归档（季度切换时执行）
```bash
# 每季度第一天执行:
# 1. 创建季度归档目录结构（如不存在）
# mkdir -p ~/.openclaw/archive/YYYY-Qx/{audit-logs,cron-snapshots,agent-reports}
#
# 示例: ~/.openclaw/archive/2026-Q1/
#        ├── audit-logs/          # 审计日志归档
#        ├── cron-snapshots/      # Cron 任务快照
#        └── agent-reports/       # Agent 活动报告

# 2. 将本季度数据归档
# cp ~/.openclaw/workspace/AUDIT_LOG.md \
#    ~/.openclaw/archive/YYYY-Qx/audit-logs/
# cp ~/.openclaw/workspace/.cron_snapshot.json \
#    ~/.openclaw/archive/YYYY-Qx/cron-snapshots/

# 3. 在 AUDIT_LOG.md 中创建新区块，开始新季度记录
```

---

## 历史检查记录

### 检查记录: YYYY-MM-DD (初始化)

**执行人**: Main Agent
**检查结果**: ✅ 通过

### 检查项
- [x] 审计日志完整性检查
- [x] 无前缀任务扫描
- [x] 异常访问模式检查
- [ ] 日志归档（季度切换时）- 非季度切换，跳过

### 发现问题
无

### 备注
- 初始检查，审计日志系统刚刚建立
- 所有 Cron 任务已按规范重命名
- 未发现无前缀任务

---

## 季度归档记录

| 季度 | 归档目录 | 内容 | 状态 |
|------|----------|------|------|
| - | - | - | - |

