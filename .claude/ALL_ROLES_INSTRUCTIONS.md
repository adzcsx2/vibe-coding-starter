**Language**: [English](.claude/ALL_ROLES_INSTRUCTIONS.md) | [中文](docs/cn/ALL_ROLES_INSTRUCTIONS.md)

---

# Essential Guide for All Roles (Except Coordinator)

> **Applicable to**: architect, coder, test, ui, docs, frontend, backend, and all future roles

## 🚨 Your Most Important Responsibility

As a role (architect/coder/test, etc.), you have an **unshirkable responsibility**:

### After completing a task, you MUST notify the coordinator!

---

## ⚠️ Why Is This Critical?

### Problem Scenario
```
T1: coordinator assigns task to architect
T2: architect starts working...
T3: architect completes task
T4: architect says nothing 😶
T5: coordinator keeps waiting 😓
T6: collaboration flow deadlocks 💀
```

### Correct Scenario
```
T1: coordinator assigns task to architect
T2: architect starts working...
T3: architect completes task
T4: architect sends completion notification ✅
T5: coordinator updates status, assigns next task ✅
T6: collaboration flows smoothly 🎉
```

---

## 📋 Mandatory Task Completion Process

### Step 1: Update TASK_PROGRESS.md

Before announcing completion, update the file:

```markdown
| ID | Task Description | Assigned To | Status | Assigned Time | Completed Time | Notes |
|----|-----------------|-------------|--------|---------------|----------------|-------|
| 1 | Your Task | Your Role | ✅ Completed | T1 | T_now | Output files created |
```

### Step 2: Notify Coordinator

**Must** use this standard template:

```bash
python send coordinator "I am [your role name]

✅ Task Completion Report:

Task ID: [Read from TASK_PROGRESS.md]
Task Description: [Brief description]
Completion Time: [Current time]

📁 Output Files:
- [Complete path to file 1]
- [Complete path to file 2]
- [Continue listing if more]

📝 Completion Notes:
[Brief explanation: what was done, how it was solved]

⚠️ Context Cleared: Yes/No (If no, explain why)
Waiting Status: Waiting for next task assignment

Please update TASK_PROGRESS.md and assign the next task."
```

### Step 3: Clean Context

After task completion, **must** clean context:

```bash
# Clear conversation
/clear

# When waiting for new task, restore context
"Please read TASK_PROGRESS.md. I am [role name], current status: waiting for new task"
```

---

## 🔄 Complete Workflow Example

### Example: architect completes architecture design

```bash
# T1: Receive task
python send coordinator "I am architect, received task, starting architecture design..."

# T2-T10: Working... (designing, writing documentation)

# T11: Task completed
# 11.1 Update TASK_PROGRESS.md
# 11.2 Notify coordinator
python send coordinator "I am architect

✅ Task Completion Report:

Task ID: 1
Task Description: Unity Project Architecture Design
Completion Time: 2026-01-30 14:30

📁 Output Files:
- memory-bank/architecture.md
- memory-bank/tech-stack.md

📝 Completion Notes:
Completed architecture design for 9 modules, using Singleton pattern and object pooling for performance optimization.
Architecture documentation includes complete class diagrams and module descriptions.

⚠️ Context Cleared: Yes
Waiting Status: Waiting for next task assignment

Please update TASK_PROGRESS.md and assign the next task."

# 11.3 Clean context
/clear
```

---

## 🚨 Special Situation Handling

### Situation 1: Task is Blocked

```bash
python send coordinator "I am [role name]

⚠️ Task Blocking Report:

Task ID: [Task ID]
Blocking Reason: [Detailed description]
Coordination Needed: [Whose help is needed]

Example:
Task ID: 3
Blocking Reason: Missing API documentation, cannot write test cases
Coordination Needed: architect or coder

Waiting for coordinator to coordinate resolution."
```

### Situation 2: Task Needs to be Split

```bash
python send coordinator "I am [role name]

📊 Task Split Suggestion:

Original Task: [Task ID]
Split Reason: [Why split is needed]

Suggested Split:
- Subtask A: [Description] - [Estimated time]
- Subtask B: [Description] - [Estimated time]

Please confirm and update TASK_PROGRESS.md."
```

### Situation 3: Need More Time

```bash
python send coordinator "I am [role name]

⏱️ Time Extension Request:

Task ID: [Task ID]
Original Estimate: [Time]
New Estimate: [Time]
Extension Reason: [Reason explanation]

Requesting approval for extension."
```

---

## ✅ Checklist

Before completing a task, confirm:

- [ ] TASK_PROGRESS.md updated (status → ✅ Completed)
- [ ] Output files created and saved
- [ ] Completion notification sent to coordinator
- [ ] Notification message used standard template
- [ ] Context cleaned (/clear)

---

## 🎯 Remember These Three Things

1. **After completing a task, must notify coordinator**
2. **Notification must use standard template**
3. **After notification, must clean context**

---

**Document Version**: v1.0
**Creation Date**: 2026-01-30
**Applicable Scope**: All roles (except coordinator)
