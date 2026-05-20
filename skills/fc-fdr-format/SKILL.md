---
name: fc-fdr-format
description: Defines the Functional Decision Record (FDR) format used by all functional consultant skills to document complex or counter-intuitive design decisions throughout the engagement.
---

# Functional Decision Record (FDR) Format

A Functional Decision Record documents a design decision that is complex, counter-intuitive, or significant enough that it could surprise the client or the implementation team if not made explicit. FDRs are the audit trail of non-obvious reasoning — they exist so that every important decision is visible, traceable, and reviewable.

## What is an FDR

An FDR is **not** a log of every decision made during the project. It captures decisions that required deliberate judgment — where a reasonable person could have chosen differently, where Salesforce standard practice was consciously set aside, or where a disagreement between stakeholders forced an explicit choice.

**FDRs are not the mechanism for resolving ambiguous requirements.** Ambiguous requirements are handled directly: ask the consultant for clarification or propose a concrete interpretation. Only create an FDR when the decision itself is non-trivial and worth documenting for future reference.

## Status Values

- **Open** — decision identified but not yet taken. The affected design area is on hold until this FDR is closed.
- **Closed** — decision taken and documented. The solution proceeds on this basis.

All FDRs must be **Closed** before the Functional Document can be generated or signed.

## When to Create an FDR

Create an FDR when:

- A client statement contradicts another client statement or document, and both positions have legitimate business backing — a real decision must be made between them
- A design choice is counter-intuitive, deviates from standard Salesforce practice, or could surprise the client or the architect at sign-off
- A scope boundary is actively disputed and requires an explicit decision from both parties

Do **not** create an FDR when:

- A requirement is ambiguous → ask the consultant directly or propose a clear interpretation; update the requirement once confirmed
- A clear, sensible default or standard pattern applies → apply it and document the choice directly in the Solution Overview
- A detail is simply unknown but a standard assumption is reasonable → proceed and note the assumption in the relevant Solution Overview section

## Individual FDR Entry Format

```markdown
## FDR-[NNN] — [Short descriptive title]

**Status:** Open | Closed
**Date opened:** YYYY-MM-DD
**Date closed:** YYYY-MM-DD *(only if Status = Closed)*
**Source:** Workshop [N] | Commercial document | Design discussion | Requirement [REQ-NNN]

### Context
[Why this decision was needed. What tension, contradiction, or non-obvious situation triggered it. 2–4 sentences.]

### Decision
[The decision taken. Clear, specific, in business language. Only present if Status = Closed.]

### Consequences
[What this means for the solution. What alternatives are now ruled out. Only present if Status = Closed.]

### Pending *(only if Status = Open)*
[What specific answer or decision is needed. Who is responsible for providing it.]

### Revision History *(only if the FDR has been updated after initial creation)*
| Date | Change | Trigger |
|---|---|---|
| [YYYY-MM-DD] | [What changed] | [CL-ID if triggered by a scope change, or "Design review"] |
```

## Full FDR Document Structure

```markdown
# Functional Decision Records — [Project Name]
Generated: [date] | Last updated: [date]
Sources: Requirements Register, Workshop sessions [list], Functional Document v[X]

---

## Status Summary
| FDR | Title | Status | Date Opened | Date Closed |
|---|---|---|---|---|

---

## Open FDRs *(must be resolved before Functional Document generation)*

[FDR entries with Status = Open]

---

## Closed FDRs

[FDR entries with Status = Closed]
```

## Rules

- FDRs are numbered sequentially (FDR-001, FDR-002…) regardless of status changes
- One decision per FDR — never bundle multiple decisions into a single entry
- All Open FDRs must be Closed before the Functional Document is presented for sign-off
- When an FDR's Decision or Status changes after the Functional Document has been signed off, add a Revision History entry referencing the CL-ID from the Change Log
