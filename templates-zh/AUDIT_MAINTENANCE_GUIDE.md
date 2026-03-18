# 审计维护操作手册

**任务名称**: main-审计维护
**执行频率**: 每周日 9:00
**触发方式**: Cron 定时任务

---

## 维护目标

1. 收集各 Agent 的审计日志
2. 检查 Cron 任务变更
3. 验证权限合规性
4. 生成维护报告

---

## 执行步骤

### 步骤 1: 收集审计缓冲

读取各 Agent 写入的审计记录：

```bash
# 检查是否存在 AUDIT_BUFFER.md
if [ -f ~/.openclaw/shared/AUDIT_BUFFER.md ]; then
    cat ~/.openclaw/shared/AUDIT_BUFFER.md
else
    echo "暂无新的审计记录"
fi
```

**收集内容**:
- 各 Agent 报告的 Cron 操作
- 异常操作记录
- 命名空间冲突记录

### 步骤 2: 汇总到审计日志

将收集到的信息追加到主审计日志：

```
~/.openclaw/workspace/AUDIT_LOG.md
```

**记录格式**:
```markdown
## 审计记录 - 2026-03-18

### 时间范围
上周日至本周六

### 收集的审计项
- [条目1]
- [条目2]

### Cron 任务变更
- 新增: [任务名]
- 修改: [任务名]
- 删除: [任务名]

### 异常事项
- [异常描述]

### 处理措施
- [采取的措施]
```

### 步骤 3: 检查 Cron 任务变更

1. **获取当前任务列表**:
   ```bash
   openclaw cron list
   ```

2. **对比上周快照**:
   - 读取 `~/.openclaw/workspace/.cron_snapshot.json`
   - 对比差异：新增、修改、删除的任务

3. **检查命名规范**:
   - 验证所有任务是否带有正确的前缀
   - 违规格式：`无前缀-任务名`、`错误前缀-任务名`

4. **保存本周快照**:
   ```bash
   openclaw cron list > ~/.openclaw/workspace/.cron_snapshot.json
   ```

### 步骤 4: 检查季度归档

1. **检查归档目录**:
   ```
   ~/.openclaw/archive/
   └── 2026-Q1/          # 当前季度
       ├── audit-logs/
       ├── cron-snapshots/
       └── agent-reports/
   ```

2. **归档条件判断**:
   - 如果是季度最后一周，执行归档
   - 将本季度审计日志移动到归档目录

3. **创建新季度目录**（如果是新季度第一周）:
   ```
   ~/.openclaw/archive/2026-Q2/
   ```

### 步骤 5: 生成并发送报告

**报告内容**:
```
🔍 审计维护报告 - [日期]

📊 Cron 任务统计:
- 总任务数: [数量]
- 本周新增: [数量]
- 本周修改: [数量]
- 命名规范违规: [数量]

📋 审计事项:
- 收集记录: [数量] 条
- 待处理: [数量] 条
- 已处理: [数量] 条

✅ 维护状态: 正常 / 需关注

📁 季度归档: [进度]
```

**发送方式**:
使用 `message` 工具发送到指定渠道

---

## 快速检查清单

- [ ] AUDIT_BUFFER.md 已读取
- [ ] AUDIT_LOG.md 已更新
- [ ] Cron 列表已对比
- [ ] 命名规范已检查
- [ ] 季度归档已检查
- [ ] 报告已生成并发送

---

## 异常处理

| 异常情况 | 处理措施 |
|----------|----------|
| AUDIT_BUFFER.md 不存在 | 记录"暂无新审计记录"，继续执行 |
| 发现命名规范违规 | 记录违规任务，通知相关 Agent |
| 发现权限争议 | 记录详情，准备仲裁 |
| 归档目录权限错误 | 检查文件权限，必要时手动修复 |

---

## 相关文件

| 文件 | 路径 | 说明 |
|------|------|------|
| 审计缓冲 | `~/.openclaw/shared/AUDIT_BUFFER.md` | 各 Agent 写入的审计记录 |
| 审计日志 | `~/.openclaw/workspace/AUDIT_LOG.md` | 汇总的主审计日志 |
| 上周快照 | `~/.openclaw/workspace/.cron_snapshot.json` | Cron 任务上周状态 |
| 本指南 | `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` | 本文件 |

---

**最后更新**: 2026-03-18
**版本**: 1.0
