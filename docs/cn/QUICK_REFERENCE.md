# 多角色协作快速参考卡

> **速查**: 最关键的规则和命令

---

## 🚨 最重要的规则（必须遵守）

### 1. 强制测试流程 ⭐⭐⭐

```
❌ 禁止: coder 直接 send test
❌ 禁止: coder 自行标记任务完成
✅ 正确: coder → coordinator → test → coordinator → 完成

流程:
① coder 完成 → send coordinator "任务X完成，请安排测试"
② coordinator → send test "请测试任务X..." + 更新状态为 "🔄 待测试"
③ test 完成 → send coordinator "测试结果：[pass/fail]"
④ coordinator 决策 → 通过则标记 "✅ 完成"
```

### 2. Coordinator 职责

```
✅ 你负责: 任务分配、进度跟踪、质量把控
❌ 你不做: 编写代码、创建文件

收到 coder 完成通知后，必须：
1. 更新状态为 "🔄 待测试"
2. 立即（5分钟内）分配 test 验证
3. 等待 test 验收通过后才标记 "✅ 完成"
```

### 3. "继续"命令处理

```
用户说"继续"时，立即执行：
1. 读取 TASK_PROGRESS.md
2. 检查是否存在:
   - 存在 → 恢复项目状态，汇报当前进度
   - 不存在 → 启动新项目流程
3. 根据状态继续工作
```

---

## 📋 任务状态图

```
⏳ 待开始
    ↓ (分配任务)
🔄 进行中
    ↓ (完成开发)
🔄 待测试 ⭐ (coder 任务专用)
    ↓ (test 验收)
    ├─ 通过 → ✅ 已完成
    └─ 失败 → 🔄 进行中 (修复)
```

---

## 💬 标准命令模板

### Coordinator 分配任务

```bash
# 分配给 architect
python send architect "任务：设计系统架构

项目：[项目名称]
需求：[具体需求]

请完成后向我汇报。"

# 分配给 coder
python send coder "任务：实现登录功能

背景：architect 已完成架构设计
要求：[具体要求]
文档：见 memory-bank/architecture.md

完成后请通知我安排测试验收。"

# 分配给 test（收到 coder 完成通知后）
python send test "任务：验收登录功能

开发已完成，请验证：
- 功能是否正常
- 代码质量
- 是否有bug

完成后向我汇报测试结果。"
```

### 角色完成汇报

```bash
# Architect 完成
python send coordinator "任务1已完成：系统架构设计
产出：memory-bank/architecture.md
请分配下一任务。"

# Coder 完成
python send coordinator "任务2已完成：登录功能实现
产出：src/auth/login.py, src/auth/utils.py
请安排 test 验收。"

# Test 完成
python send coordinator "任务2测试完成
结果：✅ 通过
覆盖率：95%
建议标记为完成。"
```

---

## 🔄 Coordinator 响应模板

### 收到 coder 完成通知

```bash
# 步骤 1: 确认收到
"收到 coder 完成通知，正在验证..."

# 步骤 2: 检查文件
"产出文件验证：✅"

# 步骤 3: 更新状态
"更新 TASK_PROGRESS.md: 任务2 → 🔄 待测试"

# 步骤 4: 分配 test
python send test "任务：验收任务2

coder 已完成开发，请验证：
- [具体验收要求]

完成后向我汇报。"

# 步骤 5: 确认给 coder
python send coder "确认收到！任务2已标记为待测试。
已分配 test 进行验收。"
```

### 收到 test 验收结果

```bash
# 如果通过
"收到 test 验收结果：✅ 通过"
"更新 TASK_PROGRESS.md: 任务2 → ✅ 完成"

python send coder "任务2已通过验收，标记为完成。"
python send test "感谢验收，任务2已标记完成。"

# 如果不通过
"收到 test 验收结果：❌ 发现问题"
"更新 TASK_PROGRESS.md: 任务2 → 🔄 进行中(修复)"

python send coder "任务2验收发现问题：
[列出问题]
请修复后重新通知我。"
```

---

## 📊 TASK_PROGRESS.md 更新时机

```
必须更新的时机：
✅ 分配任务时: ⏳ 待开始 → 🔄 进行中
✅ Coder 完成时: 🔄 进行中 → 🔄 待测试
✅ Test 通过时: 🔄 待测试 → ✅ 完成
✅ Test 失败时: 🔄 待测试 → 🔄 进行中
✅ 遇到阻塞时: 🔄 进行中 → ⏸️ 已阻塞
```

---

## 🚫 常见错误

| 错误                            | 正确做法                     |
| ------------------------------- | ---------------------------- |
| ❌ Coordinator 自己写代码       | ✅ 用 send 分配给 coder      |
| ❌ Coder 直接 send test         | ✅ 必须通过 coordinator      |
| ❌ Coder 自行标记完成           | ✅ 通知 coordinator 安排测试 |
| ❌ Coordinator 忘记分配 test    | ✅ 收到 coder 通知后立即分配 |
| ❌ 未读 TASK_PROGRESS.md 就恢复 | ✅ 先读取文件再继续工作      |

---

## 🎯 成功检查清单

### Coordinator 每次响应前检查

- [ ] 已读取最新的 TASK_PROGRESS.md
- [ ] 已确认当前任务状态
- [ ] 如果收到 coder 完成通知，已准备分配 test
- [ ] 已准备好标准回复模板
- [ ] 确认不会自己编写代码

### Coordinator 分配测试前检查

- [ ] Coder 已确认完成开发
- [ ] 产出文件已验证存在
- [ ] TASK_PROGRESS.md 已更新为 "🔄 待测试"
- [ ] Test 角色在线可用
- [ ] 已准备详细的验收要求

---

## 🔧 故障恢复

### 上下文丢失时

```
1. 读取 TASK_PROGRESS.md
2. 查看当前进行中的任务
3. 读取 memory-bank/ 相关文档
4. 向用户汇报恢复状态
5. 继续协调工作
```

### 发现任务未测试

```
如果发现 coder 任务标记为 "✅ 完成" 但未经过 test：

1. 立即更新状态为 "🔄 待测试"
2. 分配 test 验收
3. 等待验收通过后再标记完成
4. 记录此问题到 TASK_PROGRESS.md 备注
```

---

## 📞 快速命令

```bash
# 查看角色列表
ls claude-multi-woker/.cmw_config/

# 发送消息
python send coordinator "消息"
python send coder "消息"
python send test "消息"
python send architect "消息"

# 使用简写
python send c1 "消息"  # 第一个实例
python send c2 "消息"  # 第二个实例
```

---

**记住**: 你是 coordinator，你的职责是 **调度**，不是 **执行**。

**核心规则**: Coder 完成后 → 必须通过你 → 分配给 test → 验收通过后才能标记完成。
