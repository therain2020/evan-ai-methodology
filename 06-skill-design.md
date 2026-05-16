# 06 — Skill Design

## When to Build a Custom Skill

A custom skill is warranted when:

1. **The workflow is repeated across sessions.** If you find yourself giving the same preamble instructions every time you touch a certain kind of file, that's a skill.
2. **The workflow has domain-specific constraints.** Thesis editing has rules that don't apply to general writing. Module auditing has phases that don't apply to code review.
3. **The workflow orchestrates multiple tools.** A skill that chains Grep → MySQL → Agent → Write is worth codifying.

Counter-indications:
- **One-off tasks** — not worth the skill overhead
- **Tasks already covered by gstack** — don't duplicate `/review` or `/investigate`
- **Simple preferences** — use CLAUDE.md behavioral rules, not a skill

## Anatomy of a Good Skill

```
SKILL.md
├── Frontmatter (YAML)
│   ├── name: unique identifier
│   ├── description: one sentence for the skill catalog
│   ├── allowed-tools: explicit whitelist
│   └── triggers: keywords that auto-suggest this skill
├── Workflow (Markdown)
│   ├── Phased structure (Phase 1 → 2 → 3)
│   ├── Executable commands, not abstract principles
│   ├── Decision points with clear criteria
│   └── STOP conditions (when to escalate)
└── Output specification
    ├── Report format (if applicable)
    ├── File naming convention
    └── Completion status protocol
```

## Design Decision: `/module-audit`

The `/module-audit` skill went through several iterations. Key design decisions:

### Why 3 Phases Instead of a Checklist?

A flat checklist (50 items) is overwhelming and hard to track. Three phases create natural pause points where the user can review findings and decide whether to continue.

### Why a Health Score?

A numeric score (0-10) weighted by severity gives a quick gut-check. It's not scientific, but it answers "should I be worried about this module?" faster than reading 20 findings.

Formula: `max(0, 10 - (Critical×3 + High×2 + Medium×1) / maxFiles)`

Critical bugs cost 3 points, High cost 2, Medium cost 1. Normalized by module size so a 5-file module and a 30-file module are comparable.

### Why Incremental Audit (Phase 4)?

The first audit of a module is expensive — you're building the mental model from scratch. Subsequent audits should be cheaper because most of the module hasn't changed. The incremental phase reads the prior report, marks fixed items, and only deeply investigates what's new.

### Why AskUserQuestion Gates?

Two mandatory decision points:
1. **Phase 0:** Does the user have reference docs (thesis, design specs, API docs)? If yes, extract design constraints and use them as the audit baseline. Code-doc mismatches become findings.
2. **Post-report:** Two options — A) Execute fixes immediately (Critical→High→Medium), or B) Self-review the report first to catch missing checks before fixing.

These prevent the audit from being a fire-and-forget report that nobody acts on.

## Skill Triggers

Good triggers are:
- **Chinese + English variants** — your team uses both
- **Specific enough to avoid false positives** — "module audit" not "audit"
- **Conversational** — what would you actually type when you want this?

Example from `/module-audit`:
```yaml
triggers:
  - module audit
  - 模块审计
  - 模块分析
  - 深度分析
  - 功能审查
  - 设计检查
  - audit module
```

## The gstack Ecosystem Boundary

Evan's custom skills coexist with gstack skills. The boundary:

| Domain | Use gstack | Use custom |
|--------|-----------|------------|
| Code review | `/review`, `/simplify` | `/module-audit` (deeper, persistent) |
| Debugging | `/investigate` | — |
| Design | `/design-consultation` | — |
| Thesis | — | `/thesis-editor` |
| Documents | `/make-pdf` | `/docx-editor`, `/pdf-reader` |

Custom skills handle workflows where gstack doesn't have an equivalent, or where domain-specific constraints (thesis formatting, Chinese academic conventions) require specialized handling.
