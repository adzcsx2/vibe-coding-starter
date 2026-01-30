# Multi-Worker Collaboration Guidelines

**Language**: [English](MULTI_WORKER_RULES.md) | [中文](docs/cn/MULTI_WORKER_RULES.md)

---

## Overview

When creating a new project or adding new requirements, you must use the `claude-multi-woker` feature to create multiple roles working collaboratively.

**⚠️ Important Note: Toolkit Positioning**

**`claude-multi-woker` is a toolkit directory, not the project itself.**

### Toolkit vs Project Relationship

```
Project Root/                      # ← Your actual project is here
├── README.md                      # ← Read project rules from here
├── CLAUDE.md                      # ← Read development guidelines from here
├── TASK_PROGRESS.md               # ← Update this file
├── memory-bank/                   # ← Work here
└── claude-multi-woker/            # ← Toolkit (only for launching)
    ├── run.py                     # ← Launch script
    └── send                       # ← Communication tool
```

### Core Rules

1. **Toolkit Responsibility**: `claude-multi-woker/` is only for launching the multi-role system
2. **Project File Location**: All project files are in **project root** (parent directory)
3. **Read Rules**: After launching, all Claude instances must read rules from project root
4. **Work Location**: Code, docs, and configs are in project root, not in toolkit directory

---

## Workflow

### 1. Role Definition and Configuration

Before starting a project, you must modify the `claude.instances` section in `claude-multi-woker/cmw.config` to define required roles.

#### Common Role Template

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
        "role": "Project Coordinator - Task allocation, progress tracking, quality control",
        "autostart": true
      },
      {
        "id": "architect",
        "role": "Architect - System design, tech stack, architecture planning",
        "autostart": true
      },
      {
        "id": "coder",
        "role": "Developer - Code implementation, feature development",
        "autostart": true
      },
      {
        "id": "test",
        "role": "Test Engineer - Test cases, quality validation",
        "autostart": true
      },
      {
        "id": "auditor",
        "role": "Project Auditor - Post-completion audit and documentation",
        "autostart": false
      }
    ]
  }
}
```

**Note**: `auditor` role is set to `autostart: false`, only started when project audit is needed.

#### Role Responsibilities

- **coordinator**: Overall responsibility, task allocation, progress coordination, result acceptance
- **architect**: Design system architecture, technical solutions, directory structure
- **coder**: Implement specific features, write business code
- **test**: Write tests, validate features, report issues
- **auditor**: After project completion, prompt user for multi-perspective audit, compile audit documentation

Expand other roles as needed:
- `ui` - UI/UX Designer
- `frontend` - Frontend Developer
- `backend` - Backend Developer
- `devops` - DevOps Engineer
- `docs` - Documentation Writer

---

### 2. Start Multi-Instance System

#### Step 1: Open WezTerm Terminal

**Important**: Must run in **WezTerm** terminal, no other terminals supported!

- Check if WezTerm is installed: `wezterm --version`
- If not installed, visit: https://wezterm.org/index.html

#### Step 2: Launch Multi-Instance

```bash
cd claude-multi-woker
python run.py
```

The system will automatically:
- Start all instances with `autostart: true`
- Create independent WezTerm tabs for each instance
- Generate instance mapping to `.cmw_config/tab_mapping.json`

#### Step 3: Start Project in Coordinator Role

Switch to **coordinator** tab and enter the following startup prompt:

```
Start project collaboration mode

Project name: [project name]
Project type: [Web app / CLI tool / Library / Other]
Project goal: [Brief description]

Role configuration:
- coordinator (me): Project Coordinator
- architect: Architect
- coder: Developer
- test: Test Engineer

Please start coordinating the project:
1. Send architecture design task to architect
2. After architect completes, assign development tasks to coder
3. After coder completes, require test to perform testing
4. After test passes, conduct final acceptance

Now send the first task message to architect.
```

The system will automatically start the collaboration process.

---

### 3. Role Communication Mechanism

#### Communication Command Format

```bash
# Basic format
python send <role-id> "message content"

# Examples
python send coder "Please implement user login feature"
python send test "Login feature completed, please test"
```

#### Communication Standards

**Message Format Requirements**:
- Clear sender: Message should start with "I am XXX role"
- Clear task description: What needs to be done, why, expected results
- Context information: Include necessary file paths, dependencies, prerequisites

**Standard Message Template**:

```
[Role Communication Template]

I am [sender role]

Task: [Specific task to do]
Background: [Why this task is needed]
Requirements: [Specific acceptance criteria]
Related files: [Files or directories involved]
Dependencies: [Prerequisites or dependencies on other roles' work]

Please confirm receipt and start work.
```

---

### 4. Mandatory Testing Workflow ⭐⭐⭐

**Every development task completion must be tested and validated, coordinated uniformly by coordinator.**

#### Why Coordinator Schedules Instead of Coder Directly Notifying Test?
- ✅ Coordinator has less context, less likely to forget testing phase
- ✅ Maintains central scheduling consistency
- ✅ Facilitates task status tracking and TASK_PROGRESS.md unified management
- ✅ Coder may forget to notify testing due to long context

#### Mandatory Flow:

```
① coder completes development
    ↓
② coder must notify coordinator (not test directly)
    python send coordinator "Task X completed, output: [file list], please arrange testing"
    ↓
③ coordinator must immediately (within 5 minutes) assign test task
    python send test "Please test Task X, content: [desc], files: [files]"
    Update TASK_PROGRESS.md status to "🧪 Testing"
    ↓
④ test executes testing
    ↓
⑤ test reports result to coordinator
    python send coordinator "Task X test [pass/fail], report: [details]"
    ↓
⑥ coordinator makes decision
    Pass → Update TASK_PROGRESS.md to ✅ Complete, assign next task
    Fail → python send coder "Test found issues: [issues], please fix"
          → coder fixes and repeats step ②
```

**Forbidden Actions**:
- ❌ coder directly `send test` (bypassing coordinator)
- ❌ coder marks task complete on their own
- ❌ coordinator forgets to assign test validation
- ❌ test validates without marking complete

**Violation Consequences**:
- Task invalid, must retest
- Quality cannot be guaranteed, may introduce bugs
- Task status chaotic, cannot trace

---

### 5. State Synchronization System

#### ⚠️ Mandatory State Sync Mechanism

**To prevent context loss and task omission, all roles must follow these mandatory rules**:

##### Rule 1: Task Confirmation Mechanism (Mandatory)

```bash
# Must reply confirmation when receiving task
✅ Correct example:
python send coordinator "Task received, starting user authentication system architecture design"

❌ Wrong approach:
- Only receive task without replying
- Assume other party received message
```

##### Rule 2: Task Status Tracking File (Mandatory)

**Project root must maintain** `TASK_PROGRESS.md`:

```markdown
# Task Progress Tracking

> This file is maintained by coordinator, all roles must sync in real-time

## Task List

| ID | Description | Assigned To | Status | Assigned | Completed | Notes |
|----|-------------|-------------|--------|----------|-----------|-------|
| 1 | Design user auth system | architect | ✅ Complete | T1 | T2 | Docs in docs/arch.md |
| 2 | Implement login feature | coder | 🔄 In Progress | T2 | - | Est. T4 completion |
| 3 | Write test cases | test | ⏳ Pending | - | - | Depends on task 2 |

## Status Legend
- ⏳ Pending
- 🔄 In Progress  
- 🧪 Testing (coder tasks only - dev done, awaiting test validation)
- ⏸️ Blocked
- ✅ Complete
- ❌ Cancelled

## Latest Updates
- T3: coder started login feature implementation
- T2: architect completed architecture design
```

**Mandatory Requirements**:
- **coordinator** must update this file when assigning tasks
- **Executing roles** must sync status when starting/completing
- **Every status change must notify coordinator**

---

### 6. Standard Startup Prompt Template

#### General Project Startup Template

```
Start project collaboration mode

Project name: [Fill in project name]
Project type: [Web app / CLI tool / Library / Framework / Other]
Project description: [Brief description of features to implement]
Tech stack: [e.g., React + Node.js / Python / Go, etc.]

Role configuration:
- coordinator (me): Project coordinator, responsible for task allocation and progress tracking
- architect: Architect, responsible for system design
- coder: Developer, responsible for code implementation
- test: Test engineer, responsible for quality validation
- auditor: Auditor (on-demand startup), responsible for project audit prompts

Workflow:
1. I will send architecture design task to architect
2. After architect completes, assign development tasks to coder
3. After coder completes, require test to perform testing
4. After test passes, conduct final acceptance
5. After acceptance passes, prompt user to start auditor for project audit

Now start execution, send architecture design task to architect.
```

---

### 7. Quick Reference Card

#### Minimum Startup Process

```bash
# 1. Configure roles (edit cmw.config)
vim claude-multi-woker/cmw.config

# 2. Launch in WezTerm
cd claude-multi-woker
python run.py

# 3. Enter startup prompt in coordinator tab
```

#### Common Communication Commands

```bash
# View help
python send

# Send message
python send <role> "message content"

# Examples
python send architect "Design user authentication system"
python send coder "Implement login feature"
python send test "Test login process"
python send coordinator "Feature completed, please review"
```

#### State Sync Commands (Important)

```bash
# Task confirmation (immediately reply after receiving task)
python send coordinator "Task [ID] received, starting execution"

# Progress report (every milestone completion)
python send coordinator "Progress update: Task [ID] 50% complete"

# Task completion (notify when done)
python send coordinator "Task [ID] completed, output: [file path]"

# Block report (when encountering issues)
python send coordinator "Block report: Task [ID] blocked, reason: [description]"
```

---

## Version History

- v1.4 (2026-01-30):
  - 🔒 **Core Update**: Added mandatory testing workflow
  - Mandatory: coder completes → notify coordinator → coordinator assigns test
  - Added "🧪 Testing" status
  - Forbidden: coder directly notifying test, bypassing coordinator
  - coordinator must assign test within 5 minutes
  - Only mark complete after test validation passes
- v1.3 (2025-01-30):
  - 🔒 **Core Update**: Added mandatory state synchronization mechanism
  - Added TASK_PROGRESS.md task tracking system
  - Added task confirmation mechanism (prevent task loss)
  - Added regular heartbeat reporting mechanism
  - Added context protection mechanism (prevent overflow)
  - Added timeout detection and recovery mechanism
  - Added task handover checklist
- v1.2 (2025-01-30):
  - Added auditor role
  - Added audit workflow and prompt templates
- v1.1 (2025-01-30):
  - Added WezTerm startup check steps
  - Added standard startup prompt templates
- v1.0 (2025-01-30): Initial version, defined basic collaboration guidelines

---

**For complete Chinese version and detailed workflow examples, see**: [docs/cn/MULTI_WORKER_RULES.md](docs/cn/MULTI_WORKER_RULES.md)

**Quick Reference**: [QUICK_REFERENCE.md](QUICK_REFERENCE.md)

**Last Updated**: January 30, 2026  
**Version**: v1.4
