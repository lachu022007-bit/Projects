---
name: Feature-analysis
description: Produces enterprise-grade requirement analysis for any user-provided subject — feature, module, workflow, business requirement, API, database, integration, service, microservice, user story, system component, process, skill, capability, architecture, technical requirement, UI component, background job, event-driven flow, data pipeline, cloud infrastructure, security requirement, DevOps process, or AI/ML workflow. Use this skill whenever the user asks to "analyze," "break down," "document," "spec out," or "write requirements for" any subject, even if they don't say "requirement analysis" explicitly. Automatically detects the subject type and adapts emphasis while preserving full Business Analysis, Solution Architecture, Product Management, Backend Engineering, System Design, and QA documentation depth. Never assumes the subject is a software feature.
---

# Requirement Analysis

A generic, adaptive, enterprise-grade analysis engine. It takes any subject the user provides and produces a complete, structured requirement analysis document — without assuming the subject is a software feature, and without inventing information that wasn't given or reasonably inferable.

## Core Principles (non-negotiable)

- **Never invent requirements.** Only document what is stated, referenced, or a clearly labeled assumption.
- **Never fabricate missing information.**
- **Clearly distinguish facts from assumptions.** Every assumption must be labeled inline as:
  `**Assumption:** ...`
- **If information is unavailable**, write `Not Specified` or `Not Applicable` with a one-line reason — never leave a gap silently or guess.
- **Consolidate every unanswered item** into the Open Questions / Clarifications Needed section — nothing should be silently dropped.
- **Never generate only a success path.** Every flow requires the full set of paths (see Flow Standard below).

## Step 1 — Determine the Subject Type

Before writing any analysis, classify the subject. Do this silently and let it drive emphasis — every section still appears, but depth shifts toward what matters.

Possible subject types (non-exhaustive — infer the closest match):
Business Requirement · Technical Requirement · API · Database · Architecture · Backend Service · Frontend/UI Component · Workflow/Process · Integration · Microservice · Infrastructure/Cloud · Data Pipeline · Event-Driven Flow · Background Job · Security Requirement · DevOps Process · AI/ML Workflow · User Story · System Component · Capability/Skill

State the detected type at the top of the output under **Subject Type**, with a one-line rationale.

**Adaptation rule:** every section in the template below is always present. If a section doesn't apply to this subject type, write:
`Not Applicable — [short reason]`
(e.g., `API Analysis: Not Applicable — subject has no network-exposed interface`, `Database Analysis: Not Applicable — subject is stateless`, `User Flow: Not Applicable — subject has no UI`, `State Transitions: Not Applicable — subject is a single-pass batch process`).

## Step 2 — Generate the Analysis

Use the full template below. It contains the exact section order, the Flow Standard, table schemas (Confidence Summary, Decision Log, etc.), and a worked micro-example.

Terminology must generalize to the subject — do not default to feature/software language. Use whichever of Subject / Requirement / Component / Capability / Process best fits, consistently, throughout the document. E.g. "Subject Summary" not "Feature Summary"; "Purpose / Business Objective" not just "Business Objective"; "Functional Analysis / Functional Requirements" not "Functional Requirements" alone.

## Step 3 — Formatting

- Enterprise-quality Markdown, consistent heading levels.
- Tables wherever data is tabular (confidence, decision log, API inventory, DB schema, test traceability).
- Bullet points for lists; prose kept concise.
- Text-based sequence diagrams always; Mermaid additionally when it adds clarity (flows, ER diagrams, state machines, sequence diagrams).
- No repeated boilerplate between sections — each section adds new information.
- Traceability: functional requirements get FR IDs (FR-001, FR-002, ...); test scenarios reference the FR ID they cover.

## Step 4 — Output

Return the complete analysis as a single document. Do not summarize or truncate sections for brevity — enterprise-grade means complete, even if long. If the input is thin, the document should still contain every section, populated with `Not Specified` / `Not Applicable` as needed, plus a full Open Questions list and Assumption Summary — thinness of input is not a reason to thin the output structure.

---

# Full Template

This is the authoritative section-by-section structure. Every section listed here must appear in every output, in this order. Use `Not Applicable — [reason]` or `Not Specified — [reason]` where content doesn't exist rather than omitting the heading.

---

## 0. Subject Type

- **Detected Subject Type:** (Business Requirement / Technical Requirement / API / Database / Architecture / Backend Service / Frontend Component / Workflow / Integration / Microservice / Infrastructure / Data Pipeline / Event-Driven Flow / Background Job / Security Requirement / DevOps Process / AI/ML Workflow / User Story / System Component / Capability / other)
- **Rationale:** one line on why this classification was chosen.

## 1. Subject Summary

Plain-language description of what the subject is, in context. 3–6 sentences. No jargon overload.

## 2. Purpose / Business Objective

- Why this subject exists / is being requested.
- The problem it solves or value it delivers.
- Success criteria, if known — else `Not Specified`.

## 3. Scope

- **In Scope:** bullet list.
- **Out of Scope:** bullet list.
- **Assumption:** anything inferred about scope boundaries.

## 4. Functional Analysis / Functional Requirements

Numbered, traceable requirements:

| FR ID | Requirement | Notes |
|---|---|---|
| FR-001 | ... | ... |

## 5. Non-Functional Requirements

Cover at minimum: performance, scalability, availability, maintainability, usability, portability, reliability. Mark `Not Specified` per attribute if the user gave no target.

## 6. User Flow

Applicable only if there's a human-facing interaction. Otherwise `Not Applicable — no UI/user-facing interaction`.
Follow the **Flow Standard** (below) — never a happy-path-only flow.

## 7. Backend / System Flow

How the subject behaves internally / server-side / system-to-system. Follow the Flow Standard.

## 8. API Analysis

If `Not Applicable`, state why (e.g., no network interface). Otherwise cover:
- API Inventory (table: Endpoint, Method, Purpose)
- Endpoint Design & HTTP Methods
- Payload Examples (request/response)
- Authentication & Authorization
- Validation rules
- Error Codes (table: Code, Meaning, Trigger)
- Versioning strategy
- Idempotency
- Pagination, Filtering, Sorting
- Rate Limiting
- Retry Strategy
- Backward Compatibility

## 9. Database Analysis

If `Not Applicable`, state why. Otherwise cover:
- Tables / Collections
- Fields (name, type, nullable, default)
- Relationships (1:1, 1:N, N:N)
- Constraints (PK, FK, unique, check)
- Indexes
- Partitioning
- Normalization / Denormalization decisions
- ER Relationship Explanation (text, plus Mermaid ER diagram if useful)
- Data Lifecycle (creation → archival → deletion)

## 10. Business Rules

Enumerated rules governing behavior, independent of implementation.

## 11. Roles & Permissions

Table: Role | Permitted Actions | Restrictions.

## 12. Validations

Field-level and business-level validation rules.

## 13. Edge Cases

Enumerated, each with expected behavior.

## 14. Exception Handling

How exceptions are caught, surfaced, and communicated.

## 15. Error Scenarios

Table: Scenario | Trigger | System Response | User/Caller Impact.

## 16. Integration Points

External/internal systems this subject touches, and the nature of each integration (sync/async, protocol, direction).

## 17. Dependencies

Upstream and downstream dependencies. Mark hard vs. soft dependencies.

## 18. State Transitions

If applicable: state diagram (text + Mermaid `stateDiagram-v2`) with all states and triggers. Else `Not Applicable — subject has no distinct states`.

## 19. Notifications

Events that trigger notifications, channel, recipient, and content summary. Else `Not Applicable`.

## 20. Audit & Logging

What gets logged, at what level, retention, and who can access it.

## 21. Security

Cover: Authentication, Authorization, Encryption (at rest/in transit), Secrets Management, PII handling, Injection risks, XSS, CSRF, Rate Limiting, Audit Trail, Data Exposure, relevant OWASP risks, and a brief Threat Model (assets, actors, attack vectors, mitigations).

## 22. Privacy & Compliance

Applicable regulations (e.g., GDPR, HIPAA, SOC 2) if inferable; otherwise `Not Specified`. Data residency, consent, retention.

## 23. Localization

Language/locale/timezone/currency handling. `Not Applicable` if irrelevant to the subject.

## 24. Accessibility

WCAG-level considerations if there's a UI. Else `Not Applicable`.

## 25. Performance

Caching, Indexing, Lazy Loading, Batch Processing, Concurrency, N+1 query risks, Memory/CPU usage, Network optimization, Scalability levers.

## 26. Cost Impact

Infra cost drivers, licensing, operational cost. `Not Specified` if no data provided, but still name the likely cost dimensions.

## 27. Failure & Recovery

Failure modes and recovery mechanisms (retries, fallbacks, circuit breakers, manual recovery).

## 28. Monitoring

Logs, Metrics, Tracing, Dashboards, Alerts, SLA, SLO, Error Rate, Latency, Availability targets.

## 29. Rollout Strategy

Deployment approach (big-bang, phased, canary, feature-flagged), rollback plan.

## 30. Data Flow

- **Data Source**
- **Input**
- **Validation**
- **Transformation**
- **Storage**
- **Retrieval**
- **Synchronization**
- **Archival**
- **Deletion**

Text description, plus a Mermaid flowchart if it clarifies the pipeline.

## 31. Sequence Diagram

Text-based interaction sequence covering the applicable actors from: User/Caller, Client, API Gateway, Service, Database, External Systems, Notification Services. Omit actors that don't apply and note why. Add a Mermaid `sequenceDiagram` version when useful.

## 32. Decision Log

For every major recommendation made in this document:

| Decision | Reason | Alternatives Considered | Trade-offs |
|---|---|---|---|

## 33. Future Scalability

Expected Growth, Horizontal Scaling, Vertical Scaling, Load Balancing, Partitioning, Sharding, Replication, Caching, Future Architecture Evolution.

## 34. Alternative Design Options

For each significant design decision:

- **Recommended Design:** ...
- **Alternative A:** ... — Advantages / Disadvantages / When to choose
- **Alternative B:** ... — Advantages / Disadvantages / When to choose

## 35. Test Scenarios

Traceable to FR IDs. Cover: Positive, Negative, Boundary, Integration, Security, Performance, Regression, UAT.

| Test ID | FR ID | Type | Scenario | Expected Result |
|---|---|---|---|---|

## 36. Risks

Table: Risk | Likelihood | Impact | Mitigation.

## 37. Improvements

Suggested enhancements beyond current scope, clearly separated from requirements.

## 38. Assumption Summary

Consolidated list of every `**Assumption:**` used anywhere in the document, numbered, each traceable back to its section.

## 39. Open Questions / Clarifications Needed

Every `Not Specified` / ambiguous item across the whole document, consolidated into one list, phrased as a question someone could answer.

## 40. Confidence Summary

| Area | Confidence (High/Medium/Low) | Reason | Missing Information |
|---|---|---|---|
| Requirements | | | |
| Functional Analysis | | | |
| User Flow | | | |
| Backend Flow | | | |
| API | | | |
| Database | | | |
| Security | | | |
| Performance | | | |
| Architecture | | | |
| Test Coverage | | | |

## 41. Final Summary

Short executive wrap-up: what this subject is, its readiness state, the top 2–3 risks/open questions, and recommended next step.

---

## Flow Standard (applies to every flow in sections 6, 7, 18, 30)

Never produce a happy-path-only flow. Every flow must address, at minimum:

1. **Happy Path**
2. **Alternative Path**
3. **Failure Path**
4. **Retry Flow**
5. **Rollback Flow**
6. **Timeout Flow**
7. **Cancellation Flow**
8. **Recovery Flow**
9. **Backflow** (how state/data moves backward if a later step invalidates an earlier one)

If a given path is genuinely not applicable to the subject (e.g., "Rollback Flow: Not Applicable — process is append-only and idempotent"), state that explicitly rather than omitting it.

---

## Worked Micro-Example (illustrative only — do not reuse verbatim)

**Subject:** "Password Reset"
**Detected Subject Type:** Workflow (with API + Database + Notification components)

- Functional Analysis → FR-001: User can request a password reset via email.
- API Analysis → `POST /auth/password-reset-request` ... (full inventory, error codes, rate limiting: max 3 requests / 15 min).
- Database Analysis → `password_reset_tokens` table: `id, user_id, token_hash, expires_at, used_at`.
- Flow (Backend/System) → Happy Path: request → token issued → email sent → user clicks link → token validated → password updated → token invalidated. Failure Path: invalid/expired token → 400 + user-facing message. Retry Flow: user can re-request after cooldown. Rollback Flow: Not Applicable — no partial state to roll back. Timeout Flow: token TTL 15 min → expires → user must re-request. Cancellation Flow: user abandons flow → token remains until TTL, no side effects. Recovery Flow: support can manually invalidate outstanding tokens. Backflow: Not Applicable.
- Security → rate limiting, token hashing at rest, single-use tokens, no user enumeration in error messages.

This example exists only to demonstrate section depth and the Flow Standard — never copy its specifics into a real analysis; always derive content from the actual subject provided.
