# Feature Deep-Dive Skill

A Claude Code / Copilot Agent skill that produces a **single, focused, code-grounded analysis report of one named feature** inside a real codebase — not a whole-repo audit, not a generic template. Point it at a project and a feature name (e.g. "Authentication," "Roles and Permissions," "Task Assignment") and it traces the feature through the actual code, then writes a structured markdown report.

---

## Why this exists

Understanding how a feature *actually* works — across middleware, guards, services, and routes — usually means manually hopping between files, cross-referencing decorators, and guessing at intent. This skill does that legwork: it locates every file implementing the feature, traces the real call path end to end, and writes findings that cite actual files, functions, and routes — flagging anything it can't confirm instead of inventing it.

---

## Core principles

- **Ground everything in real code.** Every claim references an actual file path, function, route, or table name. Nothing is guessed.
- **Scoped to one feature.** Only the named feature and its direct dependencies — no unrelated modules pulled in.
- **Says what it can't find.** Unconfirmed or missing pieces are marked `Not Found in Codebase` or `Not Applicable — [reason]`, never silently skipped or fabricated.
- **Flags risk as it goes.** Missing checks, bypassable routes, or enum/logic mismatches are called out explicitly, not buried in the happy path.
- **Verifies full enforcement coverage.** For anything auth-related, it traces every layer — middleware, global guards, decorator metadata — and lists excluded/bypass paths by name.
- **Catches implementation gaps.** If a permission or feature flag exists in an enum/exception but isn't actually enforced in the logic, that's flagged as a concrete gap, not glossed over.
- **More than the happy path.** Every flow covers at minimum: happy path, failure path, and one edge case.

---

## What it produces

A single markdown report, `<feature-name>-analysis.md`, with this exact structure:

1. Feature Overview
2. Feature Purpose (with `**Assumption:**` labels for anything inferred)
3. User Flow — happy/alt/failure paths + Mermaid diagram (or `Not Applicable` for internal-only features)
4. Backend/System Flow — real call path (route → middleware → service → DB → response) + Mermaid sequence diagram
5. API Endpoints — table: Method | Path | Purpose | Auth Required | Role/Permission Required
6. Database/Data Model — tables, key fields, relationships, unclear fields flagged as Open Questions
7. Business Rules — enforced-in-code rules only, with gaps between intended vs. actual enforcement called out
8. Edge Cases — trigger, current code behavior (cited), and a correct/missing/risky verdict
9. Security & Access Control Notes — what's actually protected vs. not; this is where the sharpest findings usually land
10. Test Cases — table: Test ID | Type (Positive/Negative/Boundary/Security) | Scenario | Expected Result
11. Risks & Recommended Improvements — specific, prioritized, tied directly to gaps found above
12. Open Questions — anything unconfirmable from code alone, phrased for a dev/PM to answer
13. Files Analyzed — every file actually read, plus any expected-but-missing pieces (e.g. no test file found)

See [`roles-and-permissions-analysis.md`](./roles-and-permissions-analysis.md) for a full worked example — a real analysis of the Postiz backend's auth/permissions system, including concrete findings like a subscription-key bypass gap and an unenforced `Sections.VIDEOS_PER_MONTH` permission.

---

## How it works

1. **Step 1 — Identify scope.** Search the codebase for route/controller, service, repository, schema, and middleware/guard files matching the feature name and its actions. Track every file found for the final "Files Analyzed" section.
2. **Step 2 — Generate the report.** Fill all 13 sections in order. Sections that don't apply are still written out as `Not Applicable — [reason]`, never omitted.
3. **Step 3 — Format.** Clean markdown, consistent headings, tables for anything tabular, Mermaid diagrams for at least User Flow and Backend Flow. Depth comes from specificity, not padding.
4. **Step 4 — Output.** Saves automatically as `<feature-name>-analysis.md` in the project root (or a location the user specifies) — no extra confirmation needed before writing. For large codebases, it works in passes: search → read relevant files → write, rather than trying to load everything at once.

---

## Usage

Trigger it with prompts like:

- "Analyze the Authentication feature in this repo"
- "Break down how Roles and Permissions works"
- "Document the Task Assignment flow"
- "Review the file upload feature for gaps"

Works on any project open in the workspace, in any language/framework — the skill adapts to what it actually finds rather than assuming a stack.

---

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill definition — scope-identification step, 13-section report spec, formatting rules, output behavior |
| `roles-and-permissions-analysis.md` | Example output: full deep-dive on a real auth/permissions feature, including cited security gaps |
