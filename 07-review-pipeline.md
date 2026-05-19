# 07 — The Review Pipeline

## Layered Review: Surface to Deep Structure

A single "code review" pass is too shallow. Evan's pipeline reviews code in layers, each layer focusing on a different dimension:

```
/simplify  →  reuse + quality + efficiency (code-level)
   ↓
/review    →  SQL safety, trust boundaries, side effects (security-level)
   ↓
/health    →  type check + lint + test suite + composite score (correctness-level)
   ↓
/qa        →  browser testing + visual QA (user-level, if UI changes)
   ↓
/ship      →  version bump + commit + push + PR (delivery)
```

Each layer catches different categories of problems. A function that passes `/simplify` (clean code) might fail `/review` (SQL injection in a downstream mapper). A change that passes both might fail `/qa` (button misaligned at 320px width).

## Layer 1: `/simplify` — Code-Level Review

Three agents run in parallel on the diff:

1. **Reuse agent:** Finds duplicated logic, suggests existing utilities
2. **Quality agent:** Flags hacky patterns, nested conditionals, dead code
3. **Efficiency agent:** Checks N+1 queries, blocking calls, memory leaks

This is the fastest layer — pure static analysis, no external dependencies. Run it after every meaningful code change.

## Layer 2: `/review` — Security-Level Review

Focuses on structural risks unique to the project's stack:

- **MyBatis `#{}` vs `${}`:** `${}` is string concatenation — SQL injection vector. Every occurrence must be manually justified.
- **LLM trust boundaries:** Data flowing from LLM output to database queries, file paths, or HTML rendering must be sanitized.
- **Conditional side effects:** Methods that behave differently based on configuration, environment, or feature flags — these are the hardest bugs to catch in testing.

## Layer 3: `/health` — Correctness-Level Review

Runs the project's own quality tools and computes a weighted composite score (0-10):

- Type checker (if applicable)
- Linter
- Test suite (coverage + pass rate)
- Dead code detector
- Shell script linter

The score trends over time. A dropping score on the same module is an early warning.

## Layer 4: `/qa` — User-Level Review

Only for changes with UI impact. Uses the headless browser to:
- Navigate the changed pages
- Test the golden path
- Test edge cases (empty state, error state, loading state)
- Compare before/after screenshots
- Check responsive breakpoints

Three tiers: Quick (critical/high only), Standard (+ medium), Exhaustive (+ cosmetic).

## Layer 5: `/ship` — Delivery

The final gate before a PR:
1. Detect the base branch (usually `main` or `master`)
2. Run the full test suite one more time
3. Bump VERSION if configured
4. Update CHANGELOG
5. Commit with structured message
6. Push and create PR

## When to Run Each Layer

| Situation | Layers to Run |
|-----------|---------------|
| Single-line fix | `/simplify` only |
| New feature (no UI) | `/simplify` → `/review` → `/health` |
| New feature (with UI) | Full pipeline |
| Refactoring | `/simplify` → `/health` |
| Security-sensitive change | `/simplify` → `/review` → `/cso` (full security audit) |
| Before merging any PR | At minimum `/review` + `/health` |

## The `/dev-flow` Shortcut

`/dev-flow` runs the full pipeline — Phase 0 sync → TDD → simplify → review → PUSH → health → QA → ship — in sequence with continuous checkpoint WIP commits and pushes. Use it for feature work where you want maximum confidence before the PR goes up.

**Multi-device support:** Phase 0 pulls remote changes before starting. After stage 3 (review), a PUSH node pushes WIP commits so another device can take over the heavy stages (health compile, QA browser test, ship). Each PUSH marks the commit with `[handoff-desktop]` or `[handoff-mobile]` so the next device knows where to resume.
