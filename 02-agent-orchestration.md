# 02 — Agent Orchestration

## One Brain vs. Many

Claude Code supports launching sub-agents — independent AI instances that work on separate tasks in parallel. The question is not *can you* but *when should you*.

### When to Use a Single Agent

Most tasks are single-agent tasks. One agent with full context produces better results than two agents with partial context. Use one agent when:
- The task has sequential dependencies (step B needs step A's output)
- The codebase is unfamiliar and needs exploration
- The change touches files that reference each other

### When to Use Multiple Agents

Multi-agent makes sense when:
- Tasks are truly independent (different modules, no shared state)
- Each task requires deep context that would overflow a single context window
- You need competing approaches evaluated side-by-side

## The 5-Step Protocol

Evan's rule for multi-agent tasks — never launch directly:

### Step 1: Confirm the Concurrency Plan
```
Task 1: Audit User module (3 phases)
Task 2: Audit Book module (3 phases)
Task 3: Audit AI module (3 phases)
All independent → launch in parallel
Agent 1 → Task 1, Agent 2 → Task 2, Agent 3 → Task 3
```

### Step 2: Confirm Constraints
Each sub-agent gets a constraint list:
- "Do NOT modify code. Read-only audit."
- "Do NOT access tables outside your module."
- "Do NOT expand scope beyond the specified module."

### Step 3: Confirm Acceptance Criteria
Must be verifiable, not subjective:
- ❌ "Make the code better"
- ✅ "Find all `${}` in MyBatis XML, report file:line for each"
- ❌ "Write good tests"
- ✅ "Cover happy path + 2 error paths + 1 boundary case, all must pass"

### Step 4: Wait for Explicit Confirmation
The user must say "确认，开始执行" before any sub-agent launches. This gate prevents premature execution of an un-reviewed plan.

### Step 5: Review Reports
- Pass → mark complete
- Fail → provide specific feedback, re-launch
- Same sub-task fails 3 times → escalate to user (the problem is likely structural, not execution)

## Sub-Agent Prompt Design

A good sub-agent prompt is self-contained — the agent has no context from the parent conversation. Structure:

```
## Goal
<one sentence>

## Files to Read
- path/to/file1.java (read lines 50-120)
- path/to/file2.xml (read entire file)

## Operations to Perform
1. Search for <pattern> in <directory>
2. Report each finding with file:line

## Constraints
- Do NOT modify any files
- Do NOT search outside <directory>
- Report findings only, do not suggest fixes

## Deliverable
A list of findings with: FILE:LINE | SEVERITY | DESCRIPTION | SUGGESTED FIX
```

## Context Budget

Sub-agents have a context window limit. The prompt should budget for:
- System instructions: ~2K tokens
- Your prompt: ~1-3K tokens
- File reads: the rest (varies by task)

If you need the sub-agent to read 10 files, keep the prompt tight. If the prompt is long, limit file reads to the essential 3-4.

## The 3-Strike Rule

If the same sub-agent fails the same task 3 times, stop retrying. The problem is not execution quality — it's either:
1. The task is underspecified (the acceptance criteria are ambiguous)
2. The task is too large for one agent (needs splitting)
3. The codebase has architectural issues that prevent a clean solution

In all three cases, escalating to the human is the right call. More retries won't help.
