# API Endpoint Report Skill

A Claude Code / Copilot Agent skill that generates a **professional, code-grounded API Analysis Report** for a real codebase — full endpoint inventory, per-endpoint detail (headers, validation, responses by status code), and platform-level sections (rate limiting, security, versioning, error codes, auth) — in a fixed, enterprise-documentation format.

This is a **full API surface scan and documentation report**, not a scored security audit (that's what `api-architecture-analysis` is for) and not a general requirement/test-scenario doc (that's `requirement-analysis`). Every fact must trace to actual route/controller/validator code — nothing is invented.

---

## Why this exists

API docs drift from reality fast — headers, validation rules, and status codes live scattered across route files, middleware, and schemas. This skill forces a full, disciplined scan of the *entire* route surface (not just the first module opened), classifies every endpoint as internal or external, and produces a single reference document where every header, validation rule, and error code is traceable back to a real line of code.

See [`api-analysis-report.md`](./api-analysis-report.md) for a full worked example — a 357-route-file scan of the Medusa v2.17.2 commerce backend (Admin/Store/Auth/Plugin APIs), covering auth mechanisms, validation, and per-endpoint detail at uniform depth.

---

## What it produces

A single markdown file, `api-analysis-report.md`, structured as:

- **Section 0 — Scan Manifest** *(required, always first)*: every route file/directory scanned, plus any explicitly excluded group and a real, codebase-specific reason why.
- **Section 1 — Overview**: API type, tech stack, categories, base URLs per environment, auth mechanisms, versioning strategy — as a table.
- **Section 2 — API Endpoint Inventory**: External and Internal tables, grouped by route group (`#### Admin`, `#### Store`, etc.) — Method | Endpoint | Purpose | Auth Required | Request Body.
- **Section 3 — Detailed Endpoint Specifications**: for every endpoint (or an explicitly stated high-priority subset) — Purpose & Data Operation (naming actual fields, not restated verbs), Required Headers, Request Validation, pagination, structural vs. data validation, and responses broken out by HTTP status code with sample JSON.
- **Section 4 — Rate Limiting & Response Headers**
- **Section 5 — Security Considerations**: auth boundaries, CORS policy, PII handling, mTLS/equivalent for internal APIs.
- **Section 6 — Versioning & Deprecation Policy**
- **Section 7 — Global Error Code Reference**: deduplicated across every endpoint in Section 3.
- **Section 8 — Authentication Summary**: mechanism, token/key location, expiry/rotation per API category.
- **Section 9 — Notes / Recommendations**: unverifiable items, excluded endpoints, concrete high-value gaps.

---

## How it works

**Step 1 — Discover the full API surface.** Identifies the tech stack, then enumerates *every* route directory/file before analyzing any single one — no starting-module bias. Classifies each endpoint External vs. Internal by auth mechanism, notes environment base URLs if config reveals them, and cross-checks that every category named in the Overview has matching rows in the Inventory.

**Step 2 — Build the report** to the exact 9-section structure above. Every Purpose line must name actual fields/resources (never a restated verb+noun), and must agree with that endpoint's own Request Validation table.

**Step 2.5 — Mandatory formatting.** Numbered, tagged endpoint headings (`### 3.4 \`POST /store/carts\` (External)`), bold standalone field labels (never bullet-wrapped tables), `---` separators between every subsection, backticked field/param names, a tabular Overview, and fenced ` ```json ` sample requests — applied with the same rigor at endpoint #1 and endpoint #50.

**Step 3 — Mandatory self-check before writing.** A hard gate sequence run before the file is produced:
- Scan Manifest present and placed correctly (checked first)
- Formatting gate (bullet-wrapped tables, missing separators, unbacktickled fields, non-tabular Overview)
- Vague-purpose check (every Purpose line names real fields)
- Request Validation ↔ Sample Request pairing check
- Schema-by-name-only check (validator schemas must be opened and their fields listed, not just named)
- Coverage check (every scanned route group has inventory rows)
- Category consistency check (Overview claims match Section 2 rows)
- Placeholder check (`n/a`, `TBD`, `...`, fabricated samples)
- Depth-uniformity check (no quality cliff partway through Section 3)
- Re-verification of every method/path/status code against the actual route/controller file

Only after every check passes clean is the file written.

---

## Usage

Trigger it with prompts like:

- "Analyze my API"
- "Document my API"
- "Generate an API report for this codebase"
- "Review internal vs external APIs in this project"

If no codebase or project has been provided, the skill asks for it rather than producing a report from assumptions.

---

## Files

| File | Purpose |
|---|---|
| `api_analysis_skill.md` (SKILL.md) | Skill definition — scan methodology, exact 9-section report structure, mandatory formatting rules, and the pre-write self-check gate sequence |
| `api-analysis-report.md` | Example output: full 357-route-file scan of the Medusa v2.17.2 backend, Admin/Store/Auth/Plugin APIs at uniform per-endpoint depth |
