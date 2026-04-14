---
name: fc-workshop-analysis
description: Analyzes all workshop materials (transcripts, documents, process diagrams, client system documentation) to produce a structured Requirements Register and Open FDRs. Primary analysis step before solution design begins. Can run incrementally after each session or once after all workshops.
argument-hint: Point to workshop-resources/ directory or a specific session folder. Run after each session for incremental analysis, or after all sessions for full analysis.
tools:
  - Atlassian
---

# fc-workshop-analysis

Transforms raw workshop output into structured, traceable requirements that drive solution design. All requirements must be traceable to their source. This is the primary analysis step before any solution design begins.

## Purpose

Raw workshop output — transcripts, notes, whiteboard photos, client documents — has no structure. This skill converts that material into a Requirements Register, Open FDRs, and an Integration Map that the solution design phase can rely on.

Can run incrementally after each session (recommended) or once after all workshops are complete. Incremental runs allow FDRs to be resolved before the next session, which reduces rework.

## Inputs

- `workshop-resources/workshops/` — transcripts (txt, docx, pdf), session notes, whiteboard photos
- `workshop-resources/client-systems/` — ERP/PMS manuals, database schemas, API documentation, AS-IS process diagrams, org charts
- `workshop-resources/commercial/` — for scope validation cross-reference
- Workshop Guide (Confluence) — cross-check planned topics vs. what was actually covered

Use all available materials. Do not skip client system documentation — it frequently contains business rules and data structures that were not verbalized in workshops.

## Execution Steps

### Step 1 — Inventory materials

Before analysis, list all available materials per session:

| Session | Date | Transcript | Notes | Client Docs | Coverage |
|---|---|---|---|---|---|
| Session 1 | [date] | [file or missing] | [file or missing] | [files or none] | [Full / Partial / Incomplete] |

**Rules:**
- If a session has no transcript and no notes, mark it as **Incomplete** and flag before proceeding.
- If coverage is Partial (e.g. only notes, no transcript), note what may be missing.
- Do not skip sessions. An incomplete session must be flagged, not silently omitted.

### Step 2 — Extract requirements

Read all materials systematically. Extract every instance of:

- **Functional requirements** — what the system must do
- **Non-functional requirements** — performance, volume, availability, language, accessibility, response times
- **Business rules** — conditions, validations, approval thresholds, escalation rules, SLAs, exceptions
- **Pain points** — current-state problems the solution must eliminate or reduce
- **User profiles** — who uses the system, their role, technical sophistication, approximate headcount
- **Process flows** — AS-IS descriptions, even if informal or incomplete
- **Key data entities** — business objects the client manages (and what they call them)
- **Integration requirements** — confirmed third-party systems, data exchanged, direction, frequency
- **Reporting and analytics needs** — what, for whom, how often
- **Data migration needs** — what data, from where, volumes, quality concerns

When a client uses different terminology across sessions for the same concept, note both terms and add to the glossary.

### Step 3 — Structure the Requirements Register

Each requirement gets a unique ID and the following fields:

| REQ-NNN | Description | Type | Source | Priority | Status | Notes |
|---|---|---|---|---|---|---|

**Type values:**
- `Functional` — system behavior
- `Non-Functional` — performance, volume, availability
- `Business Rule` — logic, validation, threshold, SLA
- `Integration` — data exchange with external system
- `Reporting` — report, dashboard, export
- `Security` — access, visibility, compliance
- `Data Migration` — historic data movement

**Priority (MoSCoW) — infer from client language and emphasis:**
- `Must-Have` — expressed as mandatory, blocking, regulatory, or repeated with urgency
- `Should-Have` — expressed as important but with flexibility
- `Nice-to-Have` — expressed as "would be great", "eventually", "if possible"
- `Unknown` — insufficient signal; create an Open FDR to confirm

**Status values:**
- `Clear` — stated consistently by multiple stakeholders or sessions
- `Ambiguous` — stated once or unclearly; needs corroboration
- `Conflicting` — contradicted by another statement; Open FDR required
- `Out of Scope` — mentioned but outside agreed scope; log to Scope Register

**Source format:** `[Session N, ~Xmin]` or `[Doc name, section/page]`

### Step 4 — Create Open FDRs

For every ambiguity, contradiction, or gap, create an Open FDR using `fc-fdr-format`.

Create an FDR for each of the following:

| Trigger | Description |
|---|---|
| Process ambiguity | A process was described inconsistently across sessions or by different stakeholders |
| Business rule gap | A rule was implied but the specifics (threshold, condition, exception) were not stated |
| Conflicting requirements | Two stakeholders described the same process or need differently |
| Integration uncertainty | A third-party system was confirmed but data exchange details are unclear |
| Scope boundary | Unclear whether a requirement is in or out of agreed scope |
| Data ownership | Who owns a record or is responsible for an action was not established |
| Terminology mismatch | The same concept is referred to by different names across sessions |

Do not silently resolve any of the above. Every unresolved item must become an FDR.

### Step 5 — Build the Integration Map

For each third-party system mentioned or confirmed:

| System | Direction | Data Objects | Frequency | Volume (approx.) | Auth Method | System Owner | Status | Notes |
|---|---|---|---|---|---|---|---|---|

**Direction values:** `SF→External` | `External→SF` | `Bidirectional`
**Status values:** `Confirmed` | `Needs Clarification` | `Out of Scope`

If any field is unknown, mark as `TBC` and create an Open FDR for that system.

### Step 6 — Publish to Confluence

Create or update three pages under the project's "1. Discovery" section:

1. **Requirements Register** — full register per the format below
2. **FDRs (Open and Assumed)** — all FDRs created during this analysis
3. **Integration Map** — standalone page with the integration table and per-system detail

On incremental runs, update existing pages rather than creating duplicates. Log the sessions analyzed in the header.

---

## Requirements Register — Confluence Format

```
# Requirements Register — [Project Name]
Generated: [date] | Last updated: [date]
Sessions analyzed: [Session 1 – DD/MM/YYYY, Session 2 – DD/MM/YYYY, ...]
Prepared by: WAM Global Functional Consultant

---

## Coverage Summary

| Functional Area | Requirements Count | Open FDRs | Status |
|---|---|---|---|
| [Area] | [N] | [N] | Complete / In Progress / Not Started |

---

## Functional Requirements

| ID | Description | Source | Priority | Status | Notes |
|---|---|---|---|---|---|

## Business Rules

| ID | Rule Description | Trigger | Source | Priority | Status |
|---|---|---|---|---|---|

## Non-Functional Requirements

| ID | Description | Source | Priority |
|---|---|---|---|

## Reporting & Analytics

| ID | Report / Dashboard Description | Audience | Frequency | Source |
|---|---|---|---|---|

## Integration Requirements

| System | Direction | Data Objects | Frequency | Volume | Auth Method | System Owner | Status | Notes |
|---|---|---|---|---|---|---|---|---|

## User Profiles

| Profile | Role | Process Areas | Technical Level | Approx. Users |
|---|---|---|---|---|

## Key Data Entities & Terminology

| Client Term | Standard Term | Definition | Notes |
|---|---|---|---|

## Data Migration

| ID | Description | Source System | Volume (approx.) | Data Quality Notes | Priority |
|---|---|---|---|---|---|

## Items Flagged as Out of Scope

Requirements mentioned during workshops that fall outside agreed scope.
Log each item to the Scope Register with session source.

| ID | Description | Source | Disposition |
|---|---|---|---|
```

---

## Analysis Rules

- A requirement mentioned once is a candidate — mark **Ambiguous** until corroborated by a second source (another session, a document, or a second stakeholder).
- A requirement mentioned multiple times across sessions or by multiple stakeholders → mark **Clear**.
- When client documents contradict workshop statements → Open FDR immediately. Do not silently resolve contradictions.
- Never invent requirements. If something seems obvious but was not stated → Open FDR to confirm, do not assume.
- Source traceability is mandatory for every requirement. `Workshop 2, ~40min mark` is a valid source. A requirement with no source is not a valid requirement.
- When the client uses inconsistent terminology, log both terms in the Key Data Entities & Terminology table. Never silently normalize terminology — the client's language matters for adoption and training.
- Out-of-scope items must be logged, not discarded. They inform future phases and protect against scope creep disputes.
- Do not group unrelated requirements into a single entry to save space. One requirement = one row.
