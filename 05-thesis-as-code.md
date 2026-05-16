# 05 — Thesis as Code

## Why Treat a Thesis Like a Software Project

A graduation thesis in software engineering has more in common with a large codebase than with a traditional essay:

- **Version control matters.** You will iterate 50+ times. `git` tracks every change.
- **Consistency is structural.** 80 figures, 30 tables, 60 citations — manual formatting guarantees drift.
- **AI is a force multiplier.** Generating diagrams, auditing citations, checking cross-references — all automatable.
- **The final artifact is a build.** Like a binary, the `.docx` is generated from source (outline + diagrams + citations).

## The Workflow

### Paragraph Replacement Over Character Editing

Never ask the AI to "change the second sentence of paragraph 47." This is fragile — paragraph content shifts with every edit.

Instead:
1. Locate the paragraph by index in the docx
2. Have the AI output the complete replacement paragraph
3. A Python script swaps the paragraph at that index

This is the equivalent of "replace the whole function, don't patch lines."

### Diagram Generation Pipeline

```
What kind of diagram?
├── Sequence / timing → PlantUML (SVG via MCP)
│   - Auto-validated syntax
│   - `<style>` blocks over skinparam
├── Flowchart / Gantt → Mermaid (SVG via MCP)
│   - Simpler syntax for flow-oriented diagrams
├── Architecture / ER / Class → DrawIO (.drawio → PNG/SVG)
│   - `<mxfile>` format with auto-routing
│   - Captions: no "图X-X" prefix
└── Data model (CDM/PDM) → PlantUML
```

**Rule:** Diagram captions never start with "图X-X" (the thesis template handles numbering). Never append the diagram type as a suffix (e.g., don't write "系统架构图" — the figure is self-evident).

### Citation Management

Two Python scripts automate reference integrity:
- `sentence_citations.py` — inserts sentence-level `[N]` markers
- `audit_citations.py` — cross-checks: are all listed references actually cited? Are all citations in the reference list?

Run `audit_citations.py` before every draft submission. A missing reference is an automatic deduction.

### AI Voice Removal

Chinese academic writing has specific expectations that AI-generated text violates:
- No exaggerated symbolism ("这座知识的殿堂")
- No promotional language ("极大地提升了")
- No shallow -ing endings ("具有重要意义")
- No dash overuse (AI loves em dashes)
- No rule-of-three patterns (AI structures everything in threes)

The `/humanizer-zh` skill applies these filters. Run it on every chapter before submission.

## Integration with `/thesis-editor`

The `/thesis-editor` skill auto-loads whenever a thesis file is touched. It enforces:
- Paragraph replacement protocol
- Diagram tool selection rules
- Citation format constraints
- Voice/style rules

This means the AI never needs to be reminded of thesis-specific rules — they're loaded automatically.
