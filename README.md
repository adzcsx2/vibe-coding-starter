# Vibe Coding Starter Template

**Enhanced Vibe Coding template with multi-role AI collaboration system**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude](https://img.shields.io/badge/Claude-Code-orange)](https://claude.ai/code)
[![Vibe Coding](https://img.shields.io/badge/Vibe_Coding-v1.2.2-blue)](https://github.com/EnzeD/vibe-coding)

---

## 📖 Overview

This project combines the **Vibe Coding methodology** with a **Multi-Worker AI collaboration system**, enabling you to build complex projects using coordinated AI agents with built-in state synchronization and task tracking.

### What's Included

- ✅ **Vibe Coding Guide** - Complete guide for AI-powered development
- ✅ **Multi-Worker System** - Coordinate multiple AI roles (coordinator, architect, coder, test, auditor)
- ✅ **State Synchronization** - Prevent task loss with real-time progress tracking
- ✅ **Auto-Reading Configuration** - Claude automatically reads project rules on startup
- ✅ **Task Tracking Template** - Ready-to-use progress monitoring system

---

## 🚀 Quick Start

### For New Users

**Step 1**: Read [START_PROJECT.md](START_PROJECT.md) to learn how to start a new project or continue an existing one.

**Step 2**: Start the multi-worker system:
```bash
cd claude-multi-woker
python run.py
```

**Step 3**: In the coordinator tab, say **"继续"** (continue) to:
- Start a new project (if no TASK_PROGRESS.md exists)
- Resume an existing project (if TASK_PROGRESS.md exists)

That's it! The coordinator will guide you through the rest.

### Prerequisites

- **Claude Code** (Pro subscription, ~$20/month) OR **Codex CLI** (Plus subscription, ~$20/month)
- **WezTerm** terminal (required for multi-worker system)
- **Visual Studio Code**

### Installation

```bash
# Clone this repository
git clone https://github.com/adzcsx2/vibe-coding-starter.git
cd vibe-coding-starter

# Start the multi-worker system
cd claude-multi-woker
python run.py

# In coordinator tab, type: 继续
```

---

## 🤖 Multi-Worker Collaboration System

### Overview

The multi-worker system enables you to run multiple AI instances simultaneously, each with a specific role, coordinating tasks through WezTerm tabs.

### Supported Roles

| Role | ID | Responsibility |
|------|-----|----------------|
| Coordinator | `coordinator` | Task allocation, progress tracking, quality control |
| Architect | `architect` | System design, tech stack, architecture planning |
| Developer | `coder` | Code implementation, feature development |
| Tester | `test` | Test cases, quality validation |
| Auditor | `auditor` | Post-completion audit and documentation (on-demand) |

### Starting Multi-Worker System

```bash
# 1. Configure roles in claude-multi-woker/cmw.config
vim claude-multi-woker/cmw.config

# 2. Launch in WezTerm
cd claude-multi-woker
python run.py

# 3. Switch to coordinator tab and provide startup prompt
```

### Role Communication

```bash
# Send messages between roles
python send <role> "message content"

# Examples
python send architect "Design user authentication system"
python send coder "Implement login feature"
python send test "Test authentication flow"
python send coordinator "Feature completed, please review"
```

---

## 📋 State Synchronization System

### Problem Solved

**Without state sync**: ~50% risk of silent task failures due to context loss
**With state sync**: <5% risk with 10-minute detection and recovery capability

### Key Features

1. **Task Confirmation** - Mandatory reply when receiving tasks
2. **Progress Tracking** - `TASK_PROGRESS.md` real-time updates
3. **Heartbeat Reporting** - Milestone updates to coordinator
4. **Context Protection** - Auto-cleanup when context >60%
5. **Timeout Detection** - 10-minute status checks
6. **Recovery Guide** - Restore from file system after context loss

### Usage

```bash
# Copy the template
cp TASK_PROGRESS.md.template TASK_PROGRESS.md

# Update task status (coordinator maintains this file)
# All roles sync progress through this file
```

---

## 📚 Development Rules

### Mandatory Reading

Claude automatically reads these files on startup (in priority order):

1. **README.md** - This file (project overview)
2. **CLAUDE.md** - Development rules and multi-role collaboration requirements
3. **MULTI_WORKER_RULES.md** - Complete multi-worker collaboration guide

### Core Principles

```markdown
Key Principle: "Planning is everything. Do NOT let the AI plan autonomously,
or your codebase will become an unmanageable mess."

Mandatory Rules:
✅ Use modular architecture (multiple files)
❌ Avoid monolith (single giant file)
✅ Always read memory-bank/@architecture.md before coding
✅ Clear context frequently (keep usage <60%)
✅ Document progress after each milestone
```

---

## 📁 Project Structure

```
vibe-coding-starter/
├── README.md                      # This file
├── CLAUDE.md                      # Auto-read development rules
├── MULTI_WORKER_RULES.md          # Multi-role collaboration guide
├── TASK_PROGRESS.md.template      # Task tracking template
├── .claude/
│   └── CLAUDE.md                  # Project-level configuration
├── claude-multi-woker/            # Multi-worker system
│   ├── cmw.config                 # Role configuration
│   ├── run.py                     # Launch script
│   └── send                       # Communication script
└── memory-bank/                   # Create this for your projects
    ├── game-design-document.md    # Your GDD/PRD
    ├── tech-stack.md              # Technology choices
    ├── implementation-plan.md     # Step-by-step plan
    ├── progress.md                # Completed steps
    └── architecture.md            # File documentation
```

---

## 🎯 Typical Workflow

### Option 1: Single-Role Development (Simple Projects)

```bash
1. Create GDD/PRD in memory-bank/
2. Generate implementation plan
3. Use single Claude instance
4. Execute step-by-step with frequent /clear
5. Track progress in TASK_PROGRESS.md
```

### Option 2: Multi-Role Collaboration (Complex Projects)

```bash
1. Configure roles in cmw.config
2. Launch multi-worker system (python run.py)
3. Coordinator assigns tasks
4. Architect designs system
5. Coder implements features
6. Tester validates quality
7. Auditor conducts post-completion audit
```

### Option 3: Hybrid Mode (Recommended for Large Projects)

```bash
Phase 1: Planning (multi-role)
  └─ coordinator + architect

Phase 2: Development (single role)
  └─ single coder with TASK_PROGRESS.md tracking
  └─ periodic /clear to maintain context

Phase 3: Testing & Audit (multi-role)
  └─ test + auditor
```

---

## 🔧 Configuration

### Multi-Worker Role Configuration

Edit `claude-multi-woker/cmw.config`:

```json
{
  "claude": {
    "instances": [
      { "id": "coordinator", "role": "Project coordinator", "autostart": true },
      { "id": "architect", "role": "System architect", "autostart": true },
      { "id": "coder", "role": "Developer", "autostart": true },
      { "id": "test", "role": "QA engineer", "autostart": true },
      { "id": "auditor", "role": "Project auditor", "autostart": false }
    ]
  }
}
```

### Custom Rules

Edit `.claude/CLAUDE.md` or `CLAUDE.md` to add project-specific rules that Claude reads automatically.

---

## 📖 Vibe Coding Guide

This template includes the complete **Ultimate Guide to Vibe Coding v1.2.2** by [Nicolas Zullo](https://x.com/NicolasZu).

**Key highlights**:

- Planning-first methodology
- Memory bank organization
- Iterative implementation
- Context management best practices
- AI agent optimization tips

For the complete guide, see the original repository: [EnzeD/vibe-coding](https://github.com/EnzeD/vibe-coding)

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Vibe Coding Guide** by [Nicolas Zullo](https://x.com/NicolasZu)
- **Claude Multi-Worker** system for multi-instance AI coordination
- **Anthropic** for Claude Code and Claude AI
- **OpenAI** for Codex CLI and GPT models

---

## 📞 Support

For questions or issues:
- Open an issue on GitHub
- Check the [MULTI_WORKER_RULES.md](MULTI_WORKER_RULES.md) for collaboration system details
- Review the [Vibe Coding Guide](https://github.com/EnzeD/vibe-coding) for methodology questions

---

**Last Updated**: January 30, 2026
**Version**: 1.0.0
