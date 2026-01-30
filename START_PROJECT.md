# New Project Startup Guide

**Language**: [English](START_PROJECT.md) | [中文](docs/cn/START_PROJECT.md)

---

## 🚀 Starting a New Project

When you want to start a new project, follow these steps:

### Step 1: Create Project Directory

```bash
# Create your project folder in the root directory
mkdir my-project
cd my-project
```

### Step 2: Start Multi-Role Collaboration System

```bash
# Return to claude-multi-woker directory
cd ../claude-multi-woker

# Launch multi-instance system
python run.py
```

### Step 3: Switch to Coordinator Tab and Start Project

Enter the following startup message in the coordinator tab:

```
Start new project collaboration mode

Please initialize the project following these steps:

1. Understand project requirements
   - Ask user for project name, type, core features
   - Ask for technology stack preferences
   - Ask if multi-role collaboration is needed

2. Create project documents
   - Create memory-bank/ directory
   - Create TASK_PROGRESS.md task tracking file
   - Create game-design-document.md or design document

3. Configure roles and start collaboration
   - Send design tasks to architect based on requirements
   - Coordinate subsequent development and testing

Start now: Please first ask the user about project requirements.
```

### Step 4: Interact with Coordinator

The coordinator will automatically:
- Ask about your project requirements
- Create necessary documents
- Launch multi-role collaboration workflow

---

## 🔄 Continue Existing Project

If your project already exists (has TASK_PROGRESS.md), simply:

### In the coordinator tab, say:

```
continue
```

The coordinator will automatically:
1. Read TASK_PROGRESS.md
2. Restore project status
3. Continue coordinating work

---

## 📖 More Information

- Complete startup and recovery guide: See [MULTI_WORKER_RULES.md](MULTI_WORKER_RULES.md)
- Quick reference card: See [QUICK_REFERENCE.md](QUICK_REFERENCE.md)
- Mandatory rules: See `.claude/COORDINATOR_INSTRUCTIONS.md`

---

**Version**: v1.0  
**Last Updated**: January 30, 2026
