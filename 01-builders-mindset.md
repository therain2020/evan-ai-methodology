# 01 — The Builder's Mindset

## What Changes When AI Makes Marginal Cost Near Zero

Traditional software engineering is shaped by scarcity: developer time is the bottleneck. You prioritize. You cut scope. You ship the MVP and iterate later. Every line of code carries an opportunity cost.

AI-assisted development flips this. The marginal cost of producing a code review, a test suite, a design document, or a refactor approaches zero. This doesn't mean AI does everything — it means the *decision* of whether to do something no longer depends on human throughput.

### The Boil-the-Lake Principle

In traditional development, you boil a pot — just enough water for the task at hand. In AI-assisted development, you boil the lake — you do the complete thing because the cost of completeness is now negligible.

Examples:
- Instead of reviewing just the diff, audit the entire module
- Instead of fixing the reported bug, trace every code path that touches the same state
- Instead of writing a test for the happy path, write tests for every edge case you can name

The constraint shifts from "how much can I produce" to "how well can I specify what I want."

## The AI Is Not a Junior Developer

Treating AI as a junior developer who needs detailed instructions is the wrong mental model. A junior developer has common sense but limited knowledge. An AI has encyclopedic knowledge but zero common sense.

This means:
- **Over-specify constraints, not steps.** Tell the AI what it CANNOT do, not every step of what it SHOULD do.
- **Verify, don't trust.** AI will produce plausible-looking wrong answers. Your job is to design verification gates.
- **Context is currency.** The quality of AI output is proportional to the quality of context you feed it. This is why CLAUDE.md and memory systems matter.

## The Role of CLAUDE.md

CLAUDE.md is not documentation. It is **context compression** — the minimum information the AI needs to be useful in your codebase without you repeating yourself every session.

A good CLAUDE.md answers, before the AI asks:
- What are we building?
- What technologies are we using?
- How do I run, test, and build?
- Where do things live?
- What are the rules I must never break?

The difference between a project with CLAUDE.md and one without is the difference between an AI that needs 3 rounds of clarification and one that produces a working PR on the first attempt.

## Communication as a Skill

The bottleneck in AI-assisted development is not the AI's capability — it's the human's ability to describe what they want. This is a learnable skill:

1. **Name the outcome, not the steps.** "Make the login flow secure" is vague. "Ensure failed login attempts are rate-limited to 5 per 15 minutes per IP" is actionable.
2. **Provide negative constraints.** "Don't add new dependencies" is often more valuable than "use library X."
3. **Show, don't tell.** Pointing the AI at existing code as a reference produces better results than describing the pattern in prose.
4. **Iterate in public.** Don't craft the perfect prompt in your head. Say what you want, see what you get, refine. The loop is seconds, not hours.
