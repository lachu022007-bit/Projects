---
name: universal-code-review
description: Perform a deep, exhaustive, language-agnostic code review on ANY codebase, project type, or framework — always use this whenever the user asks to "review my code," "check for bugs," "audit this project," "find issues," "clean up this code," "is this production ready," or similar, regardless of language, stack, or project size. Do NOT wait for the user to name specific issue categories — this skill covers bugs, duplication, naming, style/convention violations, security vulnerabilities, missing validation, performance problems, and any other defect class found, with no fixed or limited list. Always produce the findings as a single structured table with severity ratings, root cause, fix, and improvement suggestions, plus an architecture diagram. Trigger this even if the user only says something vague like "look at my code" or "what's wrong with this repo."
---

# Universal Code Review

A single skill that reviews **any codebase, in any language, framework, or domain**, with no assumption about what kind of project it is and no fixed ceiling on what categories of issues get reported. Built to be used the same way whether the "customer" is a two-file weekend script or a monorepo.

## Architecture Diagram Generation

The skill must work out the reviewed project's own architecture from what's actually in front of it and turn that into a real diagram — it never has a fixed set of layers to fall back on, and it never uses one project's shape as a template for the next.

1. Walk the codebase and group what's found into whatever real layers/components it actually has — this might be a frontend/backend/data split, a set of independent services, a single-layer script, a library with no layers at all, a CLI with subcommands, or something that doesn't match any common shape. Name the groups after what they actually are in this codebase, not after generic labels borrowed from a textbook.
2. Only draw a connection between two groups if the code actually calls, imports, or depends on the other — never assume a typical layer order (e.g. don't assume "frontend talks to backend talks to database" unless that's what the code shows).
3. Render it as a real diagram in the delivered output, built fresh from what was found in that specific codebase, using a **grouped/nested layout, not a flat flowchart**: each layer/group from Step 1 is drawn as its own labeled outer container (a bounding box with the group's name as a header, e.g. "Frontend Layer," "Data Layer"), with that group's individual component boxes nested inside it. Arrows for real dependencies are drawn between the individual component boxes (which may cross container boundaries), not between the outer containers themselves. A version with every component as a loose flat box and no visible layer containers is not acceptable output — the containers are what make the diagram readable at a glance. Nothing about the shape, count, or names of the boxes or containers is fixed in advance; every run is built from scratch from what that project actually contains.
4. Follow Step 5 below to render it as a real image, not text.

## Core Principles (non-negotiable)

1. **No fixed category list.** Bugs, duplication, naming, security, missing validation, and performance are the *minimum floor*, not the ceiling. Actively look for anything else wrong — concurrency bugs, memory/resource leaks, architectural violations, dead code, test gaps, accessibility, dependency risk, configuration/secrets handling, logging, error-handling anti-patterns, API misuse, scalability limits, license issues, technical debt — see the full checklist below, and go beyond it if the code warrants it. If a new category of problem doesn't fit any existing row type, add it as its own row rather than dropping it.
2. **No assumptions.** Every finding must be backed by something actually present in the code — an exact file path and line number/range, or a quoted structural fact (e.g., "no exception handling around this network call"). If you are not sure something is a bug, either verify it by tracing the code path or label it clearly as "Needs confirmation" in the notes — never state a guess as fact.
3. **Never narrate detection mechanics to the user.** Silently work out the language(s), frameworks, and project type from the codebase itself before reviewing it. Do not open the report with a paragraph explaining what kind of project this is, and do not describe or list how the detection was done. Mentioning the stack briefly inline is fine only if it's directly relevant to a specific finding.
4. **Universal by construction.** Never hardcode assumptions that only hold for one ecosystem. Map each category to the right idioms for whatever language is in front of you (e.g., "missing validation" means null-checks and schema validation in one codebase, and might mean missing Pydantic/zod/joi schemas in another).
5. **Findings live in tables, not prose — and every table keeps its full columns.** Every issue goes in All Issues, its matching fix goes in Fixes & Solutions, and every metric/recommendation belongs in one of the other tables defined in "Report Format" below, with no exceptions — this includes anything that feels like "analysis" or "explanation": that content goes inside a table cell (Problem, Impact, Notes), never as its own paragraph or write-up sitting outside a table. Grouping issues and fixes by severity within their own table is fine and expected — that's still tables, not prose. What's never allowed is a freestanding "Detailed Analysis" section, bullet points outside a table, a standalone table that only re-scores categories with numbers already broken out row by row elsewhere, dropping the Category column from a table to save space, or drawing a diagram to re-display numbers that are already sitting in a table (see the diagram rule in "Report Format"). The only things in the entire report that aren't tables are the four required diagrams (System Architecture, Code Quality, Security Scan, Code Flow). Summary/context text between sections stays to a single line.
6. **Cross-check before delivering.** Do a second pass over your own findings before presenting them (see "Cross-Check Pass" below) and silently drop or correct anything that doesn't hold up — don't show your verification work, just deliver the corrected table.

---

## Step-by-Step Process

### Step 1 — Map the codebase
Inventory the files and folders relevant to the review, quietly excluding anything that isn't hand-written source (dependency folders, build output, environment folders, lockfiles, generated code, and binary/asset files) unless the user asks about them. For large repos, prioritize: entry points, business logic, API/controller layers, data access/models, auth/security-sensitive code, and anything the user points at directly.

### Step 2 — Scan against the full checklist
Work through every category in the checklist below against the mapped code. Do not stop once you've found "enough" — a real review at a large company would keep going until the codebase is exhausted, not until a quota is hit.

### Step 3 — Record every finding with full context
For each issue, capture before moving on:
- Exact location (a file path and a line number or line range)
- What's wrong, stated as a fact, not an opinion
- Why it's a problem (root cause / mechanism of failure)
- Severity (see rating rubric)
- Concrete fix (working code or precise instruction — not "consider improving this")
- A further improvement suggestion beyond the minimum fix, if one exists (e.g., the fix stops the bug, the improvement makes the pattern reusable / more idiomatic / testable)

### Step 4 — Cross-Check Pass (mandatory, silent)
Before writing the final report, re-open each flagged location and verify:
- The line/range actually contains what the finding claims.
- The severity is consistent with similar findings elsewhere in the same report (don't call one unhandled exception "High" and an identical pattern elsewhere "Low" without a stated reason).
- No duplicate rows describe the same root cause in the same location.
- No finding depends on an assumption about code you haven't actually seen (e.g., don't flag "no input validation" on a function whose caller you haven't checked, if the caller might already validate — check the caller before flagging, or note it explicitly).
- Nothing is invented. If a suspected issue can't be confirmed by reading the code, remove it or mark it clearly as unconfirmed rather than presenting it as a defect.

### Step 5 — Render Mermaid diagrams for the four diagrams
Always produce Mermaid diagrams for all four required diagrams (System Architecture, Code Quality, Security Scan, Code Flow). Mermaid renders natively in GitHub, GitLab, and many markdown viewers. Only include modules, components, findings, connections, branches, and percentages that actually exist in or were derived from the code; never invent anything to make a picture look fuller. For System Architecture and Code Flow — which are component/flow maps — use subgraphs and color/mark any component that contains a High/Critical finding, so they double as a risk map at a glance. Security Scan is a decision tree and must show both the Yes and No branch at every checkpoint node, not only the failing branch — mark the No-branch outcome boxes that represent actual findings. Code Quality is a pie chart (Good/Fair/Critical percentage split) rather than a component map, so the coloring rule applies to its three slices instead of individual components.

Don't render extra diagrams that just re-chart numbers already sitting in a table — severity counts, category counts, pass/fail summaries, and priority ordering all belong in the tables defined under "Report Format," not as a second visual encoding of the same numbers. The four diagrams above are the diagrams this report needs by default; nothing further.

Note: Avoid using quotes inside Mermaid node labels as this can cause parsing errors. Use line breaks (<br/>) instead of quotes for multi-line labels.

### Step 6 — Deliver the report
Output format is fixed — see "Report Format" below. There is no intro line, no context paragraph, and no closing summary — the report starts directly at the first table (Project Info, which already carries the date and context) and ends at the last table (Crosscheck Results). No filler, no restating the checklist to the user, no over-explaining what a "medium severity" issue is unless asked, no closing priority or summary paragraph.

---

## Issue Checklist (floor, not ceiling)

Use these as required categories to actively check — but treat this as a starting checklist, not a boundary. Add rows for anything real that doesn't fit a category below.

**Correctness / Bugs**
Logic errors, off-by-one errors, incorrect conditionals, wrong operator precedence, null/undefined/None dereference, type coercion bugs, incorrect async/await or promise handling, race conditions, unhandled edge cases (empty input, zero, negative numbers, unicode, huge input), incorrect error propagation, silent exception swallowing (an empty catch block or a bare except clause), off-happy-path failures.

**Duplication**
Copy-pasted logic blocks, repeated magic values that should be constants, parallel implementations of the same feature, duplicated validation/error-handling logic that should be centralized.

**Naming & Readability**
Misleading names, inconsistent casing conventions for the language, single-letter variables outside tight loops, ambiguous abbreviations, functions/classes whose name doesn't match what they do, inconsistent terminology for the same concept across the codebase.

**Convention / Style Violations**
Deviation from the language's idiomatic style or the project's own established patterns (detect the dominant local style and flag outliers to it, not to some external style guide the project doesn't use), inconsistent formatting, violation of the project's own linter/formatter config if one exists.

**Security**
Injection risks (SQL, command, template, LDAP, NoSQL), XSS, CSRF gaps, insecure deserialization, hardcoded secrets/credentials/API keys, weak or missing encryption, insecure randomness for security-sensitive values, missing authN/authZ checks, privilege escalation paths, path traversal, SSRF, unsafe use of dynamic code evaluation or reflection, outdated or known-vulnerable dependencies, verbose error messages leaking internals, insecure CORS/config defaults, missing rate limiting on sensitive endpoints.

**Missing Validation / Defensive Gaps**
Unvalidated user input, missing schema validation on API boundaries, missing null/type checks before use, missing bounds checks, unchecked array/index access, unvalidated file uploads, missing sanitization before storage or rendering, assumptions about input shape/size that aren't enforced.

**Performance**
N+1 queries, unnecessary loops/re-computation, missing memoization/caching where clearly warranted, blocking I/O on a hot or main thread, large synchronous operations, inefficient data structures for the access pattern, unbounded loops/recursion, unnecessary re-renders (frontend), missing indexes implied by query patterns, excessive object allocation in hot paths.

**Resource & Concurrency Issues**
Unclosed file handles/connections/sockets, memory leaks, missing connection pooling, deadlock-prone lock ordering, shared mutable state without synchronization, missing cleanup in a finally block, defer statement, or equivalent resource-cleanup construct.

**Error Handling**
Generic catch-alls that hide the real error, missing error handling on I/O/network calls, inconsistent error types/shapes returned from similar functions, errors logged but not handled (or handled but not logged), missing retries/backoff where transient failures are expected.

**Architecture / Design**
Violations of separation of concerns (business logic in controllers/views, DB queries in UI layer), tight coupling between modules that should be independent, circular dependencies, God objects/functions doing too much, missing abstraction where one is clearly warranted, violation of the project's own layering.

**Testing**
Missing tests for critical paths, tests that don't actually assert meaningful behavior, flaky-looking tests (time-based sleeps, order-dependent state), untested error branches.

**Documentation**
Missing or stale docstrings/comments on non-obvious logic, comments that contradict the code, missing README/setup instructions for a project that clearly needs them, undocumented public API surface.

**Dependency & Configuration Risk**
Outdated major-version dependencies, unpinned versions in a context that needs reproducibility, secrets committed to config files, environment-specific values hardcoded instead of externalized, missing example environment configuration for new setups.

**Accessibility (if UI code is present)**
Missing alt text, non-semantic HTML for interactive elements, missing keyboard navigation support, insufficient color contrast in defined theme values, missing ARIA where a custom widget needs it.

**Anything else found**
If the code exhibits a real problem that doesn't fit any category above, still report it — add a new row and label the category descriptively rather than omitting the finding.

---

## Severity Rating Rubric

| Rating | Meaning |
|---|---|
| **🔴High Severity Level** | One combined top tier — covers both an actively exploitable security hole, data loss/corruption path, or crash-in-normal-use bug (what used to be called "Critical") and a serious bug/security weakness/performance problem that will cause real incidents under realistic conditions but isn't actively triggered yet (what used to be called "High"). In practice these two are close enough that they don't need separate ratings — use High/Critical as one tier and note in the Impact cell if a specific row is the more severe, actively-exploitable end of that tier. |
| **🟡Medium Severity Level** | Real defect or violation that degrades quality, maintainability, or reliability, but has limited blast radius or requires uncommon conditions to trigger. |
| **🟢Low Severity Level** | Style, naming, minor duplication, or small polish item — correct but improvable; no functional or security risk. |

Every row must get exactly one rating. Don't inflate severity to seem thorough, and don't downplay real security/correctness issues to seem lenient.

---

## Report Format (required output shape)

No prose opens the report. Go directly into the tables, in this order:

**1. Project Info** — a small table: date, language(s) detected, number of files reviewed, total issues found, crosscheck status.

**2. Quality Metrics** — one table scoring the codebase across every dimension that matters:

| Metric | Rating |
|---|---|
| Overall Score | e.g. 52/100 |
| Code Quality | ... |
| Security Posture | ... |
| Performance | ... |
| Scalability | ... |
| Compatibility | ... |
| Maintainability | ... |
| Test Coverage | ... |
| Estimated Fix Time | ... |

Performance, Scalability, and Compatibility are always their own rows here — they matter as much as security and code quality, not optional extras. If any of these needs more than a one-line rating to be useful (a specific scaling bottleneck, a concrete browser/runtime/version compatibility gap), give it its own short table right underneath instead of cramming detail into this one.

**3. Issue Summary** — one table with counts by severity (High/Critical/Medium/Low), and however you want to slice it further (by category, by difficulty). This is the only place severity/category counts get summarized — see the diagram rule below for why they don't also get charted.

**4. Diagrams** — four real rendered images, each showing something a table can't:

- **System Architecture** — the real, auto-detected diagram from "Architecture Diagram Generation" above, drawn with visible labeled layer containers (not a flat flowchart of loose boxes) — see that section's Step 3 for the exact layout requirement. Arrows only where a real call/import/dependency exists. Color or mark any component that contains a High/Critical finding.
- **Code Quality** — a pie chart showing the percentage breakdown of the codebase across three tiers: **Good**, **Fair**, and **Critical**. Derive the split from what Step 2's scan actually found (e.g., proportion of files/functions/modules landing in each tier based on real defect density, complexity, and severity of findings present in them) — never invented percentages. This is a genuinely different view from the Quality Metrics table's single-line scores, since it shows the *distribution* across the codebase rather than one aggregate rating per dimension.
- **Security Scan** — a decision-tree-style visual per security-relevant checkpoint found in the code (e.g., "Validation?", "Secure?", "Protected?"), built from the actual Security-category findings — not a pass/fail chart of counts already in the Issue Summary table. Every decision node must show **both branches**, Yes and No — never only the failing branch. The No branch leads to the vulnerable file/line and the finding (as in a real gap found in the code); the Yes branch leads to what a passing/secure version of that checkpoint looks like (a brief label, e.g. "Input sanitized" or "Session regenerated on login"), so the diagram shows the full decision, not just the failures. If a given checkpoint in this codebase has no code path that ever passes it (i.e., it's unconditionally broken), still draw the Yes branch labeled with what secure behavior would look like there, so the tree stays structurally complete rather than one-sided.
- **Code Flow** — a diagram of how control or data actually moves through the system for its main paths (e.g., request lifecycle, a core data pipeline, the primary user action), based on what the code actually calls in sequence — not a repeat of the System Architecture's static dependency graph.

Render all four as real images per Step 5. Do not draw an Issue Distribution diagram/tree, a Fix Priority diagram/chart, or any other visual whose only content is re-plotting counts already sitting in the Issue Summary table — those stay banned. The four diagrams above are allowed and required because each shows structure, flow, or clustering a table can't, not because they display numbers.

**5. All Issues** — every issue found, split into three separate sub-tables by severity tier, in this order. Do not merge these into one table and do not skip a sub-table heading even if that tier is empty (write "None found" as the only row in that case):

*🔴High Severity Level*

| # | Category | File | Line | Issue | Impact |
|---|---|---|---|---|---|

*🟡Medium Severity Level*

| # | Category | File | Line | Issue | Impact |
|---|---|---|---|---|---|

*🟢Low Severity Level*

| # | Category | File | Line | Issue | Impact |
|---|---|---|---|---|---|

(Mistake Level is dropped as a column here since the sub-table heading already states the tier — don't restate it in a column.)

**6. Fixes & Solutions** — the matching fix for every row above, split into the same three sub-tables by severity tier, in the same order, one row per issue matched 1:1 to its All Issues row via the same # and Category (this is where the fix, before/after code, and improvement suggestion live — this also carries the per-category information, so no separate category-ratings table is ever needed):

*🔴High Severity Level*

| # | Category | Issue Title | File | Problem | Impact | Improvement | Fix Steps | Before | After |
|---|---|---|---|---|---|---|---|---|---|

*🟡Medium Severity Level*

| # | Category | Issue Title | File | Problem | Impact | Improvement | Fix Steps | Before | After |
|---|---|---|---|---|---|---|---|---|---|

*🟢Low Severity Level*

| # | Category | Issue Title | File | Problem | Impact | Improvement | Fix Steps | Before | After |
|---|---|---|---|---|---|---|---|---|---|

All Issues and Fixes & Solutions are two table-groups, not one: All Issues is the short-form list per tier (what's wrong, where, how bad); Fixes & Solutions is the long-form companion per tier (why it matters, how to fix it, the before/after code). Every row in an All Issues sub-table has exactly one matching row in the same-tier Fixes & Solutions sub-table with the same # and Category — never merge Issues and Fixes into a single table, and never drop the Category column from either one. Never go back to one undivided table for either section — the High/Critical, Medium, and Low sub-tables must be visibly separate every time, not just internally sorted rows.

Use a real before/after snippet or diff wherever the fix is a concrete code change, not a prose description — but keep every Before/After cell **table-safe**: a markdown table row breaks the moment a cell contains a raw line break or an unescaped `|`, so multi-line code must be flattened before it goes in the cell. To do this: join multiple lines with `<br>` (not an actual newline) between them, wrap the whole snippet in inline code formatting with single backticks (not a triple-backtick fenced block, which cannot sit inside a table cell), and escape any literal `|` character in the code as `\|`. If a fix genuinely can't be condensed this way without losing meaning (e.g., a large multi-function change), put a short representative line or two in the cell using the same `<br>`-joined format and add "(see Fix Steps for full change)" rather than breaking the table.

**7.💪 What is done well(Strengths)** — a short table of what's genuinely done well, with evidence (file/line) — the report isn't only criticism.

**8.🔮 Recommendations / Future Scope** — a table of forward-looking suggestions (effort, impact) that go beyond fixing what's already broken.

**9.✅ Crosscheck Results** — a small table confirming what was verified before delivery (per Step 4).

Do **not** include:
- **A separate per-category ratings table** (e.g. a standalone table scoring "Security: 2/10, Performance: 5/10 ..." with nothing else in it). That information lives inside the Category column of All Issues and Fixes & Solutions instead — don't build a third table just to re-score categories that are already broken out row by row.
- **A separate fix-checklist/priority-tracking table.** If a priority marker or status checkbox is genuinely useful, add it as a column inside the All Issues or Fixes & Solutions tables instead of building a whole extra table around it.
- **A Files Reviewed table.** File-by-file detail is already covered by the File column in All Issues and Fixes & Solutions, and Project Info already gives the total file count — a separate table listing every file doesn't scale once a project has more than a handful of files, so skip it entirely.

Do not add a "type of project" section, a "Detailed Analysis" paragraph, a lengthy methodology write-up, or restate the checklist categories in the output — those are internal process, not deliverable content. If there's genuinely more explanation needed for a finding than a short sentence, that explanation goes in a table cell (Problem, Impact, Notes) — never as a separate narrative block sitting outside a table. The only non-tabular elements in the whole report are the four required diagrams from Step 5; literally everything else — every explanation, every score, every recommendation — is a row in a table.

### Format enforcement — what this means concretely

**NEVER write a finding like this:** a markdown heading for the issue, followed by bolded label lines such as "File:" and "Severity:", a fenced code sample showing the problem, a second fenced code sample under a "Fix:" label showing the correction, and a bullet list of migration steps underneath — all sitting outside any table.

**ALWAYS write the same finding like this instead:** as one row in the correct severity sub-table of All Issues (#, Category, File, Line, Issue, Impact) with a matching row in the same-tier sub-table of Fixes & Solutions carrying the same # and Category, plus Issue Title, File, Problem, Impact, Improvement, Fix Steps, and — where the fix is a concrete code change — the before and after code shown inside those two cells as `<br>`-joined inline code (never a fenced block, never a raw newline) so the row stays a valid table row rather than breaking into separate fenced blocks sitting next to the table.

No markdown headings per issue, no bolded label lines, no fenced code blocks floating outside a table cell, and no bullet list of steps sitting under a heading. Every finding is a matched pair of rows across the two same-tier sub-tables, not a mini-report of its own.

---

## Additional Capabilities

These extend the core review and should be applied whenever they're relevant to what's in front of you — not every review needs every one of these, but don't skip them out of habit either.

**Use real tools when they're already in the project.** If the repo already has a linter, formatter, or static analyzer configured, run it and fold real results into the same table instead of re-deriving what a tool would already tell you. Attribute those rows honestly (note which tool detected each one) so the user can tell tool-verified findings apart from manual review findings.

**Complexity & maintainability signals.** Where relevant, call out functions/files with excessive cyclomatic complexity, very long functions/classes, deep nesting, or very high fan-in/fan-out — these are legitimate "Architecture" or "Performance"-adjacent rows even without a specific bug.

**Confidence tagging.** If a finding is a strong structural signal but you couldn't fully trace every caller (e.g., in a huge codebase), add a short unconfirmed note in the Root Cause or Fix cell rather than silently presenting it as certain — this keeps rule 2 ("no assumptions") honest at scale.

**Suppression respect.** If the codebase uses inline suppression comments for its existing linters, don't re-flag exactly what that comment already suppresses unless the suppression itself looks wrong (e.g., silencing a real security rule) — in that case, flag the suppression as its own row.

**A fifth diagram, only if it shows something new.** By default the four required diagrams (System Architecture, Code Quality, Security Scan, Code Flow) are the only visuals in the report. The one exception: if the system is genuinely too large or dense for one readable System Architecture diagram, split it into an overview plus one focused diagram of a specific risky flow (e.g., the auth flow, the payment flow). Never add another diagram that just re-encodes severity or category counts already sitting in a table — that's not a new view, it's a duplicate one.

**Output format flexibility.** Default deliverable is the Markdown tables + image in the chat/PR. If the user asks for it in another shape (CSV/JSON for CI ingestion, a Word/PDF report for stakeholders, an HTML page), produce that from the same underlying findings rather than re-reviewing the code.

**CI/CD framing (optional, only if the user is clearly working in a pipeline context).** Note that Critical/High counts could gate a build (e.g., "fail the pipeline if Critical > 0"), but don't assume this is wanted unless the user's context suggests a CI use case.

**Diffs in the Fix column.** Where a fix is a small, concrete code change, show it as an actual before/after snippet or unified diff in the Before/After cells rather than a prose description — it's directly actionable that way. Keep it table-safe: `<br>`-joined lines inside inline-code backticks, never a fenced code block or a raw newline, or the table row breaks.

---

## Hard Rules Recap

- Never guess — verify every finding against the actual code before it goes in a table.
- Never limit yourself to a fixed set of categories; the checklist is a floor.
- Never present the report before completing the silent cross-check pass.
- Never narrate which project type, stack, or "few categories" were checked — just do the work and show results.
- Always rate severity Low/Medium/High-Critical (one merged top tier), always give a concrete fix, always give a further improvement suggestion where one exists.
- Always score Performance, Scalability, and Compatibility alongside Security and Code Quality in the Quality Metrics table — never treat them as optional extras.
- Always include four real rendered images — System Architecture, Code Quality, Security Scan, Code Flow — unless the codebase is trivially small (e.g., a single short script) or the rendering tools genuinely aren't available — in either case, say briefly why an image was skipped rather than fabricating one.
- Never add a fifth diagram or image beyond those four — specifically, never draw an Issue Distribution diagram or a Fix Priority diagram/chart, since both just re-chart numbers already sitting in the Issue Summary table.
- Never invent nodes, edges, components, or flows in any diagram that don't exist in the actual code.
- Never produce a separate per-category ratings table (a table that only re-scores categories with no other content) or a Files Reviewed table — fold that information into the Category column already present in All Issues and Fixes & Solutions.
- Never write a freestanding "Detailed Analysis" or explanation paragraph outside a table — if something needs explaining, it goes in a table cell (Problem, Impact, Notes). The four diagrams are the only non-tabular elements in the whole report.
- Never format a finding as its own heading with bolded label lines and a floating code sample. Every finding is a matched row-pair across the All Issues and Fixes & Solutions tables, sharing the same # and Category — see "Format enforcement" above.
