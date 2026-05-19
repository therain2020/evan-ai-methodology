---
name: update-project
description: Automated update pipeline for evan-ai-methodology — audit chapters, fix issues, sync bilingual READMEs, commit and push.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - PowerShell
  - Agent
triggers:
  - update project
  - update the project
  - refresh project
  - project update
  - sync project
---

# Update Project

Automated update pipeline for `evan-ai-methodology`. One command to audit all chapters, fix issues, sync bilingual READMEs, and ship.

## When to use

Run `/update-project` when:
- You've learned something new and want to update the methodology
- Tooling or skills referenced in chapters have changed
- Periodic maintenance — catch staleness, inconsistency, AI writing traces
- After adding or removing a chapter

## Pipeline

Run these phases in order. Report progress at each phase boundary. If a phase finds nothing to fix, say so before moving on.

### Phase 0: Pre-flight

```
git status
git log --oneline -3
```

If the working tree is dirty, warn and ask whether to proceed.

### Phase 1: Chapter Audit

Read every chapter file (`01-builders-mindset.md` through `08-mcp-as-infrastructure.md`). The audit can use parallel sub-agents to speed up review. For each chapter, check:

1. **Broken links** — internal links to other chapters, external links to evan-ai-setup or tools.
2. **Terminology drift** — the same concept named differently across chapters. Standard terms: "agent / sub-agent", "skill / custom skill", "gstack", "MCP server", "degradation", "review pipeline".
3. **Stale claims** — references to specific model versions, dates that have passed, or tool features that have changed.
4. **AI writing traces** — apply the humanizer checklist:
   - Three-item lists → two or four
   - Negative-parallelism patterns → simplify ("不仅...而且...", "not only...but also...")
   - AI filler words: 此外, 至关重要, 强调了, 标志着, 见证了, 彰显了
   - Em dash overuse (>3 per section)
   - Filler phrases: 值得注意的是, 所以更准确地说
   - Bold+colon list patterns → natural paragraphs
5. **Missing cross-references** — if Chapter A explains something Chapter B needs, suggest linking.

Report findings grouped by chapter: issue type, location, suggested fix.

### Phase 2: README Sync

Read both `README.md` and `README_zh.md`:

1. Verify the chapter table has all 8 chapters with correct links.
2. Verify each one-liner matches the chapter's actual content (spot-check the opening paragraph).
3. Both READMEs must have identical structure and link targets. Mirror any section that exists in only one.
4. Language switcher links: `README.md` → `README_zh.md`, `README_zh.md` → `README.md`.
5. Companion repo link (`evan-ai-setup`) is present and correct in both.
6. Humanizer polish: no AI traces, no offensive language, natural tone per language.

### Phase 3: Apply Fixes

For each finding:
- Use `Edit` for targeted changes. Provide enough context in `old_string` for a unique match.
- Use `Write` only for full rewrites.
- Skip false positives — note them and move on.

### Phase 4: Pre-commit Review

```
git diff --stat
```

Review the full diff (`git diff`) for sensitive information:
- No IP addresses, credentials, API keys, tokens
- No personal information beyond git author
- No internal URLs or unredacted env var values

### Phase 5: Commit and Push

Stage changed files by name (never `git add -A`):

```
git add <file1> <file2> ...
git commit -m "docs: project update — <brief summary>"
git push
```

### Phase 6: Summary

Report chapters audited, issues found and fixed by category, README sync status, commit hash, and push confirmation.
