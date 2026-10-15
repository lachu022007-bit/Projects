---
name: requirement-analysis
description: Use this skill whenever the user asks to analyze, document, or break down anything they provide — a feature, module, workflow, business requirement, API, database, integration, service, microservice, user story, system component, process, skill, capability, architecture, or technical requirement of any kind. Produces a full Business Analyst / Solution Architect / Product Manager / Backend Engineer / QA Lead-level analysis covering data flow, sequence diagrams, API analysis, database analysis, business rules, roles & permissions, edge cases, security, privacy & compliance, performance, scalability, decision rationale, alternative designs, and execution-ready test scenarios. Trigger this for requests like "analyze this," "break down this requirement," "what would the design look like for X," "give me the flow, edge cases, and trade-offs for Y," or any request for a structured requirements/technical analysis document — regardless of domain. Do NOT invent or assume the subject matter yourself — always wait for the user to supply it, and clearly flag any missing information as a labeled assumption rather than fabricating detail.
---

# Requirement Analysis

Acts as a combined Business Analyst, Solution Architect, Product Manager, Backend Engineer, and QA Lead to produce one comprehensive analysis document for anything the user provides — a feature, module, workflow, business requirement, API, database, integration, service, microservice, user story, system component, process, skill, capability, architecture, or any other technical or business requirement. The subject under analysis is referred to generically below as "the subject."

## Core rule

**Never assume or invent the subject matter.** Only analyze what the user has explicitly provided. If information needed for a section is missing:
- Do not fabricate it.
- State the gap explicitly under that section (e.g., "Not specified by user") and, where reasonable, propose a labeled assumption: `**Assumption:** ...`
- Collect every gap in "Open Questions / Clarifications Needed," and consolidate every assumption made anywhere in the document in "Assumption Summary."
- Always distinguish stated facts from assumptions — never let one read as the other.

If the user's input is minimal (a short name or one-line description), proceed using clearly labeled assumptions throughout rather than stopping to ask — unless no subject at all was provided, in which case ask for it.

## Adaptive behavior

Not every subject needs every section in full. Adapt without dropping structure:

- If a section genuinely does not apply, write **"Not Applicable"** with a brief reason — never omit a section silently.
- Do not force an API Analysis onto a process or workflow with no API surface — mark it Not Applicable.
- Do not force a Database Analysis onto a stateless subject — mark it Not Applicable.
- Do not force a User Flow onto a backend-only or system-to-system subject — reframe it as the primary trigger-to-outcome flow, or mark Not Applicable if there is no external interaction at all.
- Adapt terminology automatically to the subject type: "endpoint" for an API, "message contract" for an event-driven integration, "screen/step" for a user story, "control" for a process, "component" for an architecture — never force feature-specific vocabulary onto a non-feature subject.

## Output structure

Produce the analysis with ALL sections below, in order, adapted to the subject. Tag each functional requirement with a traceability ID (FR-01, FR-02, ...) and reference these IDs in Test Scenarios for full traceability.

1. **Subject Summary** — 2-4 sentences: what the subject is, its type (feature, process, architecture, etc.), and who uses or is affected by it.
2. **Purpose / Business Objective** — why the subject exists and what problem or goal it addresses.
3. **Scope** — table: In Scope | Out of Scope.
4. **Functional Analysis / Functional Requirements** — numbered with traceability IDs (FR-01, FR-02...); for technical subjects, express as capabilities/behaviors rather than user-facing features where appropriate.
5. **Non-Functional Requirements** — performance, scalability, availability, usability, maintainability, etc.
6. **User Flow / Interaction Flow** — text-based flow of the primary path a user, consumer, or caller takes. For subjects with no direct interaction, describe the trigger-to-outcome flow instead; mark Not Applicable only if truly no external interaction exists.
7. **Backend/System Flow** — text-based flow of internal processing, including the reverse/failure path: rollback, retry, cancellation, undo, timeout.
8. **Data Flow** — how data moves through the subject:
   - Source
   - Input
   - Validation
   - Transformation
   - Storage
   - Retrieval
   - Synchronization
   - Archival
   - Deletion
   Mark Not Applicable with reason if the subject is not data-bearing.
9. **Sequence Diagram** — text-based sequence of interactions across the parties relevant to the subject (omit any party that doesn't apply): User/Caller, Client/UI, API Gateway, Service, Database, External Systems, Notification Services.
10. **API Analysis**
    - API List (table: Endpoint | Method | Purpose)
    - Endpoints (path detail)
    - Methods (or equivalent operations for non-REST interfaces) and rationale
    - Request/Response (sample payload shapes)
    - Authentication (scheme, token type, scopes)
    - Validations (per endpoint/operation)
    - Error Codes (table: Code | Meaning | Trigger condition)
    - Versioning & Backward Compatibility (rollout of breaking changes, deprecation window, sunset policy)
    - Not Applicable if the subject has no API surface.
11. **Database Analysis**
    - Required Tables/Collections
    - Fields (table: Field | Type | Constraints | Notes)
    - Relationships (foreign keys, cardinality)
    - Constraints (unique, not-null, check constraints, indexes)
    - Not Applicable if the subject has no persistence layer.
12. **Business Rules** — domain logic, calculations, conditional behavior governing the subject.
13. **Roles & Permissions** — table: Role | Allowed Actions | Restrictions.
14. **Validations** — input-level and business-level rules, distinct from the endpoint-scoped validations in API Analysis.
15. **Edge Cases** — boundary values, empty states, concurrency, duplicate submissions, race conditions, and other atypical conditions.
16. **Exception Handling** — how each exception/error type is caught and handled.
17. **Error Scenarios** — user- or consumer-facing error conditions and messaging.
18. **Integration Points** — other systems, modules, or services this subject touches.
19. **Dependencies** — upstream/downstream dependencies, third-party services or libraries.
20. **State Transitions** — state machine table/diagram if the subject has status or lifecycle; Not Applicable if stateless.
21. **Notifications** — email/SMS/push/in-app/webhook triggers, including localization needs; Not Applicable if none exist.
22. **Audit & Logging** — what gets logged, retention, traceability needs.
23. **Security Considerations** — authentication/authorization, data exposure, injection risks, encryption.
24. **Data Privacy & Compliance** — PII/payment/health data flags and applicable regimes (GDPR, PCI-DSS, HIPAA, DPDP Act, etc.), or "Review needed" / "Not Applicable."
25. **Localization & Accessibility** — i18n (date/currency formats, RTL, translated strings) and accessibility (keyboard nav, screen-reader labels, contrast) where a UI exists; Not Applicable otherwise.
26. **Performance Considerations** — expected load, query/processing efficiency, caching, indexing needs.
27. **Cost & Resource Impact** — third-party API/cloud costs, rate limits, quota-exhaustion behavior; Not Applicable if no meaningful cost driver exists.
28. **Failure & Recovery Scenarios** — partial-failure behavior and recovery/compensation steps.
29. **Monitoring & Metrics** — what to track and alert on once live or in use.
30. **Future Scalability** —
    - Expected Growth
    - Horizontal vs Vertical Scaling
    - Load Balancing
    - Partitioning
    - Sharding
    - Caching Strategy
    - Future Architecture Evolution
31. **Rollout Strategy** — feature flag, phased rollout, A/B test, staged deployment, or direct release; rollback plan.
32. **Decision Log** — for every major architectural or design recommendation, a table: Decision | Reason | Alternatives Considered | Trade-offs.
33. **Alternative Design Options** — for significant implementation decisions: Recommended Design | Alternative A | Alternative B | Pros | Cons | When Each Option Is Appropriate.
34. **Test Scenarios** — execution-ready, not just descriptive. Produce four tables — Positive | Negative | Boundary | Integration — each with the following mandatory columns:

    | Test ID | Scenario | FR-ID | Test Data / Preconditions | Environment / Dependencies | Steps | Expected Result | Automation | Regression | Priority |
    |---|---|---|---|---|---|---|---|---|---|
    | TS-01 | ... | FR-01 | ... | ... | ... | ... | Automatable / Manual-only | Yes / No | High/Medium/Low |

    - **FR-ID:** the functional requirement this scenario validates — every scenario must trace back to one.
    - **Test Data / Preconditions:** the exact fixture or system state needed to run the test (e.g., "expired JWT," "account locked after 5 failed attempts," "seeded record with zero stock") — never leave this implicit.
    - **Environment / Dependencies:** whether the scenario needs a specific environment (staging vs. prod-like), a mocked third-party service, or seeded DB/queue state to execute.
    - **Automation:** mark "Manual-only" for anything inherently unreliable to automate (CAPTCHA, bot-detection-sensitive flows, visual/perceptual checks) rather than defaulting everything to automatable.
    - **Regression:** "Yes" for scenarios protecting core, high-risk, or frequently-broken behavior that should re-run every release; "No" for one-off/exploratory checks.
    - If the user indicates they want a living tracker rather than a static report, add an **Execution Status** column (Pass / Fail / Blocked / Not Run) so the table can be pasted directly into a spreadsheet-based QA tracker.
    - Out of scope for this section: performance/load testing (covered in Performance Considerations) and automation script generation (this section defines scenarios, not test code).
35. **Risks & Mitigations** — table: Risk | Impact | Mitigation.
36. **Improvement Suggestions** — optional enhancements beyond stated scope.
37. **Open Questions / Clarifications Needed** — every gap flagged during the analysis.
38. **Assumption Summary** — every `**Assumption:**` made anywhere in the document, consolidated into one list/table for quick review.
39. **Confidence Summary** — for each of Requirements, Functional Analysis, API Analysis, Database Analysis, Security, Architecture, Performance, and Test Coverage, provide a table with:
    - Confidence Level (High / Medium / Low)
    - Reason for Confidence
    - Missing Information Affecting Confidence
40. **Final Summary** — 3-5 sentence wrap-up of the overall readiness/maturity of the subject as analyzed.

## Formatting rules

- Use tables wherever content is naturally tabular (roles, fields, error codes, risks, test cases, decisions, alternatives, confidence). Don't force prose into a table or vice versa.
- Use text-based flow/sequence diagrams for User/Interaction Flow, Backend/System Flow, Data Flow, and Sequence Diagram, e.g.:

```
[Entry point / trigger]
        |
        v
[Primary action] --(invalid input)--> [Validation error] --> back to entry
        |
        v
[Core processing step] --(call)--> [Downstream system/service]
        |
        v
[Success] --> [Expected outcome]
        |
        v (failure)
[Error raised] --(retry)--> back to entry
        |
        v (repeated failure)
[Escalation / lockout / circuit-break] --> [Notification triggered]
```

Sequence diagram example:

```
User/Caller -> Client/UI: initiates action
Client/UI -> API Gateway: sends request
API Gateway -> Service: routes request
Service -> Database: reads/writes data
Service -> External System: calls dependency (if applicable)
Service -> Notification Service: triggers alert (if applicable)
Service -> Client/UI: returns response
```

- Offer a Mermaid-diagram version of any flow or sequence on request, in addition to the text version.
- Label every assumption inline: `**Assumption:** ...` and mirror it in the Assumption Summary.
- Explain non-obvious design decisions briefly in-line, and capture the full rationale in the Decision Log.
- Always cover both the success path and the alternative/failure path for every flow and error-related section.
- Call out cross-module, cross-service, or cross-system dependencies wherever they surface, not only in Dependencies.
- Thread security, compliance, scalability, and maintainability considerations through every relevant section rather than confining them to their named sections.
- Keep language concise and enterprise-standard; avoid repeating the same point across multiple sections.

## Workflow

1. Wait for the user to supply the subject. Do not proceed without it.
2. Identify the subject's type (business-facing, technical/architectural, data-centric, process-only, etc.) so section emphasis can adapt without dropping structure.
3. Identify what's explicitly stated versus missing.
4. Draft the full 40-section analysis, applying "Not Applicable" where genuinely appropriate, labeling assumptions inline, and tagging FR-IDs for traceability.
5. Build Test Scenarios as execution-ready tables — every scenario carries test data/preconditions, environment/dependency notes, automation feasibility, and regression classification, not just a description.
6. Consolidate every assumption into Assumption Summary and every open gap into Open Questions.
7. Complete the section-specific Confidence Summary last, once the full document is drafted.
8. Deliver as a markdown file unless the user asks for it inline in chat. Offer Mermaid versions of flows/sequences, and an Execution Status column for Test Scenarios, on request.
