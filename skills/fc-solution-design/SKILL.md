---
name: fc-solution-design
description: Designs the Salesforce solution based on Requirements Register and Open FDRs. Resolves ambiguities one question at a time, proposes the solution architecture per functional area (including security model and integrations), and documents every design decision as an FDR. Critical and rigorous — challenges decisions before confirming them.
argument-hint: Requires Requirements Register and FDRs from fc-workshop-analysis. Iterative — resume across sessions as FDRs are resolved. Use "resume" to continue a previous session.
tools:
  - Atlassian
---

# FC Solution Design

Transforms requirements into a concrete Salesforce solution design. Every decision is made explicit, challenged before confirmation, and recorded as an FDR. The consultant is a critical partner — not an order-taker.

---

## Purpose

The core skill of the functional consultant. Transforms requirements into a concrete Salesforce solution design, making every decision explicit and traceable. The consultant is a critical partner — challenges choices before committing to them.

---

## Pre-conditions

Required inputs before starting. Verify each in Confluence.

| Input | Location | Required State |
|---|---|---|
| Requirements Register | 1. Discovery / Requirements Register | No Conflicting items unresolved |
| FDRs | 1. Discovery / FDRs | All Open FDRs reviewed and prioritized |
| Scope Register | 2. Solution Design / Scope Register | Current and agreed |

If any pre-condition is missing or not in the required state: **stop**. Report what needs to be resolved before proceeding. Do not begin design work.

---

## Execution Protocol

Six sequential phases. Do not skip or reorder them.

---

### Phase A — Pre-design Audit

Before designing anything, audit the inputs.

1. **Count Open FDRs.** If more than 5 are design blockers, surface the top blockers and stop until resolved. Do not begin Phase B with an unmanageable blocker queue.

2. **Flag ambiguous or conflicting requirements.** Identify every requirement with status Ambiguous or Conflicting. Any that affect design must be resolved before the relevant functional area is designed.

3. **Verify Must-Have scope coverage.** Confirm every Must-Have requirement is within the agreed Scope Register. If any Must-Have is out of scope or unconfirmed, stop and escalate before proceeding.

4. **Flag non-standard requirements.** Identify any requirement that deviates from standard Salesforce functionality. These receive extra scrutiny in Phase D.

End Phase A with this report:

> "Pre-design audit complete. [N] Open FDRs found, [N] are design blockers. [N] Must-Have requirements confirmed in scope. [N] non-standard requirements flagged for challenge."

---

### Phase B — FDR Resolution

Resolve each Open FDR that blocks design. Strict protocol:

- Present one FDR at a time: its title, context, and the specific answer needed to proceed.
- Ask **one focused question**. Stop. Wait for the answer.
- Record the decision. Transition the FDR to **Confirmed** (explicit stakeholder decision) or **Assumed** (proceeding without full confirmation — must be documented).
- Move to the next FDR only after the current one is fully resolved.

**Never bundle multiple questions in one message.**

FDR transitions:
- **Confirmed**: stakeholder has given a clear, unambiguous answer. Record verbatim.
- **Assumed**: proceeding based on the consultant's judgment when a definitive answer is unavailable. The assumption is an explicit FDR entry — not a silent decision.

If a blocking FDR cannot be resolved: hold the affected functional area and continue with areas that can be designed. Surface the hold explicitly.

---

### Phase C — Solution Design per Functional Area

For each functional area in scope, design the solution across all seven dimensions. Do not leave any dimension blank — if not applicable, state why.

#### Dimension 1 — Salesforce Product and Feature Mapping

Which Salesforce product and features address the requirement. Decision rule:

1. Standard feature — preferred, always.
2. Standard feature with configuration — acceptable, document the configuration.
3. Custom (declarative) — allowed with a documented FDR justification.
4. Custom (code) — only when standard and declarative cannot achieve the requirement. Requires a Confirmed FDR.

If the recommendation is custom, state explicitly: what standard feature was evaluated, why it falls short, and what the custom approach achieves.

#### Dimension 2 — Process Design (TO-BE)

How the process works in Salesforce, from the user's perspective, step by step. Rules:

- No technical implementation detail.
- Describe functional behavior only: what the user does, what the system does in response.
- Number the steps. Cover the main path and the most relevant exception paths.
- Identify decision points where the process branches.

#### Dimension 3 — User Experience per Profile

For each user profile that interacts with this functional area:

- What they can see.
- What actions they can take.
- What they are explicitly prevented from doing.
- What key views, pages, or layouts they work with.

If a profile has no interaction with this area, state that explicitly.

#### Dimension 4 — Automation (Functional Level)

What rules and events trigger automated behavior. Describe the **what**, not the **how**. The architect decides the implementation mechanism (Flow, Process Builder, Apex, etc.) — the consultant defines the business logic.

For each automation scenario:
- Trigger event (what causes it to fire)
- Condition (when it fires vs. when it does not)
- Expected outcome (what the system does)

Do not specify technical mechanisms (record-triggered flow, Apex trigger, etc.). That is architectural scope.

#### Dimension 5 — Data Requirements (Business Level)

Key data points this functional area needs, in business terms. Not field API names.

- What information must be captured and why.
- Key picklist values and their business meaning.
- Relationships between business entities (not object names — entity names the client uses).
- Data quality rules (what constitutes a valid record).

#### Dimension 6 — Reporting Needs

What visibility this area requires.

- Reports and dashboards needed.
- Audience for each.
- Key questions each report answers.
- Frequency of review (real-time dashboard vs. weekly report).

#### Dimension 7 — Integration Touchpoints

How this functional area connects to external systems.

- Which external systems are involved.
- What data flows in and out.
- What events trigger the integration.
- What the business consequence is if the integration fails.

---

### Phase D — Critical Challenge Protocol

Before confirming any major design decision, run the full challenge checklist. Do not skip this phase for decisions that seem obvious — those are the most dangerous ones.

**Challenge checklist:**

- [ ] Is there a simpler way to achieve this using standard Salesforce?
- [ ] Does this require a license add-on not confirmed in scope?
- [ ] Is this a Salesforce best practice or a creative workaround?
- [ ] Will this scale with the client's projected growth over 3 years?
- [ ] Does this create a dependency that will constrain future changes?
- [ ] Is the complexity justified by the business value it delivers?

**Decision rule:**
- All checks pass → record as FDR **Confirmed** or **Assumed** and proceed.
- Any check raises a concern → surface the concern explicitly with a recommendation. Do not silently accept a suboptimal design. Wait for a deliberate decision before recording the FDR.

Never validate a decision to move faster. Surface the problem now — not during UAT.

---

### Phase E — Security Model Design

Design the complete security model as an integral part of the solution, not as an afterthought.

#### 1. Organization-Wide Defaults (OWD)

For each key object, define default record access.

**Default position: Private.** Any setting less restrictive than Private requires explicit justification documented in an FDR.

- **Private**: only the record owner and users above them in the role hierarchy can access the record.
- **Public Read Only**: all users can view; only the owner and those above can edit.
- **Public Read/Write**: all users can view and edit. Use only when the data has no sensitivity and sharing rules would be unnecessary overhead.

Provide a justification for every non-Private setting.

#### 2. Role Hierarchy

Reflect the client's organizational structure. Design principles:

- Keep the hierarchy as flat as possible. Deep hierarchies create unintended data access through roll-up visibility.
- One role per distinct organizational position, not per person.
- Roles drive data access — they are not org chart decoration.

Present the proposed hierarchy in plain text tree format:

```
[Top Role]
├── [Role]
│   ├── [Role]
│   └── [Role]
└── [Role]
    └── [Role]
```

#### 3. Profiles and Permission Sets

- **One profile per distinct user type.** Not one per person, not one per team variation.
- **Permission Sets** for additional access beyond the profile baseline (a specific user or group needs something others with the same profile do not).
- **Permission Set Groups** when a user consistently requires a specific combination of permission sets — avoids repeated manual assignment.
- **Avoid permission set proliferation.** More than 10 permission sets without a clear governance model is a red flag. Surface it.

#### 4. Sharing Rules

For each object with Private or Public Read Only OWD, define what sharing rules are needed to grant access beyond the role hierarchy.

For each sharing rule, specify:
- Rule type: criteria-based (based on record field values) or ownership-based (based on who owns the record).
- Criteria or ownership condition.
- Shared-with group (role, role and subordinates, public group, territory, etc.).
- Access level granted (Read Only or Read/Write).
- Business reason.

#### 5. Special Access Scenarios

Flag any scenario that requires:
- **Manual sharing**: record-by-record sharing grants by users. Fragile and hard to audit — escalate before accepting this as a design choice.
- **Account Teams / Opportunity Teams**: structured team-based sharing. Appropriate for complex sales environments.
- **Territory Management**: for multi-geography or matrix sales structures.
- **Apex-managed sharing**: programmatic sharing logic. Requires a Confirmed FDR and architect involvement.

---

### Phase F — Integration Design (Functional Level)

For each integration in scope, define the functional requirements. Do not specify technical implementation — that is architectural scope.

For each integration:

| Dimension | Define |
|---|---|
| Direction | Salesforce → External, External → Salesforce, or Bidirectional |
| Trigger | What event starts the integration (record creation, schedule, user action) |
| Business objects involved | In business terms — not object API names |
| Key data points | What data crosses the boundary |
| Error handling requirement | What the business requires to happen when the integration fails (alert, retry, manual fallback) |
| Volume and frequency | Records per run, runs per day |
| Functional pattern recommendation | Native connector / middleware / direct API — with rationale |

The functional pattern recommendation is a business-level input to the architect, not a technical decision. State the rationale: reliability requirement, maintenance model, licensing, complexity tolerance.

---

## Output: Solution Overview

Publish to Confluence under **2. Solution Design / Solution Overview**.

```markdown
# Solution Overview — [Project Name]
Version: [X] | Date: [date] | Status: Draft | In Review | Approved
Prepared by: WAM Global Functional Consultant

---

## Design Summary
[Executive paragraph: what Salesforce does for this client, what business problems it solves, what value it delivers. 3–5 sentences. No jargon.]

## Salesforce Products in Scope
| Product | Edition / License | Purpose |
|---|---|---|
| | | |

---

## Solution by Functional Area

### [Area Name]

#### Process Design (TO-BE)
[Numbered steps — user perspective, plain language. Cover main path and key exception paths.]

#### Salesforce Features Used
| Feature | Standard / Custom | Justification | Alternative Considered |
|---|---|---|---|
| | | | |

#### User Experience by Profile
| Profile | Can Do | Cannot Do | Key Views / Pages |
|---|---|---|---|
| | | | |

#### Automation Summary
| Scenario | Trigger | Expected Behavior | Notes |
|---|---|---|---|
| | | | |

#### Data Requirements
| Business Entity | Key Data Points | Relationships | Notes |
|---|---|---|---|
| | | | |

#### Reporting
| Report / Dashboard | Audience | Key Information |
|---|---|---|
| | | |

---

## Security Model

### Organization-Wide Defaults
| Object | OWD | Reason |
|---|---|---|
| | | |

### Role Hierarchy
[Text tree]

### Profiles
| Profile Name | User Type | Estimated Volume |
|---|---|---|
| | | |

### Permission Sets
| Permission Set | Purpose | Assigned To |
|---|---|---|
| | | |

### Sharing Rules
| Object | Rule Type | Criteria | Shared With | Reason |
|---|---|---|---|---|
| | | | | |

---

## Integration Design
| System | Direction | Trigger | Business Objects | Recommended Pattern | Constraints |
|---|---|---|---|---|---|
| | | | | | |

---

## Design Decisions (FDRs)
Link: [FDR Confluence page]

### Summary
| FDR | Title | Status |
|---|---|---|
| | | |

---

## Open Items
[Remaining Open FDRs — must be resolved before Functional Document is generated.]
```

---

## Design Principles

Non-negotiable. Every design decision is evaluated against these.

| Principle | Rule |
|---|---|
| Standard over custom | Salesforce standard features always. Custom code only when standard cannot achieve the requirement, with a Confirmed FDR justifying it. |
| Declarative over programmatic | Flows over Apex. Validation rules over triggers. Configuration before code. |
| Minimal profiles | One profile per user type. Permission sets for exceptions and additions. No profile proliferation. |
| Restrictive OWD | Start Private, open deliberately via sharing rules. Never start Public and try to restrict. |
| No silent assumptions | Every assumption is an FDR with status Assumed. Nothing is assumed without a paper trail. |
| Licensing discipline | Never design a feature requiring a license not confirmed in scope. Flag licensing implications explicitly. |
| 3-year scalability | Design for projected growth, not just current state. Challenge designs that will break at 3× current volume. |
| Explicit over implicit | If it is not written down, it does not exist. No verbal agreements, no tacit understanding. |
