---
name: AI Agent Task
about: Task assignment for AI agents (Copilot, Claude, Codex, Gemini)
title: '[AGENT] Task Name'
labels: ai-task
assignees: ''
---

## 🎯 Task Assignment

**AI Agent:** [Copilot/Claude/Codex/Gemini]  
**Priority:** [🔴 High / 🟡 Medium / 🟢 Low]  
**Estimated Time:** [1-3 hours / 1-2 days / 1 week]  
**Module:** [domain/data/transport/protocol/features/ui]

---

## 📋 Task Description

[Clear description of what needs to be implemented]

---

## 📚 Documentation References

**Must Read:**
- [ ] `docs/INTERFACE_CONTRACTS.md` (section X)
- [ ] `docs/IMPLEMENTATION_EXAMPLES.md` (section Y)
- [ ] `docs/MODULAR_ARCHITECTURE_GUIDE.md` (if needed)

**Task Assignment File:**
- [ ] `.github/tasks/[AGENT]_TASKS.md` Task #N

---

## ✅ Acceptance Criteria

**Functional:**
- [ ] Feature works as expected
- [ ] All interface methods implemented
- [ ] Error handling in place

**Code Quality:**
- [ ] KDoc comments for public API
- [ ] Follows project conventions
- [ ] No compiler warnings

**Testing:**
- [ ] Unit tests written (or Issue created for Gemini)
- [ ] Tests pass
- [ ] Coverage > 70%

**Documentation:**
- [ ] README updated (if needed)
- [ ] Examples added (if complex feature)

---

## 📄 Files to Create/Modify

**Create:**
- `path/to/new/File1.kt`
- `path/to/new/File2.kt`

**Modify:**
- `path/to/existing/File3.kt` (add method X)

---

## 🔗 Dependencies

**Blocked by:**
- [ ] #IssueNumber - Description

**Blocks:**
- [ ] #IssueNumber - Description

---

## 💡 Implementation Notes

[Any specific hints, gotchas, or requirements]

**Example:**
```kotlin
// Expected interface to implement:
interface DtcRepository {
    suspend fun getDtcCodes(vehicleId: String): List<DtcCode>
}
```

---

## 🧪 Testing Instructions

**How to test:**
1. [Step 1]
2. [Step 2]
3. Expected result: [X]

**Test data:**
```kotlin
val testData = ...
```

---

## 📞 Need Help?

**Architecture questions:** Tag `@cursor`  
**Build issues:** Tag `@claude`  
**Domain clarifications:** Tag `@copilot`  
**Protocol questions:** Tag `@codex`

---

## ✅ When Complete

- [ ] Create PR with title: `[Module] Task description`
- [ ] Tag `@cursor` for review
- [ ] Update PROGRESS_TRACKER.md
- [ ] Move Issue to "Review" column
