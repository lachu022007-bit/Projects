---
name: api-documentation-library
description: Generate a complete, multi-file API documentation library from a codebase — a master index file (00_API_INDEX.md with a full route inventory and module summary table) plus one Markdown file per module/resource (e.g. 01_AUTHENTICATION.md, 02_USERS.md, 03_PRODUCTS.md), each documenting every route, function, middleware, validator, workflow, service, and database interaction in exhaustive, beginner-friendly detail. Use this whenever the user asks to "document", "analyze", or "explain" an API codebase, especially larger ones (50+ route files), or explicitly asks for per-module documentation files instead of a single report. Do NOT use for a quick single-file summary or a short architecture overview — only when the user wants the full documentation library treatment.
---

# API Documentation Library

## Objective

When asked to analyze or document an API codebase, produce a complete API documentation **library** — not a single report. The goal is for a beginner or developer to understand exactly how every module works internally, end to end.

## Ground every claim in the actual source (non-negotiable)

Every section of every module file must come from **reading that route file's actual source code** — its handler, imports, and the functions/services/workflows it calls — not from describing "how this kind of framework generally works."

- **Before writing about a route file, open and read it.** If a description would be equally true if pasted into a different module's file, it hasn't been grounded in the real code — rewrite it from what the file actually does.
- Avoid boilerplate phrasing that substitutes for real content, e.g. "delegates business logic to services and workflows," "the exact table set depends on the resource," "Route file discovered under the API tree." These are true of almost anything and communicate nothing — replace them with the actual services/workflows/tables that file uses.
- If a route file cannot be opened or its contents can't be determined, say so explicitly in that file's entry (e.g. "Source could not be read — documented from filename/path only") rather than inventing plausible-sounding detail to fill the gap.

## When NOT to use this

If the user just wants a quick summary, a single architecture diagram, or a lightweight overview, don't invoke this full-library workflow — it is intentionally exhaustive and expensive. Confirm with the user if it's ambiguous whether they want the full treatment.

## Documentation Strategy

Do **not** generate one large report. Instead, produce:

1. **One Master Index file** (`00_API_INDEX.md`) — the entry point for the whole library.
2. **Multiple Module Documentation files** — one per module or resource, numbered for reading order after the index.

Example module file set (adapt names/module boundaries to what's actually in the codebase — never force a module list that doesn't match the real routes):

```
00_API_INDEX.md
01_AUTHENTICATION.md
02_USERS.md
03_PRODUCTS.md
04_ORDERS.md
05_PAYMENTS.md
06_SHIPPING.md
07_INVENTORY.md
08_SETTINGS.md
09_CUSTOMERS.md
10_CHECKOUT.md
11_RETURNS.md
12_WEBHOOKS.md
```

Continue creating files until **every discovered route file** has been documented. Never leave a scanned route file undocumented.

## Master Index File (00_API_INDEX.md)

Generate this file **first**, before any module file. It must contain:

### Overview
- Project name/description
- Framework (if identifiable)
- Total route files, total endpoints
- Breakdown of Internal APIs, External APIs, Plugin APIs (if applicable)

### Route Summary Table
One row per module:

| Module | Route Files | Total APIs | Purpose | Documentation File |
|--------|-------------|------------|---------|---------------------|
| Authentication | 14 | XX | User authentication and authorization | 01_AUTHENTICATION.md |

### Complete Route Inventory
List **every** discovered route file exactly once — never summarized, never sampled:

| No | Route File | Module | Internal / External | Purpose | Documentation |
|----|------------|--------|----------------------|---------|----------------|

**Integrity rule:** the `Documentation` column must always name the module file that *actually documents that specific route*. A row can never cite a different module's file just because that file exists — a mismatch here means the route was mis-assigned upstream and needs to be re-grouped, not force-linked.

**Route files vs. endpoints:** a single route file often exports multiple HTTP methods (e.g. `GET` + `POST` on the same file), so the total endpoint count will exceed the total route file count. State this explicitly in the Overview section so the two numbers in the summary table don't look inconsistent.

**Handling partial route detail (e.g. rebuilding an index from existing module docs, not a fresh scan):** when the index is being generated or updated from module files that were *not* all documented to the same depth — some modules list every concrete `route.ts` path, others only list a top-level folder like `admin/tax-regions/` with no file-level breakdown — never fabricate a precise count to fill the gap:
- Report the real, countable total (e.g. "47 route files enumerated at file level") separately from any folder-level-only groups (e.g. "15 additional route groups documented at folder level only").
- In the Route Summary Table, mark folder-level-only modules' `Total APIs` as `not itemized` rather than guessing a number — a guess here (e.g. writing "10+" with no basis) is worse than an honest gap.
- In the Complete Route Inventory, folder-only entries get a `*` (or similar) marker and a path like `admin/tax-regions/*`, with a footnote explaining the marker.
- Add a short **Notes** section at the end of the index stating which modules are file-level-complete vs. folder-level-only, and what it would take to close the gap (e.g. "expand modules 11–17 with the same route.ts-level detail used in 01–10").
- If a prior draft or template implies a specific large total (e.g. "320 route files" from an example or earlier version) but the current source docs don't substantiate that count, don't carry the old number forward silently — recompute from what's actually documented and flag the discrepancy instead of repeating an unverified figure.

### Module Relationship Diagram
Show how modules connect to each other, e.g.:
```
Authentication
↓
Users
↓
Products
↓
Orders
↓
Payments
```
Adapt the chain to the real dependency order of the codebase being documented.

### Documentation Files
List every generated module markdown file with a one-line description.

## Workflow

1. **Scan the codebase** to discover every route file and group them into logical modules **by actual resource domain**, not by directory convenience. Determine the module from what the route *does* (e.g. a payments route is Payments even if it lives under an `admin/` tree that also contains product routes).
   - **Never create a catch-all module.** If a module (like "Products") starts absorbing unrelated domains — payments, shipping, returns, promotions, regions/currencies, API keys, webhooks — that is a sign the grouping is wrong, not a sign those domains belong there. Split them into their own accurately named modules, even if some end up small (a module with 3 route files is still its own module).
   - If a resource domain is discovered but won't get a full dedicated file in this pass, still give it its own row in the Route Summary Table and mark its Documentation File as `Pending` / `Not yet documented` — never point it at a neighboring module's file as a placeholder.
2. **Confirm scope with the user** if the codebase is very large — e.g. "I found N route files across M modules; I'll generate the index plus M markdown files." Proceed by default rather than blocking, but flag scale.
3. **Generate `00_API_INDEX.md` first**, following the Master Index structure above exactly, with every route file accounted for.
4. **Work module by module, not the whole codebase in one pass.** For each module: read every one of its route files first, then write that module's markdown file, then move to the next module. Attempting many modules or hundreds of route files in a single continuous pass is what causes drift into generic, ungrounded descriptions — small batches keep every claim traceable to a specific file just read.
5. **Generate one Markdown file per module**, following the Module Documentation Structure below exactly.
6. **Never summarize or merge multiple endpoints into one section.** Never use wildcards like `/products*` or phrases like "representative endpoints" — every endpoint gets its own fully detailed section.
7. **Self-audit before saving** — run through the Verification Checklist below and fix any failures.
8. Save all files to the output directory and present them to the user when done.

## Module Documentation Structure

Every generated Markdown file must follow this structure, in this order:

### 1. Overview
- Purpose of the module
- Why it exists
- Internal or External API
- Who uses it
- Authentication required
- Dependencies

### 2. Folder Structure
Show the real folder hierarchy for this module (e.g.):
```
admin/products/
├── route.ts
├── [id]/route.ts
├── variants/
├── options/
```
Explain what every file is responsible for.

### 3. Route Files
For every route file: file path, purpose, exported handlers, middleware used, validation used, services called, workflows executed. Never skip a route file.

### 4. API Endpoints
Document **every** endpoint individually — never grouped, never wildcarded. Each endpoint gets its own subsection with these parts, **in this order, using tables wherever noted** (prose-only descriptions of headers/validation/errors are not sufficient):

- **Method, URL, Classification** (Internal/External), **Purpose**, **Business Use Case**
- **Authentication** — explicitly label one of: `Public (no auth)` / `Bearer token (customer)` / `Bearer token (admin)` / `API key`. Never leave this implied.
- **Headers table** — every header the request needs or may send:

  | Header | Required | Example Value | Description |
  |---|---|---|---|

- **Path Parameters table** (if any):

  | Param | Type | Required | Description |
  |---|---|---|---|

- **Query Parameters table** (if any — for list endpoints always include pagination/filter/field-selection params with their defaults, e.g. `limit`, `offset`, `fields`, filter keys):

  | Param | Type | Required | Default | Description |
  |---|---|---|---|---|

- **Request Body** — a realistic JSON example, followed by a field-level **Validation table**:

  | Field | Type | Required | Validation Rule |
  |---|---|---|---|

  (Skip both if the endpoint has no body.)
- **Middleware, Business Logic, Workflow** — the execution chain this endpoint runs through
- **Database Operations, Service Calls**
- **Response Structure**:
  - Success: HTTP status code + JSON example
  - Every error case in a **Status & Error Code table**:

    | HTTP Code | Error Code | Meaning | Description |
    |---|---|---|---|

- **Source File, Dependencies, Related Endpoints**
- **Example Request, Example Response, Notes**

If the codebase uses a consistent error envelope (e.g. `{ type, message, code }`), show it once at the top of the module's Error Handling section (#12) rather than repeating the full shape in every endpoint's error table.

### 5. Functions
For every function used in the route: Name, Purpose, Parameters, Return Type, Internal Logic, Called By, Calls To, Example.

### 6. Middleware
For every middleware: Purpose, Execution Order, Input, Output, Authentication, Authorization, Logging, Rate Limiting, Error Handling.

### 7. Validation
For every request field: Name, Data Type, Required/Optional, Default Value, Validation Rules, Example JSON.

### 8. Business Logic
Describe the full execution flow step by step:
```
Client → Router → Middleware → Authentication → Validation → Workflow → Service → Repository → Database → Response
```

### 9. Services
Purpose, Methods, Responsibilities, Dependencies, Example Usage — for every service.

### 10. Workflows
Trigger, Execution Steps, Input, Output, Rollback, Failure Cases — for every workflow.

### 11. Database
Tables Used, Reads, Writes, Updates, Deletes, Relationships, Transactions.

### 12. Error Handling
- Show the module's **standard error response envelope** once (JSON example), if the codebase uses a consistent shape.
- For every possible error across the module's endpoints: Status Code, Reason, Example Response, Recovery.
- Close with a **Status Code Quick Reference table** rolling up every distinct HTTP status code used anywhere in the module, for fast lookup:

  | Code | Category | Typical Meaning (in this module) |
  |---|---|---|

### 13. Security
Authentication, Authorization, RBAC, Permissions, API Keys, JWT, Sessions, CORS, CSRF, Rate Limiting, Webhook Verification, Security Best Practices.

### 14. Learning Notes
Teach the module like a mentor: why it exists, why it was designed this way, advantages/disadvantages, real-world examples, comparison with a simpler/naive implementation approach, common interview questions, common mistakes, best practices.

### 15. Module Summary
Purpose; Important Classes, Functions, Services, Workflows, Middleware, Validators, Endpoints; how this module connects to other modules.

## Verification Checklist

Run this self-audit before saving/presenting the library. Fix any failure rather than noting it as a caveat:

- [ ] Every route file discovered in the scan appears exactly once in the index's Complete Route Inventory.
- [ ] No module is a catch-all — every route's module reflects its actual resource domain (payments routes are in a Payments module, not folded into Products/Carts/etc.).
- [ ] Every `Documentation` column entry in the index points to the module file that genuinely covers that route — no copy-paste mismatches.
- [ ] The Overview explains the route-file-count vs. endpoint-count distinction if they differ.
- [ ] Any module documented at folder-level only (no per-file breakdown) is marked `not itemized` in Total APIs and `*` in the route inventory — never given a guessed number, and never silently inherits a total from an old draft or template.
- [ ] Every endpoint section has a Headers table, Path/Query Parameter tables (where applicable), a Request Body example + Validation table (where applicable), a Success response example, and a Status & Error Code table.
- [ ] Every endpoint's Authentication requirement is explicitly labeled (not just implied by module-level text).
- [ ] No endpoint is grouped, wildcarded, or described as "representative."
- [ ] Each module file ends with a Status Code Quick Reference table (#12) and a Module Summary (#15).
- [ ] **Genericness spot-check:** pick 2-3 endpoint or route-file descriptions at random and ask "would this sentence still read correctly if pasted into a different module's file?" If yes, it's not grounded in the actual source — reopen that file and rewrite the description from what it really does.
- [ ] Every route file entry either reflects content actually read from source, or is explicitly flagged as unreadable/undetermined — never silently backfilled with plausible-sounding but unverified detail.

## Output Rules

- Create **00_API_INDEX.md** first, then one Markdown file per module.
- Include every discovered route file in the index's Complete Route Inventory table.
- Explain every route file, endpoint, function, middleware, validator, workflow, service, and database interaction — nothing skipped.
- Never summarize multiple endpoints into one section. Never omit route files. Never use wildcard endpoint names.
- Continue generating files until every scanned route file has been documented.
- Use clear headings, tables, diagrams, and examples throughout.
- Write in beginner-friendly language while staying technically accurate — the result should read like official framework documentation combined with an in-depth learning guide.