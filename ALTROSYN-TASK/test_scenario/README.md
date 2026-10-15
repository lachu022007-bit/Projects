# Test Scenario Analysis Skill

A Claude Code / Copilot Agent skill that turns any feature, module, workflow, API, service, or technical requirement into a full **Business Analyst + Solution Architect + Product Manager + Backend Engineer + QA Lead**-level analysis, with **execution-ready test scenarios** as the centerpiece deliverable.

Give it a one-line feature name or a detailed spec, and it produces a structured 40-section markdown report — data flow, sequence diagrams, API/DB analysis, security, scalability, decision rationale — all feeding into fully traceable Positive / Negative / Boundary / Integration test scenario tables.

---

## Why this exists

Getting from "here's a feature" to "here's the full set of test scenarios QA should run" usually means switching hats along the way — BA, architect, backend engineer, QA lead — and manually chasing down flows, edge cases, API contracts, and error paths before a single test case can be written responsibly. This skill automates that entire chain into one traceable document, so every test scenario is grounded in an explicit functional requirement (FR-ID) instead of guesswork.

---

## What it produces

A single markdown report with **40 sections**, always in the same order, including:

- Subject Summary, Purpose, Scope (in/out table)
- Functional Requirements — each tagged with a traceability ID (`FR-01`, `FR-02`, ...)
- Non-Functional Requirements
- User Flow, Backend/System Flow, Data Flow, Sequence Diagram (text-based, Mermaid on request)
- API Analysis — endpoint list, request/response samples, auth, validations, error codes, versioning
- Database Analysis — tables, fields, relationships, constraints
- Business Rules, Roles & Permissions, Validations, Edge Cases, Exception Handling, Error Scenarios
- Integration Points, Dependencies, State Transitions, Notifications, Audit & Logging
- Security, Data Privacy & Compliance, Localization & Accessibility
- Performance, Cost & Resource Impact, Failure & Recovery, Monitoring & Metrics
- Future Scalability, Rollout Strategy
- Decision Log and Alternative Design Options (with trade-off tables)
- **Test Scenarios** — four execution-ready tables (Positive / Negative / Boundary / Integration), each row carrying Test ID, FR-ID, test data/preconditions, environment/dependencies, steps, expected result, automation feasibility, and regression classification
- Risks & Mitigations, Improvement Suggestions
- Open Questions, Assumption Summary
- Confidence Summary (per section area: High/Medium/Low + reasoning)
- Final Summary

See [`Post_Scheduling_Requirement_Analysis.md`](./Post_Scheduling_Requirement_Analysis.md) for a full worked example generated from just the input `"Post Scheduling"`.

---

## How it works

1. **Never invents the subject.** The skill only analyzes what's explicitly provided. If input is minimal, it proceeds using clearly labeled `**Assumption:**` tags rather than stopping to ask questions — unless no subject is given at all.
2. **Adapts to subject type.** APIs, workflows, architectures, and stateless processes each get the right sections — irrelevant sections (e.g., API Analysis for a non-API process) are marked **Not Applicable** with a reason, never silently dropped.
3. **Traces everything.** Every functional requirement gets an FR-ID, and every test scenario references back to the FR-ID(s) it validates.
4. **Separates fact from assumption.** Every assumption is labeled inline and consolidated in the Assumption Summary; every gap is logged in Open Questions.
5. **Ends with a confidence check.** A per-area confidence table (Requirements, API, DB, Security, Architecture, Performance, Test Coverage) tells you where the analysis is solid and where it's speculative.

---

## Usage

Trigger it with prompts like:

- "Analyze this requirement: `<feature/module/API/etc.>`"
- "Break down the design for X"
- "Give me the flow, edge cases, and trade-offs for Y"
- Paste a user story, API spec, or one-line feature name

Output is delivered as a markdown file by default. On request, it can also:
- Provide Mermaid-diagram versions of any flow/sequence
- Add an **Execution Status** column (Pass/Fail/Blocked/Not Run) to Test Scenarios for use as a live QA tracker

---

## Notes

- This skill produces **documentation, not code** — no automation scripts or executable test cases.
- Best results come from giving it as much real context as you have (existing endpoints, schema, roles); minimal input still works but leans more heavily on labeled assumptions.
- Designed to be enterprise-review-ready: consistent structure, tables wherever content is tabular, and full traceability from requirement → test.

---

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill definition — trigger conditions, 40-section output spec, formatting rules, workflow |
| `Post_Scheduling_Requirement_Analysis.md` | Example output generated from a single-line input (`"Post Scheduling"`), test scenarios included |
