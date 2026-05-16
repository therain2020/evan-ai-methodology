# 04 — Debugging with AI

## The Iron Law

**No fixes without root cause investigation first.**

Fixing symptoms creates whack-a-mole debugging. Every fix that doesn't address root cause makes the next bug harder to find. AI makes it dangerously easy to generate plausible-looking fixes for symptoms. Don't.

## The 5-Phase Debugging Loop

### Phase 1: Root Cause Investigation

Before forming any hypothesis, gather evidence:

1. **Collect symptoms** — error messages, stack traces, reproduction steps
2. **Read the code** — trace the code path from symptom back to potential causes
3. **Check recent changes** — `git log --oneline -20 -- <affected-files>`. A regression means the root cause is in the diff
4. **Reproduce** — can you trigger the bug deterministically? If not, gather more evidence

At the end of Phase 1, you should have a specific, testable claim: "I believe the bug is in `file.java:line` because X."

### Phase 2: Pattern Analysis

Does this match a known pattern?

| Pattern | Signature | Where to Look |
|---------|-----------|---------------|
| Race condition | Intermittent, timing-dependent | Concurrent access to shared state |
| Null propagation | NullPointerException | Missing guards on optional values |
| State corruption | Inconsistent data, partial updates | Transactions, callbacks, hooks |
| Integration failure | Timeout, unexpected response | External API calls |
| Stale cache | Shows old data, fixes on clear | Redis, CDN, browser cache |

Also check: prior learnings from `/learn`, prior audit reports from `/module-audit` on the same files.

### Phase 3: Hypothesis Testing

Before writing ANY fix:
1. Add a temporary log or assertion at the suspected root cause
2. Run the reproduction scenario
3. Does the evidence match the hypothesis?

**3-Strike Rule:** If 3 hypotheses fail, STOP. The bug is likely architectural, not local. Escalate.

### Phase 4: Implementation

Once root cause is confirmed:
1. **Fix the root cause, not the symptom** — the smallest change that eliminates the problem
2. **Minimal diff** — fewest files, fewest lines. Resist refactoring adjacent code
3. **Write a regression test** that fails without the fix and passes with it
4. **Run the full test suite**

### Phase 5: Verification

- Reproduce the original bug scenario — confirm it's fixed
- Run the full test suite and paste output
- Output a structured debug report

## Why AI Changes Debugging

Traditional debugging is bottlenecked by the developer's ability to hold the codebase in their head. AI changes this:

**Faster Phase 1:** An AI agent can trace a code path across 20 files in seconds, finding all references to a method or variable that a human would need minutes to grep for.

**Better Phase 2:** AI can pattern-match against a catalog of known bug patterns more exhaustively than a human recalling from memory.

**Dangerous Phase 4:** AI will happily generate a fix for the symptom if you don't force it through Phase 3 first. The iron law exists because AI makes symptom-fixing too easy.

## The `/investigate` Skill

Evan's `/investigate` skill encodes this 5-phase methodology. It adds:
- Automatic git log checks for recent changes
- Prior learning search for related investigations
- Scope locking (`/freeze`) to prevent accidental changes to unrelated code
- Structured debug report output

Use it for any bug that takes more than 5 minutes to understand.
