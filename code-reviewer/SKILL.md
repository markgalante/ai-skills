---
name: code-reviewer
description: Smart code review router. Analyzes the diff to detect file types (JS/TS, Python, CI/CD, Docker), delegates to specialist reviewers, and guides an interactive review session. Supports author mode (one issue at a time) and reviewer mode (all at once).
compatibility: Requires git access, file system access
---

## Step 1: Determine Mode

If the user's prompt clearly states they are reviewing their own work or someone else's, infer the mode. Otherwise, ask:

> "Are you reviewing your own work (**author mode**) or someone else's (**reviewer mode**)?"

- **Author mode**: issues are presented one at a time for discussion
- **Reviewer mode**: all findings are presented at once, grouped by severity

## Step 2: Get the Diff

If the user has already provided a diff or pasted code directly, use it as-is and skip the git commands.

Otherwise, infer the scope from the user's prompt:

| Scope | Command |
|-------|---------|
| Branch (default — nothing specified) | `git remote show origin \| grep 'HEAD branch' \| awk '{print $NF}'` to get the default branch, then `git diff <default-branch>..<current-branch>` |
| Specific file | `git diff <default-branch>..<current-branch> -- <path/to/file>` |
| Single commit | `git show <sha>` |
| Commit range | `git diff <sha1>..<sha2>` |
| Tag | `git diff <tag>..HEAD` |
| Selected lines | `git diff <default-branch>..<current-branch> -- <path/to/file>`, then scope the analysis to the specified lines only |

If the scope is ambiguous, default to the full branch diff and note the assumption.

## Step 3: Detect File Types and Load References

Scan the diff to identify which file types are present, 
then load only the matching reference checklists — in parallel if multiple apply.

[references/general.md](./references/general.md) is **always loaded** for every review. Specialist references are loaded on top based on file types:

| Files | Reference |
|-------|-----------|
| `.py`, `.pyw`, `.pyi` | [references/python.md](./references/python.md) |
| `.js`, `.jsx`, `.ts`, `.tsx`, `.mjs`, `.mts` | [references/typescript-javascript.md](./references/typescript-javascript.md) |
| `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Dockerfile`, `docker-compose.yml` | [references/ci-cd.md](./references/ci-cd.md) |

If no specialist reference matches, the general reference still applies — note the gap and proceed.

## Step 4: Analyze

### Severity levels
- 🔴 **Critical** — Security breaches, data loss, runtime errors, significant performance degradation
- 🟠 **Important** — Memory leaks, architectural flaws, moderate performance issues, technical debt
- 🟡 **Suggestion** — Optimizations, pattern improvements, code clarity
- 🔵 **Nit-pick** — Minor inconsistencies, style edge cases, missing docs

Review the diff against each loaded checklist. Collect all findings. For each finding record:

- **ID** (sequential: 1, 2, 3…)
- **Severity** (from the levels above)
- **File + line reference**
- **Description** of the issue and why it matters

Before flagging anything, check whether the project has linting/formatting tooling (`.eslintrc`, `ruff.toml`, `pyproject.toml`, `.flake8`, etc.) and skip issues that would be caught automatically.

Also check for consistency with the rest of the codebase (naming conventions, patterns, style).

## Step 5: Present Summary

Before diving into issues, always present a summary followed by the **initial tracker** — all issues listed with `⏳ Pending`:

```
## Review Summary

🔴 Critical    — N issues
🟠 Important   — N issues
🟡 Suggestion  — N issues
🔵 Nit-pick    — N issues

<!-- tracker -->
| # | Severity | Issue | Resolution |
|---|----------|-------|------------|
| 1 | 🔴       | Short description | ⏳ Pending |
| 2 | 🟠       | Short description | ⏳ Pending |
| 3 | 🟡       | Short description | ⏳ Pending |
```

This is the canonical starting state for the tracker. Every subsequent update copies the most recently emitted tracker and changes only the resolved row — never reconstruct from memory.

---

## Step 6: Review Session

### Reviewer Mode

Present all findings at once, grouped by severity (critical first). No interactive loop required.

---

### Author Mode

Before the first issue, tell the author:

> "We'll go one issue at a time, most severe first. You can use `/all` to see everything at once, `/list` for a quick overview without resolving anything, or `/summary` to wrap up early."

Then work through issues **one at a time**, from most to least severe (🔴 → 🟠 → 🟡 → 🔵).

For each issue:

1. Present the finding: severity, file/line, description
2. Wait for the author's response. They may:

| Response | What to do |
|----------|------------|
| Wants to discuss | Engage until you reach mutual understanding. Then ask: "Shall we make the change now, or log it for later?" |
| Agrees, will fix it | Mark as **change to be made** and move on |
| Pushes back | Hear them out. If their reasoning holds, mark as **dismissed**. If you still disagree, mark as **acknowledged — no action** and note the disagreement. |
| "I'll do this in a separate task" | Note it for the final summary and move on |
| Skip | Mark as **skipped** and move on |

3. After each resolution, copy the most recently emitted tracker, update the resolved row, and emit the full table — every row, including remaining `⏳ Pending` ones. Then say "_(N remaining)_ Ready for the next one?" and continue.

#### Resolution tracker — emoji legend

| Emoji | Meaning |
|-------|---------|
| ⏳ | Pending — not yet reached |
| ✅ | Change made |
| 📋 | Ticket / follow-up task logged |
| 🤝 | Discussed — no action |
| ❌ | Dismissed |
| ⏭️ | Skipped |

#### Escape hatches

At any point during the loop, the author may:

| Command | What to do |
|---------|------------|
| `/all` | Dump all remaining unresolved issues at once (grouped by severity), then proceed to the final summary |
| `/summary` | Skip straight to the final summary — mark all unresolved issues as **skipped** |
| `/list` | Print a compact numbered list of all remaining issues (severity + one-line description) without resolving any, then resume the loop from where you left off |

Recognise natural-language equivalents ("show me everything", "just give me the full list", "let's wrap up") and map them to the appropriate escape hatch.

---

## Step 7: Final Summary

Emit the final tracker by copying the most recently emitted tracker and promoting any remaining `⏳ Pending` entries to `⏭️ Skipped`. Every row must appear.

If any tickets were logged, offer to create them (via Notion, Jira, or another tool if available).

---

## Notes

- Follow community best practices for each language/framework
