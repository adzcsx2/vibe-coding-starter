# Claude 项目级配置

## 🔴 强制性要求

⚠️ **CRITICAL**: 在执行任何开发任务前，必须按顺序阅读：

### 1️⃣ README.md - Vibe Coding 核心规范

**必须遵守的核心原则**：

```markdown
Key Principle: "Planning is everything. Do NOT let the AI plan autonomously,
or your codebase will become an unmanageable mess."
```

**强制性规则**：

1. **模块化强制要求**
   - ✅ 必须使用多个文件，保持代码模块化
   - ❌ 严禁创建单体文件（monolith）
   - ✅ 每个文件职责单一、边界清晰

2. **Always Rules（始终触发）**
   ```
   # 在编写任何代码前，必须：
   - 阅读 memory-bank/@architecture.md
   - 阅读 memory-bank/@game-design-document.md
   - 完成功能后更新 memory-bank/@architecture.md
   ```

3. **上下文管理**
   - 每完成一个步骤后使用 `/clear` 或 `/new`
   - 保持 context usage < 60%
   - 每步都记录到 progress.md

4. **迭代原则**
   - 不一次完成整个功能
   - 每个小步骤都要有测试
   - 等待用户验证后再继续

### 2️⃣ MULTI_WORKER_RULES.md - 多角色协作规范

**协作工作流程**：

1. 创建项目前配置角色 (`claude-multi-woker/cmw.config`)
2. 在 WezTerm 中启动多实例 (`python run.py`)
3. 使用 coordinator 角色分配任务
4. 通过 `send` 命令进行角色间通信
5. 完成后进行项目审计

## 🎯 开发任务检查清单

在开始任何开发任务前：

- [ ] 已阅读 README.md 核心原则
- [ ] 已阅读 MULTI_WORKER_RULES.md 协作流程
- [ ] 确认是否需要多角色协作
- [ ] 理解项目架构（architecture.md）
- [ ] 准备好模块化设计方案

## 📋 快速参考

```bash
# 启动多角色协作
cd claude-multi-woker && python run.py

# 角色间通信
python send <角色> "消息内容"

# 示例
python send coordinator "任务完成"
python send architect "需要架构设计"
```

## 🚨 违规后果

不遵循上述规范将导致：
- 代码结构混乱，难以维护
- 上下文溢出，性能下降
- 协作混乱，效率低下
- 项目质量不达标

---

**本文件位置**: `.claude/CLAUDE.md`
**自动读取优先级**: 高（项目级配置）
**最后更新**: 2025-01-30
