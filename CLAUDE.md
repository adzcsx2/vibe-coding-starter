# Claude 项目配置

## 🔒 强制性开发规范

⚠️ **CRITICAL**: 在执行任何任务前，必须先阅读以下文件：

### 1. 必读文档（按优先级）

```
优先级 0: .claude/ALL_ROLES_INSTRUCTIONS.md - 所有角色（除coordinator）必读！
优先级 1: README.md         - Vibe Coding 核心原则和开发规范
优先级 2: MULTI_WORKER_RULES.md  - 多角色协作工作流程
优先级 3: .claude/COORDINATOR_INSTRUCTIONS.md  - Coordinator 角色指令
```

### 1.1 角色必读指南

#### 🔴 如果你是任何角色（architect/coder/test/ui/docs 等）

**必须阅读**: `.claude/ALL_ROLES_INSTRUCTIONS.md`

**最重要的规则**:
- 任务完成后**必须**通知 coordinator
- 必须使用标准完成消息模板
- 任务完成后**必须**清理上下文（`/clear`）

详见：`.claude/ALL_ROLES_INSTRUCTIONS.md`

#### 🟢 如果你是 coordinator 角色

**必须阅读**: `.claude/COORDINATOR_INSTRUCTIONS.md`

**最重要的指令**: 处理用户的"继续"命令
- 当用户说"继续"时，首先检查是否存在 `TASK_PROGRESS.md`
- 如果存在 → 读取并恢复项目状态
- 如果不存在 → 启动新项目流程

详见：`.claude/COORDINATOR_INSTRUCTIONS.md`

### 2. 核心原则（来自 README.md）

#### 🔑 Key Principle
**"Planning is everything. Do NOT let the AI plan autonomously, or your codebase will become an unmanageable mess."**

#### 📐 强制性规则

1. **模块化优先**
   - ✅ 使用多个文件，保持代码模块化
   - ❌ 禁止创建单体文件（monolith）
   - ✅ 每个文件职责单一，清晰明确

2. **Always Rules（始终遵循）**
   - 在编写任何代码前，先阅读 `memory-bank/@architecture.md`
   - 在编写任何代码前，先阅读 `memory-bank/@game-design-document.md`
   - 完成主要功能后，更新 `memory-bank/@architecture.md`

3. **上下文管理**
   - 经常使用 `/clear` 或 `/new` 清空上下文
   - 保持 context usage 保持在 50-60% 以下以获得最佳性能
   - 每完成一个步骤后，记录到 `progress.md`

4. **迭代开发**
   - 不一次完成整个功能
   - 每个小步骤都要有测试验证
   - 等待用户验证后再进行下一步

5. **交互式需求收集（强制）** ⚠️
   - ✅ **必须使用 `AskUserQuestion` 工具**收集用户需求和偏好
   - ❌ **禁止**用文本列出问题让用户回答
   - ✅ 让用户通过勾选/选择的方式提供需求
   - ✅ 适用于：项目启动、功能选择、技术栈选型等场景

6. **分支管理检查（强制）** ⚠️
   - ✅ **任何任务启动时，必须先显示当前分支**
   - ✅ **使用 AskUserQuestion 工具让用户选择分支策略**
   - ✅ **如果选择切换分支，再收集目标分支名称**
   - ✅ **如果分支不存在，先提示再创建并切换**
   - ✅ **每个操作步骤都要明确告知用户**
   - ❌ **禁止**自动判断或猜测应该使用哪个分支

   **执行流程**：
   ```
   任务启动时：
   1. 显示: "当前分支: [branch-name]"

   2. 询问分支策略（使用 AskUserQuestion）：
      - 选项1: "使用当前分支 [branch-name]"
      - 选项2: "切换到新分支（如果未创建会自动创建）"

   3. 如果选择选项2，再次询问（使用 AskUserQuestion）：
      "请输入新分支名称："
      - 选项1: "feature-gobang"
      - 选项2: "dev-game"
      - 选项3: "Type something"（让用户自定义）

   4. 执行分支操作：
   # 场景 1: 分支不存在
   → 提示: "分支 '[branch-name]' 不存在，正在创建并切换..."
   → 执行: git checkout -b [branch-name]
   → 确认: "✓ 已创建并切换到新分支 '[branch-name]'"

   # 场景 2: 分支已存在
   → 执行: git checkout [branch-name]
   → 确认: "✓ 已切换到已有分支 '[branch-name]'"

   # 场景 3: 选择使用当前分支
   → 确认: "✓ 继续使用当前分支 '[branch-name]'"
   ```

## 🤖 多角色协作规范

### 工作流程

1. **创建项目前**: 配置角色 (`claude-multi-woker/cmw.config`)
2. **启动协作**: 在 WezTerm 中运行 `python run.py`
3. **分配任务**: 通过 coordinator 角色协调
4. **角色通信**: 使用 `python send <角色> "消息"`
5. **完成审计**: 项目完成后进行多维度审计

### 快速命令

```bash
# 启动多实例
cd claude-multi-woker && python run.py

# 角色通信
python send coordinator "任务已完成"
python send architect "需要架构设计"
```

## 📂 项目结构

```
项目根目录/
├── CLAUDE.md                  # 本文件，自动读取
├── README.md                  # Vibe Coding 规范（必读）
├── MULTI_WORKER_RULES.md      # 多角色协作规范（必读）
├── claude-multi-woker/        # 多实例协作工具
└── .claude/CLAUDE.md          # 项目级配置
```

## ⚡ 快速检查清单

在开始任何开发任务前，确认：

- [ ] 已阅读 `README.md` 中的核心原则
- [ ] 已阅读 `MULTI_WORKER_RULES.md` 中的协作流程
- [ ] 确认是否需要多角色协作
- [ ] 理解项目的架构和设计文档
- [ ] 准备好模块化的代码结构

---

**最后更新**: 2025-01-30
**版本**: v1.1
