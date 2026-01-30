# Multi-Role Collaboration Quick Reference

> **Quick Guide**: Most Critical Rules and Commands

**Language**: [English](QUICK_REFERENCE.md) | [中文](docs/cn/QUICK_REFERENCE.md)

---

## 🚨 Most Important Rules (Must Follow)

### 1. Mandatory Testing Workflow ⭐⭐⭐

```
❌ Forbidden: coder directly sends to test
❌ Forbidden: coder marks task as complete on their own
✅ Correct: coder → coordinator → test → coordinator → complete

Workflow:
① coder completes → send coordinator "Task X complete, please arrange testing"
② coordinator → send test "Please test Task X..." + update status to "🔄 Testing"
③ test completes → send coordinator "Test result: [pass/fail]"
④ coordinator decides → if passed, mark as "✅ Complete"
```

### 2. Coordinator Responsibilities

```
✅ Your role: Task allocation, progress tracking, quality control
❌ You don't: Write code, create files

When receiving coder completion notice, you must:
1. Update status to "🔄 Testing"
2. Immediately (within 5 minutes) assign test validation
3. Only mark as "✅ Complete" after test passes
```

### 3. "Continue" Command Handling

```
When user says "continue", immediately:
1. Read TASK_PROGRESS.md
2. Check if exists:
   - Exists → Restore project status, report current progress
   - Not exists → Start new project workflow
3. Continue work based on status
```

---

## 📋 Task Status Diagram

```
⏳ Pending
    ↓ (assign task)
🔄 In Progress
    ↓ (complete development)
🔄 Testing ⭐ (coder tasks only)
    ↓ (test validation)
    ├─ Pass → ✅ Complete
    └─ Fail → 🔄 In Progress (fixing)
```

---

## 💬 Standard Command Templates

### Coordinator Assigns Tasks

```bash
# Assign to architect
python send architect "Task: Design system architecture

Project: [project name]
Requirements: [specific requirements]

Please report back when complete."

# Assign to coder
python send coder "Task: Implement login feature

Background: architect has completed design
Requirements: [specific requirements]
Docs: see memory-bank/architecture.md

Please notify me when complete to arrange testing."

# Assign to test (after receiving coder completion notice)
python send test "Task: Validate login feature

Development complete, please verify:
- Functionality works correctly
- Code quality
- Any bugs

Report test results to me when done."
```

### Role Completion Reports

```bash
# Architect completes
python send coordinator "Task 1 complete: System architecture design
Output: memory-bank/architecture.md
Please assign next task."

# Coder completes
python send coordinator "Task 2 complete: Login feature implementation
Output: src/auth/login.py, src/auth/utils.py
Please arrange test validation."

# Test completes
python send coordinator "Task 2 testing complete
Result: ✅ Passed
Coverage: 95%
Recommend marking as complete."
```

---

## 🔄 Coordinator Response Templates

### Receiving Coder Completion Notice

```bash
# Step 1: Acknowledge receipt
"Received coder completion notice, verifying..."

# Step 2: Check files
"Output file verification: ✅"

# Step 3: Update status
"Update TASK_PROGRESS.md: Task 2 → 🔄 Testing"

# Step 4: Assign test
python send test "Task: Validate Task 2

Coder has completed development, please verify:
- [specific validation requirements]

Report back when done."

# Step 5: Confirm to coder
python send coder "Acknowledged! Task 2 marked as testing.
Test has been assigned for validation."
```

### Receiving Test Validation Results

```bash
# If passed
"Received test validation result: ✅ Passed"
"Update TASK_PROGRESS.md: Task 2 → ✅ Complete"

python send coder "Task 2 has passed validation, marked complete."
python send test "Thanks for validation, Task 2 marked complete."

# If failed
"Received test validation result: ❌ Issues found"
"Update TASK_PROGRESS.md: Task 2 → 🔄 In Progress (fixing)"

python send coder "Task 2 validation found issues:
[list issues]
Please fix and notify me again."
```

---

## 📊 TASK_PROGRESS.md Update Timing

```
Must update when:
✅ Assigning task: ⏳ Pending → 🔄 In Progress
✅ Coder completes: 🔄 In Progress → 🔄 Testing
✅ Test passes: 🔄 Testing → ✅ Complete
✅ Test fails: 🔄 Testing → 🔄 In Progress
✅ Blocked: 🔄 In Progress → ⏸️ Blocked
```

---

## 🚫 Common Mistakes

| Mistake | Correct Approach |
|---------|------------------|
| ❌ Coordinator writes code themselves | ✅ Use send to assign to coder |
| ❌ Coder directly sends to test | ✅ Must go through coordinator |
| ❌ Coder marks task complete themselves | ✅ Notify coordinator to arrange testing |
| ❌ Coordinator forgets to assign test | ✅ Immediately assign after receiving coder notice |
| ❌ Resume without reading TASK_PROGRESS.md | ✅ Read file first then continue work |

---

## 🎯 Success Checklist

### Coordinator Check Before Each Response

- [ ] Read latest TASK_PROGRESS.md
- [ ] Confirmed current task status
- [ ] If received coder completion notice, ready to assign test
- [ ] Have standard response template ready
- [ ] Confirm won't write code myself

### Coordinator Check Before Assigning Test

- [ ] Coder has confirmed development complete
- [ ] Output files verified to exist
- [ ] TASK_PROGRESS.md updated to "🔄 Testing"
- [ ] Test role online and available
- [ ] Detailed validation requirements prepared

---

## 🔧 Fault Recovery

### When Context Lost

```
1. Read TASK_PROGRESS.md
2. Check currently in-progress tasks
3. Read memory-bank/ related docs
4. Report recovery status to user
5. Continue coordination work
```

### If Task Not Tested

```
If find coder task marked "✅ Complete" but not tested:

1. Immediately update status to "🔄 Testing"
2. Assign test validation
3. Wait for validation to pass before marking complete
4. Log this issue in TASK_PROGRESS.md notes
```

---

## 📞 Quick Commands

```bash
# View role list
ls claude-multi-woker/.cmw_config/

# Send messages
python send coordinator "message"
python send coder "message"
python send test "message"
python send architect "message"

# Use abbreviations
python send c1 "message"  # First instance
python send c2 "message"  # Second instance
```

---

**Remember**: You are coordinator, your responsibility is **scheduling**, not **execution**.

**Core Rule**: After coder completes → must go through you → assign to test → mark complete only after validation passes.
