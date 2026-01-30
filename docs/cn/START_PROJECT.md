# 新项目启动指南

## 🚀 开始一个新项目

当你想开始一个新项目时，按照以下步骤操作：

### 步骤 1: 创建项目目录

```bash
# 在项目根目录创建你的项目文件夹
mkdir my-project
cd my-project
```

### 步骤 2: 启动多角色协作系统

```bash
# 回到 claude-multi-woker 目录
cd ../claude-multi-woker

# 启动多实例
python run.py
```

### 步骤 3: 切换到 coordinator 标签页并启动项目

在 coordinator 标签页中输入以下启动消息：

```
开始新项目协作模式

请按照以下步骤初始化项目：

1. 了解项目需求
   - 询问用户项目名称、类型、核心功能
   - 询问技术栈偏好
   - 询问是否需要多角色协作

2. 创建项目文档
   - 创建 memory-bank/ 目录
   - 创建 TASK_PROGRESS.md 任务追踪文件
   - 创建 game-design-document.md 或设计文档

3. 配置角色并开始协作
   - 根据项目需求向 architect 发送设计任务
   - 协调后续开发和测试

现在开始：请先询问用户的项目需求。
```

### 步骤 4: 与 coordinator 交互

coordinator 会自动：
- 询问你的项目需求
- 创建必要的文档
- 启动多角色协作流程

---

## 🔄 继续已存在的项目

如果你的项目已经存在（有 TASK_PROGRESS.md），只需：

### 在 coordinator 标签页中说：

```
继续
```

coordinator 会自动：
1. 读取 TASK_PROGRESS.md
2. 恢复项目状态
3. 继续协调工作

---

## 📖 更多信息

- 完整的启动和恢复指南：`claude-multi-woker/STARTUP_GUIDE.md`
- 快速参考卡片：`claude-multi-woker/QUICK_START.txt`
- 强制性规则：`claude-multi-woker/STATUS_SYNC_RULES.md`

---

**版本**: v1.0
**最后更新**: 2026-01-30
