# Claude Project Configuration

**Language**: [English](CLAUDE.md) | [中文](docs/cn/CLAUDE.md)

---

## 🔒 Mandatory Development Guidelines

⚠️ **CRITICAL**: Before executing any task, you must read the following files:

### 1. Required Reading (by Priority)

```
Priority 0: .claude/ALL_ROLES_INSTRUCTIONS.md - Required for all roles (except coordinator)!
Priority 1: README.md         - Vibe Coding core principles and development guidelines
Priority 2: MULTI_WORKER_RULES.md  - Multi-role collaboration workflow
Priority 3: .claude/COORDINATOR_INSTRUCTIONS.md  - Coordinator role instructions
```

### 1.1 Role-Specific Reading Guide

#### 🔴 If You Are Any Role (architect/coder/test/ui/docs, etc.)

**Must Read**: `.claude/ALL_ROLES_INSTRUCTIONS.md`

**Most Important Rules**:

- **Must** notify coordinator after task completion
- Must use standard completion message template
- **Must** clear context (`/clear`) after task completion

See: `.claude/ALL_ROLES_INSTRUCTIONS.md`

#### 🟢 If You Are the Coordinator Role

**Must Read**: `.claude/COORDINATOR_INSTRUCTIONS.md`

**Most Important Instruction**: Handle user's "continue" command

- When user says "continue", first check if `TASK_PROGRESS.md` exists
- If exists → Read and restore project status
- If not exists → Start new project workflow

See: `.claude/COORDINATOR_INSTRUCTIONS.md`

### 2. Core Principles (from README.md)

#### 🔑 Key Principle

**"Planning is everything. Do NOT let the AI plan autonomously, or your codebase will become an unmanageable mess."**

#### 📐 Mandatory Rules

1. **Modularity First**
   - ✅ Use multiple files, keep code modular
   - ❌ Forbidden to create monolith files
   - ✅ Each file has single, clear responsibility

2. **Always Rules (Always Follow)**
   - Before writing any code, read `memory-bank/@architecture.md`
   - Before writing any code, read `memory-bank/@game-design-document.md`
   - After completing major features, update `memory-bank/@architecture.md`

3. **Context Management**
   - Frequently use `/clear` or `/new` to clear context
   - Keep context usage below 50-60% for optimal performance
   - After completing each step, record in `progress.md`

4. **Iterative Development**
   - Don't complete entire feature at once
   - Each small step requires testing validation
   - Wait for user validation before next step

5. **Interactive Requirements Gathering (Mandatory)** ⚠️
   - ✅ **Must use `AskUserQuestion` tool** to collect user requirements and preferences
   - ❌ **Forbidden** to list questions in text for user to answer
   - ✅ Let users provide requirements through checkboxes/selections
   - ✅ Applicable to: project startup, feature selection, tech stack choices, etc.

6. **Branch Management Check (Mandatory)** ⚠️
   - ✅ **At task startup, must first display current branch**
   - ✅ **Use AskUserQuestion tool to let user choose branch strategy**
   - ✅ **If switching branch, then collect target branch name**
   - ✅ **If branch doesn't exist, prompt before creating and switching**
   - ✅ **Clearly inform user of each operation step**
   - ❌ **Forbidden** to automatically determine or guess which branch to use

   **Execution Flow**:

   ```
   At task startup:
   1. Display: "Current branch: [branch-name]"

   2. Ask branch strategy (using AskUserQuestion):
      - Option 1: "Use current branch [branch-name]"
      - Option 2: "Switch to new branch (will auto-create if doesn't exist)"

   3. If Option 2 selected, ask again (using AskUserQuestion):
      "Please enter new branch name:"
      - Option 1: "feature-gobang"
      - Option 2: "dev-game"
      - Option 3: "Type something" (user custom input)

   4. Execute branch operation:
   # Scenario 1: Branch doesn't exist
   → Prompt: "Branch '[branch-name]' doesn't exist, creating and switching..."
   → Execute: git checkout -b [branch-name]
   → Confirm: "✓ Created and switched to new branch '[branch-name]'"

   # Scenario 2: Branch exists
   → Execute: git checkout [branch-name]
   → Confirm: "✓ Switched to existing branch '[branch-name]'"

   # Scenario 3: Use current branch
   → Confirm: "✓ Continuing with current branch '[branch-name]'"
   ```

## 🤖 Multi-Role Collaboration Guidelines

### Workflow

1. **Before Creating Project**: Configure roles (`claude-multi-woker/cmw.config`)
2. **Start Collaboration**: Run `python run.py` in WezTerm
3. **Assign Tasks**: Coordinate through coordinator role
4. **Role Communication**: Use `python send <role> "message"`
5. **Complete Audit**: Conduct multi-dimensional audit after project completion

### Quick Commands

```bash
# Start multi-instance
cd claude-multi-woker && python run.py

# Role communication
python send coordinator "Task completed"
python send architect "Need architecture design"
```

## 📂 Project Structure

```
Project Root/                      # ← Current directory (your actual project)
├── CLAUDE.md                      # This file, auto-read
├── README.md                      # Vibe Coding guidelines (required reading)
├── MULTI_WORKER_RULES.md          # Multi-role collaboration guidelines (required)
├── claude-multi-woker/            # ⚡ Multi-instance collaboration toolkit (tool directory)
│   └── run.py                     # ← Start multi-role system from here
│   └── send                       # ← Role communication tool
└── .claude/
    ├── COORDINATOR_INSTRUCTIONS.md # Coordinator-specific instructions
    └── ALL_ROLES_INSTRUCTIONS.md   # All roles (except coordinator) instructions
```

## ⚡ Quick Checklist

Before starting any development task, confirm:

- [ ] Read core principles in `README.md`
- [ ] Read collaboration workflow in `MULTI_WORKER_RULES.md`
- [ ] Confirm if multi-role collaboration is needed
- [ ] Understand project architecture and design documents
- [ ] Prepared modular code structure

---

**Last Updated**: January 30, 2026  
**Version**: v1.1
