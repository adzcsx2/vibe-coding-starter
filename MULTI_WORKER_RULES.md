# Claude Multi-Worker 协作规范

## 规范概述

在创建新项目或新增需求时，必须使用 `claude-multi-woker` 功能，创建多个角色分工协作完成。

## 工作流程

### 1. 角色定义与配置

在开始项目前，必须先修改 `claude-multi-woker/cmw.config` 中的 `claude.instances` 部分，定义项目所需的角色。

#### 通用角色模板

```json
{
  "providers": ["claude"],
  "flags": {
    "claudeArgs": ["--dangerously-skip-permissions"]
  },
  "claude": {
    "instances": [
      {
        "id": "coordinator",
        "role": "项目协调员 - 负责任务分配、进度跟踪、质量把控",
        "autostart": true
      },
      {
        "id": "architect",
        "role": "架构师 - 负责系统设计、技术选型、架构规划",
        "autostart": true
      },
      {
        "id": "coder",
        "role": "开发工程师 - 负责代码实现、功能开发",
        "autostart": true
      },
      {
        "id": "test",
        "role": "测试工程师 - 负责测试用例编写、质量验证",
        "autostart": true
      },
      {
        "id": "auditor",
        "role": "项目审计员 - 负责完成后的项目审计提示和文档汇总",
        "autostart": false
      }
    ]
  }
}
```

**注意**: `auditor` 角色设置为 `autostart: false`，只在项目需要审计时启动。

#### 角色职责说明

- **coordinator** (协调员): 总负责人，分配任务、协调进度、验收结果
- **architect** (架构师): 设计系统架构、技术方案、目录结构
- **coder** (开发): 实现具体功能、编写业务代码
- **test** (测试): 编写测试、验证功能、报告问题
- **auditor** (审计员): 在项目完成后，提示用户进行多角度审计，汇总审计文档

根据项目需求，可扩展其他角色如：
- `ui` - UI/UX 设计师
- `frontend` - 前端开发
- `backend` - 后端开发
- `devops` - 运维工程师
- `docs` - 文档编写员

### 2. 启动多实例系统

#### 步骤 1: 打开 WezTerm 终端

**重要提示**: 必须在 **WezTerm** 终端中运行，不支持其他终端！

- 检查 WezTerm 是否安装: `wezterm --version`
- 如未安装，访问: https://wezterm.org/index.html

#### 步骤 2: 启动多实例

```bash
cd claude-multi-woker
python run.py
```

系统会自动：
- 启动所有 `autostart: true` 的实例
- 为每个实例创建独立的 WezTerm 标签页
- 生成实例映射到 `.cmw_config/tab_mapping.json`

#### 步骤 3: 在 coordinator 角色中启动项目

切换到 **coordinator** 标签页，输入以下启动提示词：

```
开始项目协作模式

项目名称: [项目名称]
项目类型: [Web应用/CLI工具/库/其他]
项目目标: [一句话描述项目目标]

角色配置:
- coordinator (我): 项目协调员
- architect: 架构师
- coder: 开发工程师
- test: 测试工程师

请开始协调项目开发:
1. 向 architect 发送架构设计任务
2. 等待架构设计完成后，向 coder 分配开发任务
3. 开发完成后，要求 test 进行测试验证
4. 测试通过后，进行最终验收

现在请向 architect 发送第一条任务消息。
```

系统将自动开始协作流程。

### 3. 角色间通信机制

#### 通信命令格式

```bash
# 基本格式
python send <角色ID> "消息内容"

# 示例
python send coder "请实现用户登录功能"
python send test "登录功能已完成，请进行测试"
```

#### 通信规范

**消息格式要求**：
- 明确发送方：消息开头应表明"我是xxx角色"
- 清晰的任务描述：说明需要做什么、为什么、期望结果
- 上下文信息：包含必要的文件路径、依赖关系、前置条件

**标准消息模板**：

```
【角色间通信模板】

我是 [发送方角色]

任务：[具体要做的事情]
背景：[为什么需要这个任务]
要求：[具体的验收标准]
相关文件：[涉及的文件或目录]
依赖：[前置条件或依赖的其他角色工作]

请确认收到并开始工作。
```

#### ⚠️ 强制性状态同步机制

**为了防止上下文丢失和任务遗漏，所有角色必须遵守以下强制规则**：

##### 规则 1: 任务确认机制（强制性）

```bash
# 接收任务时必须回复确认
✅ 正确示例：
python send coordinator "收到任务，开始执行用户认证系统架构设计"

❌ 错误做法：
- 只收到任务不回复
- 假设对方已收到消息
```

##### 规则 2: 任务状态追踪文件（强制性）

**项目根目录必须维护** `TASK_PROGRESS.md`：

```markdown
# 任务进度追踪表

> 本文件由 coordinator 维护，所有角色必须实时同步

## 任务列表

| ID | 任务描述 | 分配给 | 状态 | 分配时间 | 完成时间 | 备注 |
|----|---------|--------|------|----------|----------|------|
| 1 | 设计用户认证系统架构 | architect | ✅ 完成 | T1 | T2 | 文档在 docs/arch.md |
| 2 | 实现登录功能 | coder | 🔄 进行中 | T2 | - | 预计 T4 完成 |
| 3 | 编写测试用例 | test | ⏳ 待开始 | - | - | 依赖任务 2 |
| 4 | 项目审计 | auditor | ⏳ 待开始 | - | - | 依赖任务 3 |

## 状态图例
- ⏳ 待开始 (Pending)
- 🔄 进行中 (In Progress)
- ⏸️ 已阻塞 (Blocked)
- ✅ 已完成 (Completed)
- ❌ 已取消 (Cancelled)

## 最近更新
- T3: coder 开始实现登录功能
- T2: architect 完成架构设计
```

**强制性要求**：
- **coordinator** 必须在分配任务时更新此文件
- **执行角色** 必须在开始/完成时同步状态
- **每次状态变更必须通知 coordinator**

##### 规则 3: 定期心跳机制（强制性）

```python
# 每个角色每完成一个里程碑必须汇报

# architect 完成部分设计时：
python send coordinator "进度更新: 已完成 50% 架构设计，预计 T2 全部完成"

# coder 完成一个模块时：
python send coordinator "进度更新: 登录模块已完成 80%，用户注册已完成"

# test 遇到阻塞时：
python send coordinator "阻塞报告: 缺少 API 文档，无法继续测试，请协调"
```

##### 规则 4: 上下文保护机制（强制性）

```bash
# 每个角色必须定期清理上下文，防止溢出

# 当 context usage > 60% 时：
1. 将当前状态写入 TASK_PROGRESS.md
2. 将重要决策写入 memory-bank/ 目录
3. 使用 /clear 清空对话
4. 重新读取 TASK_PROGRESS.md 恢复上下文

# 示例恢复流程：
"我已经清空了上下文。请读取 TASK_PROGRESS.md 恢复我的工作状态。
当前我正在执行任务 2: 实现登录功能，进度 80%"
```

##### 规则 5: 超时检测与恢复（强制性）

```python
# coordinator 必须定期检查任务状态

# 每 10 分钟检查一次（在 coordinator 角色中）：
python send coordinator "检查任务状态：读取 TASK_PROGRESS.md，确认所有进行中任务是否有进度更新"

# 发现超时任务：
python send <超时角色> "警告：你的任务 [任务ID] 已超时 20 分钟，请报告当前状态或说明是否遇到问题"
```

##### 规则 6: 任务移交检查清单（强制性）

```
任务完成移交前必须检查：

- [ ] 任务结果已写入 TASK_PROGRESS.md
- [ ] 相关文件已创建/更新
- [ ] 已向下一角色发送明确的移交消息
- [ ] 已向 coordinator 汇报完成
- [ ] 下一角色已确认收到任务

示例消息：
"我是 architect
任务 1 已完成：用户认证系统架构设计
产出文件: docs/arch.md, docs/api-spec.md
已通知: coder
请更新任务状态"
```

#### 状态同步工作流示例（完整流程）

```
T0: coordinator 创建 TASK_PROGRESS.md
T1: coordinator → architect "设计用户认证系统" + 更新任务表
T2: architect 回复 "收到任务，开始执行" + coordinator 更新状态为🔄
T3: architect 完成 50% → coordinator "进度 50%"
T4: architect 完成 → 更新 TASK_PROGRESS.md + 通知 coder
T5: coder 回复 "收到架构文档，开始开发" + coordinator 更新状态
T6: coder 完成 → 更新 TASK_PROGRESS.md + 通知 test
T7: test 完成 → 更新 TASK_PROGRESS.md + 通知 coordinator
T8: coordinator 验收 → 标记所有任务 ✅ 完成

关键点：
- 每个步骤都有确认回复
- 每个状态变更都写入文件
- coordinator 作为中央调度器
- 文件系统作为持久化备份
```

### 4. 协作工作流示例

#### 场景：开发新功能（含审计流程）

```
1. coordinator 分配任务
   └─> send architect "设计用户认证系统的架构"

2. architect 完成设计
   └─> send coder "架构设计完成，文档在 docs/arch.md，请开始实现"

3. coder 完成开发
   └─> send test "用户认证功能已实现，代码在 src/auth/，请编写测试"

4. test 完成测试
   └─> send coordinator "测试完成，覆盖率 95%，发现 2 个 bug 已修复，可以发布"

5. coordinator 验收通过
   └─> send auditor "项目开发完成，请提示用户进行项目审计"

6. auditor 提示审计（见审计提示词模板）
   └─> 输出审计提示，等待用户执行审计操作
```

### 5. 启动检查清单

在开始任何项目前，请按顺序完成以下步骤：

- [ ] **步骤 1**: 根据项目类型，修改 `claude-multi-woker/cmw.config` 定义角色
- [ ] **步骤 2**: 确认已安装 WezTerm (`wezterm --version`)
- [ ] **步骤 3**: 创建项目根目录 `TASK_PROGRESS.md` 任务追踪文件
- [ ] **步骤 4**: 在 WezTerm 终端中运行 `cd claude-multi-woker && python run.py`
- [ ] **步骤 5**: 等待所有实例启动完成（会看到多个标签页）
- [ ] **步骤 6**: 切换到 coordinator 标签页
- [ ] **步骤 7**: 在 coordinator 中输入启动提示词（见下方模板）
- [ ] **步骤 8**: coordinator 确认所有角色已就绪
- [ ] **步骤 9**: 观察各角色开始协作工作并同步状态

### 6. 标准启动提示词模板

#### 通用项目启动模板

```
开始项目协作模式

项目名称: [填写项目名称]
项目类型: [Web应用/CLI工具/库/框架/其他]
项目描述: [简要描述项目要实现的功能]
技术栈: [例如: React + Node.js / Python / Go 等]

角色配置:
- coordinator (我): 项目协调员，负责任务分配和进度跟踪
- architect: 架构师，负责系统设计
- coder: 开发工程师，负责代码实现
- test: 测试工程师，负责质量验证
- auditor: 审计员（按需启动），负责项目审计提示

工作流程:
1. 我将向 architect 发送架构设计任务
2. architect 完成后，向 coder 分配开发任务
3. coder 完成后，要求 test 进行测试
4. test 通过后，我进行最终验收
5. 验收通过后，提示用户启动 auditor 进行项目审计

现在开始执行，请向 architect 发送架构设计任务。
```

#### Web 应用项目模板

```
开始 Web 应用项目协作

项目名称: [项目名称]
项目类型: Web 应用
前端技术: [例如: React/Vue/Next.js]
后端技术: [例如: Node.js/Python/Go]
数据库: [例如: PostgreSQL/MongoDB]

核心功能:
1. [功能1]
2. [功能2]
3. [功能3]

角色配置:
- coordinator (我): 项目协调员
- architect: 全栈架构师
- frontend: 前端开发
- backend: 后端开发
- test: 测试工程师

请开始协调: 先向 architect 发送架构设计任务，包含前后端分离方案和 API 设计。
```

#### CLI 工具项目模板

```
开始 CLI 工具项目协作

项目名称: [工具名称]
项目类型: CLI 命令行工具
主要功能: [工具的核心用途]
开发语言: [Python/Go/Rust 等]

角色配置:
- coordinator (我): 需求分析与协调
- architect: CLI 架构设计
- coder: 核心功能开发
- test: 测试与文档

请开始: 向 architect 发送 CLI 结构和命令设计任务。
```

#### 库/框架项目模板

```
开始库开发项目协作

项目名称: [库名称]
项目类型: 开发库/框架
目标用户: [谁会使用这个库]
核心 API: [列出主要 API 接口]

角色配置:
- coordinator (我): API 设计与协调
- architect: 库架构设计
- coder: 核心实现
- docs: 文档编写
- test: 测试工程师

请开始: 向 architect 发送库的模块设计和 API 规范任务。
```

### 8. 审计角色工作流程

#### 审计触发时机

当项目开发完成，coordinator 验收通过后，需要启动审计流程：

```bash
# 1. 在 WezTerm 中手动启动 auditor 标签页
#（或者将 cmw.config 中 auditor 的 autostart 改为 true 后重新运行 python run.py）

# 2. 切换到 coordinator 标签页，发送消息
python send auditor "项目开发已完成，请提示用户进行项目审计"

# 3. 切换到 auditor 标签页，查看审计提示
```

#### 审计提示词模板

auditor 角色在收到审计请求后，应使用以下模板提示用户：

```
🔍 项目审计提示

项目已完成开发和测试，现进入审计阶段。

项目信息:
- 项目名称: [从 coordinator 获取]
- 项目类型: [Web应用/CLI工具/库等]
- 技术栈: [项目使用的技术]
- 完成状态: ✅ 开发完成 ✅ 测试通过

建议审计维度:

1️⃣ 代码质量审计
   使用工具: Claude Code, GitHub Copilot, Codex
   审计内容:
   - 代码规范性检查
   - 安全漏洞扫描
   - 性能优化建议
   - 代码复杂度分析

2️⃣ 架构设计审计
   使用工具: Claude Opus, Gemini
   审计内容:
   - 架构合理性评估
   - 扩展性和可维护性
   - 技术选型合理性
   - 设计模式应用

3️⃣ 文档完整性审计
   使用工具: Claude, ChatGPT
   审计内容:
   - API 文档完整性
   - 用户文档清晰度
   - 代码注释充分性
   - README 规范性

4️⃣ 安全性审计
   使用工具: 专用安全扫描工具 + AI 辅助
   审计内容:
   - 依赖包安全性
   - 输入验证和输出编码
   - 认证授权机制
   - 敏感数据处理

5️⃣ 测试覆盖率审计
   使用工具: 测试工具 + AI 分析
   审计内容:
   - 单元测试覆盖率
   - 集成测试完整性
   - 边界情况测试
   - 错误处理测试

执行步骤:

步骤 1: 选择审计维度
根据项目重要性，选择需要执行的审计维度（建议至少选择 2-3 个）

步骤 2: 使用相应工具进行审计
按照各个维度的审计内容，使用对应的 AI 工具进行审计

步骤 3: 汇总审计结果
将各工具的审计结果整理到统一的审计文档中:
📄 建议创建: docs/AUDIT_REPORT.md

步骤 4: 问题修复与验证
根据审计结果修复发现的问题，并重新验证

步骤 5: 审计结论
形成最终审计结论，决定是否可以发布

审计文档模板:

# 项目审计报告

## 审计概要
- 审计时间: [日期]
- 审计人: [用户名称]
- 审计工具: [使用的工具列表]

## 审计结果

### 代码质量审计
- 发现问题: [列出问题]
- 风险等级: [高/中/低]
- 修复建议: [具体建议]

### 架构设计审计
- 评估结果: [通过/需改进]
- 改进建议: [具体建议]

### 文档完整性审计
- 完整性评分: [分数或评价]
- 缺失内容: [列出缺失部分]

### 安全性审计
- 安全风险: [列出风险点]
- 修复方案: [具体方案]

### 测试覆盖率审计
- 覆盖率: [具体百分比]
- 测试缺口: [未覆盖的部分]

## 总结与建议
[整体评估和发布建议]

---

请根据实际情况选择审计维度和工具，完成审计后请告知结果。
```

#### 审计完成后的处理

用户完成审计后，应向 coordinator 汇报：

```bash
# 在 auditor 标签页
python send coordinator "审计已完成，审计报告在 docs/AUDIT_REPORT.md，共发现 X 个问题，其中高风险 Y 个，建议 [通过/修改后发布/暂不发布]"
```

### 7. 强制执行规则

#### 规则 1: 先配置后开发

在任何项目开始前，必须：
1. 根据项目需求定义角色（修改 `cmw.config`）
2. 启动多实例系统（`python run.py`）
3. 在 coordinator 角色中分配任务

#### 规则 2: 角色职责分离

- 不同角色不得越权操作
- coder 角色不应修改架构设计
- architect 角色不应直接写业务代码
- 每个角色专注于自己的职责

#### 规则 3: 通信可追溯

- 所有的任务分配必须通过 `send` 命令
- 重要的决策和变更必须通知相关角色
- 完成工作后必须向下一角色发送明确的移交消息

#### 规则 4: 质量门禁

- coder 完成代码后，必须由 test 进行测试验证
- test 发现问题，必须通知 coder 修复
- 所有关键功能必须由 coordinator 最终验收

#### 规则 5: 状态同步强制要求 ⚠️

**违反以下任何一条将导致任务丢失风险**：

- ✅ **任务确认**: 接收任务必须回复确认
- ✅ **状态更新**: 每个状态变更必须写入 `TASK_PROGRESS.md`
- ✅ **进度汇报**: 每完成一个里程碑必须向 coordinator 汇报
- ✅ **上下文保护**: context usage > 60% 时必须清理并恢复
- ✅ **移交检查**: 任务移交前必须完成检查清单
- ✅ **超时检测**: coordinator 每 10 分钟检查任务状态

**违规后果**：
- 任务可能丢失或重复执行
- 无法追溯问题根源
- 项目进度无法准确评估
- 团队协作混乱

#### 规则 6: 上下文管理强制要求

- ❌ **禁止**: 单个对话超过 context usage 的 60%
- ✅ **必须**: 定期使用 `/clear` 并从 `TASK_PROGRESS.md` 恢复
- ✅ **必须**: 重要决策写入 `memory-bank/` 永久保存
- ✅ **必须**: 角色切换前保存当前状态到文件

**强制恢复流程**：
```bash
# 当上下文即将溢出时
1. 将当前进度写入 TASK_PROGRESS.md
2. 将关键决策写入 memory-bank/
3. 使用 /clear 清空对话
4. 读取 TASK_PROGRESS.md 恢复工作状态
```

## 项目类型与角色配置

### Web 应用项目

```json
{
  "instances": [
    { "id": "coordinator", "role": "项目协调员", "autostart": true },
    { "id": "architect", "role": "系统架构师", "autostart": true },
    { "id": "frontend", "role": "前端开发", "autostart": true },
    { "id": "backend", "role": "后端开发", "autostart": true },
    { "id": "test", "role": "测试工程师", "autostart": true },
    { "id": "auditor", "role": "项目审计员", "autostart": false }
  ]
}
```

### 库/框架开发项目

```json
{
  "instances": [
    { "id": "coordinator", "role": "项目协调员", "autostart": true },
    { "id": "architect", "role": "API 设计师", "autostart": true },
    { "id": "coder", "role": "核心开发者", "autostart": true },
    { "id": "docs", "role": "文档编写员", "autostart": true },
    { "id": "test", "role": "测试工程师", "autostart": true },
    { "id": "auditor", "role": "项目审计员", "autostart": false }
  ]
}
```

### 简单脚本/工具项目

```json
{
  "instances": [
    { "id": "coordinator", "role": "需求分析与开发", "autostart": true },
    { "id": "coder", "role": "代码实现", "autostart": true },
    { "id": "test", "role": "测试验证", "autostart": true },
    { "id": "auditor", "role": "项目审计员", "autostart": false }
  ]
}
```

## 快速参考卡

### 最小化启动流程

```bash
# 1. 配置角色（编辑 cmw.config）
vim claude-multi-woker/cmw.config

# 2. 在 WezTerm 中启动
cd claude-multi-woker
python run.py

# 3. 在 coordinator 标签页输入启动提示词
```

### 常用通信命令

```bash
# 查看帮助
python send

# 发送消息
python send <角色> "消息内容"

# 示例
python send architect "设计用户认证系统"
python send coder "实现登录功能"
python send test "测试登录流程"
python send coordinator "功能已完成，请验收"
python send auditor "项目已完成，请提示用户进行审计"
```

### 角色简写

```bash
# 支持使用 c1, c2, c3... 代替角色名
python send c1 "消息"  # 第一个实例
python send c2 "消息"  # 第二个实例
python send c3 "消息"  # 第三个实例
```

### 审计流程

```bash
# 1. 项目完成后，coordinator 发送审计请求
python send auditor "项目开发已完成，请提示用户进行项目审计"

# 2. 用户根据 auditor 提示使用多个 AI 工具进行审计

# 3. 完成审计后，auditor 向 coordinator 汇报
python send coordinator "审计已完成，审计报告在 docs/AUDIT_REPORT.md"
```

### 状态同步命令（重要）

```bash
# 任务确认（接收任务后立即回复）
python send coordinator "收到任务 [ID]，开始执行"

# 进度汇报（每完成一个里程碑）
python send coordinator "进度更新: 任务 [ID] 已完成 50%"

# 任务完成（完成时通知）
python send coordinator "任务 [ID] 已完成，产出: [文件路径]"

# 阻塞报告（遇到问题时）
python send coordinator "阻塞报告: 任务 [ID] 被阻塞，原因: [描述]"

# 上下文恢复（清理后恢复状态）
"读取 TASK_PROGRESS.md，当前任务: [ID]，状态: [进行中/已完成]"

# 超时检测（coordinator 定期执行）
python send coordinator "检查任务状态: 读取 TASK_PROGRESS.md 确认所有任务进度"
```

### TASK_PROGRESS.md 更新时机

```bash
# 必须更新 TASK_PROGRESS.md 的时机：
1. coordinator 分配任务时
2. 角色接收任务确认时
3. 任务状态变更时（待开始→进行中→完成）
4. 完成里程碑时
5. 遇到阻塞时
6. 上下文清理前（保存当前状态）
```

### 上下文保护流程

```bash
# 当 context usage > 60% 时强制执行：

# 步骤 1: 保存状态
echo "更新 TASK_PROGRESS.md 记录当前进度"

# 步骤 2: 保存关键决策
echo "将重要决策写入 memory-bank/ 目录"

# 步骤 3: 清理上下文
/clear

# 步骤 4: 恢复状态
"请读取 TASK_PROGRESS.md 和 memory-bank/ 目录，恢复我的工作状态"
```

## 注意事项

1. **消息编码**: 目前 MCP 工具对中文支持有限，发送中文消息请使用命令行方式
2. **映射文件**: 每次启动后 `tab_mapping.json` 会重新生成，确保使用最新的映射
3. **实例数量**: 建议使用 3-5 个角色，过多会导致协作效率下降
4. **角色命名**: 使用小写英文，简短明确，便于输入命令

## 故障排除

### 消息发送失败
- 检查是否在 WezTerm 中运行了 `python run.py`
- 确认 `.cmw_config/tab_mapping.json` 文件存在
- 验证角色 ID 是否正确（区分大小写）

### 实例无响应
- 使用 `Ctrl+C` 退出无响应的实例
- 重新运行 `python run.py` 启动系统

## 版本历史

- v1.3 (2025-01-30):
  - 🔒 **核心更新**: 添加强制性状态同步机制
  - 添加 TASK_PROGRESS.md 任务追踪系统
  - 添加任务确认机制（防止任务丢失）
  - 添加定期心跳汇报机制
  - 添加上下文保护机制（防止溢出）
  - 添加超时检测与恢复机制
  - 添加任务移交检查清单
  - 更新强制执行规则（规则 5、规则 6）
  - 添加状态同步工作流示例
  - 添加快速参考卡中的状态同步命令
- v1.2 (2025-01-30):
  - 添加 auditor（审计员）角色
  - 添加审计工作流程和提示词模板
  - 更新所有角色配置包含 auditor
  - 添加审计维度说明（代码质量/架构/文档/安全/测试）
  - 添加审计文档模板
- v1.1 (2025-01-30):
  - 添加 WezTerm 启动检查步骤
  - 添加标准启动提示词模板（通用/Web应用/CLI工具/库开发）
  - 添加启动检查清单
  - 添加快速参考卡
- v1.0 (2025-01-30): 初始版本，定义基本协作规范
