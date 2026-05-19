---
name: fc-fdr-format
description: Defines the Functional Decision Record (FDR) format used by all functional consultant skills to track design decisions, client-confirmed requirements, consultant assumptions, and open questions throughout the engagement.
---

# Functional Decision Record (FDR) Format

A Functional Decision Record captures any design decision, assumption, or open question that arises during the engagement. Every assumption made by the consultant MUST be an FDR — never buried in prose.

## What is an FDR

An FDR is the audit trail of design reasoning. It exists to make design decisions visible, traceable, and reviewable — by the client, by WAM Global, and by any consultant who joins the engagement later.

## Status Values

- **Confirmed** — Explicitly validated with the client (in a workshop, written communication, or sign-off)
- **Assumed** — Inferred by the consultant as the most reasonable interpretation. Not yet confirmed by the client. Must be surfaced in the Functional Document for client review.
- **Open** — Unresolved. Requires a decision before the Functional Document can be signed off.

## When to Create an FDR

Create an FDR when:

- A client statement contradicts another client statement or document, and both positions have legitimate business backing
- A design decision is counter-intuitive, goes against standard Salesforce practice, or could surprise the client at sign-off
- A process detail is genuinely missing and no reasonable default exists — design cannot proceed without an explicit answer
- A scope boundary is actively disputed across materials or stakeholders

Do NOT create an FDR when a clear, sensible default or standard pattern applies. In those cases, apply the default and document the choice directly in the Solution Overview. The user will correct if wrong.

## Individual FDR Entry Format

```markdown
## FDR-[NNN] — [Short descriptive title]

**Status:** Confirmed | Assumed | Open
**Date:** YYYY-MM-DD
**Source:** Workshop [N] | Commercial document | Design discussion | Requirement [REQ-NNN]

### Context
[Why this decision was needed. What ambiguity or gap triggered it. 2-4 sentences.]

### Decision
[The confirmed decision or working assumption. Clear, specific, business language.]

### Consequences
[What this means for the solution. What alternatives are now ruled out.]

### Pending *(only if Status = Open)*
[What specific answer is needed. Who needs to provide it.]

### Revision History *(only if the FDR has been updated after initial creation)*
| Date | Change | Trigger |
|---|---|---|
| [YYYY-MM-DD] | [What changed in Decision, Status, or Consequences] | [CL-ID if triggered by a scope change, or "Design review"] |
```

## Full FDR Document Structure

```markdown
# Functional Decision Records — [Project Name]
Generated: [date] | Last updated: [date]
Sources: Requirements Register, Workshop sessions [list], Functional Document v[X]

---

## Status Summary
| FDR | Title | Status | Source | Date |
|---|---|---|---|---|

---

## Open FDRs *(resolve before Functional Document sign-off)*

[FDR entries with Status = Open]

---

## Assumed FDRs *(surface to client for confirmation)*

[FDR entries with Status = Assumed]

---

## Confirmed FDRs

[FDR entries with Status = Confirmed]
```

## Rules

- FDRs are numbered sequentially (FDR-001, FDR-002…) regardless of status changes
- One decision per FDR — never bundle multiple decisions
- When an Assumed FDR is confirmed by the client, update status to Confirmed and record the confirmation date and source
- All Open FDRs must be resolved before the Functional Document is presented for sign-off
- Every Assumed FDR must appear explicitly in the Functional Document (Section 3.3) so the client can accept or reject the assumption
- Changing an FDR's Decision requires updating the version and noting what changed
- When an FDR's Decision or Status changes after the Functional Document has been signed off, add a Revision History entry referencing the CL-ID from the Change Log. This links the FDR change to its contractual justification.
