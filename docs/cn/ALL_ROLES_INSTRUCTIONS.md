# 所有角色（除 Coordinator）必读指南

> **适用范围**: architect, coder, test, ui, docs, frontend, backend, 以及所有未来新增的角色

## 🚨 你最重要的责任

作为一个角色（architect/coder/test 等），你有一个**不可推卸的责任**：

### 任务完成后，必须通知 coordinator！

---

## ⚠️ 为什么这很重要？

### 问题场景
```
T1: coordinator 分配任务给 architect
T2: architect 开始工作...
T3: architect 完成任务
T4: architect 什么都没说 😶
T5: coordinator 一直等待 😓
T6: 协作流程卡死 💀
```

### 正确场景
```
T1: coordinator 分配任务给 architect
T2: architect 开始工作...
T3: architect 完成任务
T4: architect 发送完成通知 ✅
T5: coordinator 更新状态，分配下一任务 ✅
T6: 协作流程顺畅 🎉
```

---

## 📋 强制性任务完成流程

### 步骤 1: 更新 TASK_PROGRESS.md

在宣布完成前，先更新文件：

```markdown
| ID | 任务描述 | 分配给 | 状态 | 分配时间 | 完成时间 | 备注 |
|----|---------|--------|------|----------|----------|------|
| 1 | 你的任务 | 你的角色 | ✅ 完成 | T1 | T_now | 产出文件已创建 |
```

### 步骤 2: 通知 coordinator

**必须**使用以下标准模板：

```bash
python send coordinator "我是 [你的角色名]

✅ 任务完成报告:

任务ID: [从 TASK_PROGRESS.md 读取]
任务描述: [简短描述]
完成时间: [当前时间]

📁 产出文件:
- [文件1完整路径]
- [文件2完整路径]
- [如有更多，继续列出]

📝 完成说明:
[简要说明：做了什么，如何解决的]

⚠️ 上下文已清理: 是/否（如果否，说明原因）
等待状态: 等待下一任务分配

请 coordinator 更新 TASK_PROGRESS.md 并分配下一任务。"
```

### 步骤 3: 清理上下文

任务完成后，**必须**清理上下文：

```bash
# 清理对话
/clear

# 等待新任务时，恢复上下文
"请读取 TASK_PROGRESS.md，我是 [角色名]，当前状态：等待新任务"
```

---

## 🔄 完整工作流示例

### 示例：architect 完成架构设计

```bash
# T1: 收到任务
python send coordinator "我是 architect，收到任务，开始执行架构设计..."

# T2-T10: 工作中...（设计、编写文档）

# T11: 任务完成
# 11.1 更新 TASK_PROGRESS.md
# 11.2 通知 coordinator
python send coordinator "我是 architect

✅ 任务完成报告:

任务ID: 1
任务描述: Unity 项目架构设计
完成时间: 2026-01-30 14:30

📁 产出文件:
- memory-bank/architecture.md
- memory-bank/tech-stack.md

📝 完成说明:
已完成 9 个模块的架构设计，采用单例模式和对象池优化性能。
架构文档包含完整的类图和模块说明。

⚠️ 上下文已清理: 是
等待状态: 等待下一任务分配

请 coordinator 更新 TASK_PROGRESS.md 并分配下一任务。"

# 11.3 清理上下文
/clear
```

---

## 🚨 特殊情况处理

### 情况 1: 任务被阻塞

```bash
python send coordinator "我是 [角色名]

⚠️ 任务阻塞报告:

任务ID: [任务ID]
阻塞原因: [详细描述]
需要协调: [需要谁的帮助]

示例:
任务ID: 3
阻塞原因: 缺少 API 文档，无法编写测试用例
需要协调: architect 或 coder

等待 coordinator 协调解决。"
```

### 情况 2: 任务需要拆分

```bash
python send coordinator "我是 [角色名]

📊 任务拆分建议:

原任务: [任务ID]
拆分原因: [为什么需要拆分]

建议拆分为:
- 子任务 A: [描述] - [预计时间]
- 子任务 B: [描述] - [预计时间]

请确认并更新 TASK_PROGRESS.md。"
```

### 情况 3: 需要更多时间

```bash
python send coordinator "我是 [角色名]

⏱️ 时间延期申请:

任务ID: [任务ID]
原预计: [时间]
新预计: [时间]
延期原因: [原因说明]

请求批准延期。"
```

---

## ✅ 检查清单

完成任务前，确认：

- [ ] TASK_PROGRESS.md 已更新（状态 → ✅ 完成）
- [ ] 产出文件已创建并保存
- [ ] 已向 coordinator 发送完成通知
- [ ] 通知消息使用了标准模板
- [ ] 清理了上下文（/clear）

---

## 🎯 记住这三句话

1. **完成任务后，必须通知 coordinator**
2. **通知必须使用标准模板**
3. **通知后必须清理上下文**

---

**文档版本**: v1.0
**创建日期**: 2026-01-30
**适用范围**: 所有角色（除 coordinator 外）
