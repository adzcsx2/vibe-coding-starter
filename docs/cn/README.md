# Vibe Coding 启动模板

**增强版 Vibe Coding 模板，集成多角色 AI 协作系统**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude](https://img.shields.io/badge/Claude-Code-orange)](https://claude.ai/code)
[![Vibe Coding](https://img.shields.io/badge/Vibe_Coding-v1.2.2-blue)](https://github.com/EnzeD/vibe-coding)

**语言**: [English](../../README.md) | [中文](README.md)

---

## 📖 概述

本项目结合了 **Vibe Coding 方法论** 和 **多角色 AI 协作系统**，让你能够使用协调的 AI 代理构建复杂项目，并内置状态同步和任务追踪功能。

### 包含内容

- ✅ **Vibe Coding 指南** - AI 驱动开发的完整指南
- ✅ **多角色系统** - 协调多个 AI 角色（coordinator、architect、coder、test、auditor）
- ✅ **状态同步** - 通过实时进度追踪防止任务丢失
- ✅ **自动读取配置** - Claude 启动时自动读取项目规则
- ✅ **任务追踪模板** - 即用型进度监控系统

---

## 🚀 快速开始

### 新用户指南

**步骤 1**: 阅读 [START_PROJECT.md](START_PROJECT.md) 学习如何开始新项目或继续现有项目。

**步骤 2**: 启动多角色系统：
```bash
cd claude-multi-woker
python run.py
```

**步骤 3**: 在 coordinator 标签页中，输入 **"继续"** 来：
- 启动新项目（如果 TASK_PROGRESS.md 不存在）
- 恢复现有项目（如果 TASK_PROGRESS.md 存在）

就这样！coordinator 会引导你完成其余步骤。

### 前置要求

- **Claude Code**（Pro 订阅，约 $20/月）或 **Codex CLI**（Plus 订阅，约 $20/月）
- **WezTerm** 终端（多角色系统必需）
- **Visual Studio Code**

### 安装

```bash
# 克隆此仓库
git clone https://github.com/adzcsx2/vibe-coding-starter.git
cd vibe-coding-starter

# 启动多角色系统
cd claude-multi-woker
python run.py

# 在 coordinator 标签页输入: 继续
```

---

## 🤖 多角色协作系统

### 概述

多角色系统允许你同时运行多个 AI 实例，每个实例都有特定角色，通过 WezTerm 标签页协调任务。

### 支持的角色

| 角色 | ID | 职责 |
|------|-----|----------------|
| 协调员 | `coordinator` | 任务分配、进度追踪、质量把控 |
| 架构师 | `architect` | 系统设计、技术栈、架构规划 |
| 开发者 | `coder` | 代码实现、功能开发 |
| 测试员 | `test` | 测试用例、质量验证 |
| 审计员 | `auditor` | 完成后审计和文档汇总（按需启动） |

### 启动多角色系统

```bash
# 1. 在 claude-multi-woker/cmw.config 中配置角色
vim claude-multi-woker/cmw.config

# 2. 在 WezTerm 中启动
cd claude-multi-woker
python run.py

# 3. 切换到 coordinator 标签页并提供启动提示
```

### 角色通信

```bash
# 在角色之间发送消息
python send <角色> "消息内容"

# 示例
python send architect "设计用户认证系统"
python send coder "实现登录功能"
python send test "测试认证流程"
python send coordinator "功能已完成，请审查"
```

---

## 📋 状态同步系统

### 解决的问题

**无状态同步**: 约50%的风险因上下文丢失导致任务静默失败
**有状态同步**: <5%的风险，10分钟检测和恢复能力

### 关键特性

1. **任务确认** - 接收任务时强制回复
2. **进度追踪** - `TASK_PROGRESS.md` 实时更新
3. **心跳汇报** - 里程碑更新到 coordinator
4. **上下文保护** - 上下文 >60% 时自动清理
5. **超时检测** - 10分钟状态检查
6. **恢复指南** - 上下文丢失后从文件系统恢复

### 使用方法

```bash
# 复制模板
cp TASK_PROGRESS.md.template TASK_PROGRESS.md

# 更新任务状态（coordinator 维护此文件）
# 所有角色通过此文件同步进度
```

---

## 📚 开发规则

### 必读文档

Claude 启动时自动读取这些文件（按优先级）：

1. **README.md** - 本文件（项目概述）
2. **CLAUDE.md** - 开发规则和多角色协作要求
3. **MULTI_WORKER_RULES.md** - 完整的多角色协作指南

### 核心原则

```markdown
关键原则："规划就是一切。不要让 AI 自主规划，
否则你的代码库会变成无法管理的混乱。"

强制规则：
✅ 使用模块化架构（多个文件）
❌ 避免单体（单个巨大文件）
✅ 编码前始终阅读 memory-bank/@architecture.md
✅ 频繁清理上下文（保持使用率 <60%）
✅ 每个里程碑后记录进度
```

---

## 📁 项目结构

```
vibe-coding-starter/
├── README.md                      # 本文件
├── CLAUDE.md                      # 自动读取的开发规则
├── MULTI_WORKER_RULES.md          # 多角色协作指南
├── TASK_PROGRESS.md.template      # 任务追踪模板
├── QUICK_REFERENCE.md             # 快速参考卡
├── SYSTEM_AUDIT_REPORT.md         # 系统审计报告
├── .claude/
│   └── COORDINATOR_INSTRUCTIONS.md # Coordinator 专用指令
├── claude-multi-woker/            # 多角色系统
│   ├── cmw.config                 # 角色配置
│   ├── run.py                     # 启动脚本
│   └── send                       # 通信脚本
├── docs/
│   └── cn/                        # 中文文档
│       ├── README.md              # 中文版 README
│       ├── MULTI_WORKER_RULES.md  # 多角色协作规范
│       ├── QUICK_REFERENCE.md     # 快速参考卡
│       └── ...                    # 其他中文文档
└── memory-bank/                   # 为你的项目创建此目录
    ├── game-design-document.md    # 你的 GDD/PRD
    ├── tech-stack.md              # 技术选择
    ├── implementation-plan.md     # 逐步计划
    ├── progress.md                # 已完成的步骤
    └── architecture.md            # 文件文档
```

---

## 🎯 典型工作流程

### 选项 1：单角色开发（简单项目）

```bash
1. 在 memory-bank/ 中创建 GDD/PRD
2. 生成实施计划
3. 使用单个 Claude 实例
4. 逐步执行，频繁 /clear
5. 在 TASK_PROGRESS.md 中追踪进度
```

### 选项 2：多角色协作（复杂项目）

```bash
1. 在 cmw.config 中配置角色
2. 启动多角色系统 (python run.py)
3. Coordinator 分配任务
4. Architect 设计系统
5. Coder 实现功能
6. Tester 验证质量
7. Auditor 进行完成后审计
```

### 选项 3：混合模式（大型项目推荐）

```bash
阶段 1: 规划（多角色）
  └─ coordinator + architect

阶段 2: 开发（单角色）
  └─ 单个 coder 使用 TASK_PROGRESS.md 追踪
  └─ 定期 /clear 保持上下文

阶段 3: 测试与审计（多角色）
  └─ test + auditor
```

---

## 🔧 配置

### 多角色配置

编辑 `claude-multi-woker/cmw.config`：

```json
{
  "claude": {
    "instances": [
      { "id": "coordinator", "role": "项目协调员", "autostart": true },
      { "id": "architect", "role": "系统架构师", "autostart": true },
      { "id": "coder", "role": "开发工程师", "autostart": true },
      { "id": "test", "role": "测试工程师", "autostart": true },
      { "id": "auditor", "role": "项目审计员", "autostart": false }
    ]
  }
}
```

### 自定义规则

编辑 `.claude/CLAUDE.md` 或 `CLAUDE.md` 添加 Claude 自动读取的项目特定规则。

---

## 📖 Vibe Coding 指南

此模板包含 [Nicolas Zullo](https://x.com/NicolasZu) 编写的完整 **Ultimate Guide to Vibe Coding v1.2.2**。

**关键亮点**：

- 规划优先方法论
- 内存库组织
- 迭代实现
- 上下文管理最佳实践
- AI 代理优化技巧

完整指南请见原始仓库：[EnzeD/vibe-coding](https://github.com/EnzeD/vibe-coding)

---

## 🚨 强制测试流程（关键特性）

### 核心规则

每个 **coder 任务完成后必须经过 test 验收，由 coordinator 统一调度**。

**为什么？**
- ✅ Coordinator 上下文更少，不易忘记测试环节
- ✅ 保持中央调度的统一性
- ✅ 便于任务状态追踪
- ✅ Coder 可能因上下文过长而忘记

### 强制流程

```
① coder 完成开发
    ↓
② coder 通知 coordinator（禁止直接通知 test）
    python send coordinator "任务X已完成，请安排测试"
    ↓
③ coordinator 立即分配 test（5分钟内）
    python send test "请测试任务X..."
    更新状态为 "🔄 待测试"
    ↓
④ test 执行测试
    ↓
⑤ test 向 coordinator 汇报结果
    python send coordinator "测试完成，结果：[通过/失败]"
    ↓
⑥ coordinator 决策
    通过 → 标记 "✅ 完成"
    失败 → 通知 coder 修复 → 返回 ①
```

详见：[MULTI_WORKER_RULES.md](MULTI_WORKER_RULES.md) 和 [QUICK_REFERENCE.md](QUICK_REFERENCE.md)

---

## 🤝 贡献

欢迎贡献！请随时提交 Pull Request。

---

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](../../LICENSE) 文件。

---

## 🙏 致谢

- **Vibe Coding Guide** by [Nicolas Zullo](https://x.com/NicolasZu)
- **Claude Multi-Worker** 多实例 AI 协调系统
- **Anthropic** 提供 Claude Code 和 Claude AI
- **OpenAI** 提供 Codex CLI 和 GPT 模型

---

## 📞 支持

如有问题或疑问：
- 在 GitHub 上提 issue
- 查看 [MULTI_WORKER_RULES.md](MULTI_WORKER_RULES.md) 了解协作系统详情
- 查看 [QUICK_REFERENCE.md](QUICK_REFERENCE.md) 快速参考
- 查看 [Vibe Coding Guide](https://github.com/EnzeD/vibe-coding) 了解方法论问题

---

**最后更新**: 2026年1月30日
**版本**: 1.0.0
