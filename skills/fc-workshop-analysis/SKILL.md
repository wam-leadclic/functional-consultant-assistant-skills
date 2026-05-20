---
name: fc-workshop-analysis
description: Analyzes all workshop materials (transcripts, documents, process diagrams, client system documentation) to produce a structured Requirements Register, Open FDRs, and Integration Map. Primary analysis step before solution design begins. Can run incrementally after each session or once after all workshops.
tools:
  - Atlassian
  - Google Drive
---

# fc-workshop-analysis

Transforms raw workshop output into structured, traceable requirements that drive solution design. All requirements must be traceable to their source. This is the primary analysis step before any solution design begins.

## Purpose

Raw workshop output — transcripts, notes, whiteboard photos, client documents — has no structure. This skill converts that material into a Requirements Register, Open FDRs, and an Integration Map that the solution design phase can rely on.

Can run incrementally after each session (recommended) or once after all workshops are complete. Incremental runs allow FDRs to be resolved before the next session, which reduces rework.

## Inputs

- **Local:** `resources/workshops/` — transcripts (txt, docx, pdf), session notes, whiteboard photos, client system docs (ERP/PMS manuals, DB schemas, API docs, AS-IS diagrams). Additional sources (Google Drive, Confluence) as configured in `agent-params.md`.
- **Attachments:** files shared directly in this conversation — same document types.
- **Scope cross-reference:** `resources/commercial/` or files attached to this conversation — for scope validation.
- **Workshop Guide** (Confluence) — cross-check planned topics vs. what was actually covered.

If local folders are not directly accessible, ask the consultant to attach the relevant files before proceeding. Use all available materials. Do not skip client system documentation — it frequently contains business rules and data structures that were not verbalized in workshops.

## Execution Steps

### Step 0 — Read project configuration

**Where to find `agent-params.md`:** Read it from the **root of the user's workspace folder** — the top-level directory the consultant has selected in Claude Desktop. There is exactly one `agent-params.md` per project; it always lives at the root. Do not search subdirectories. If it is not found at the root, stop and ask the consultant to confirm its location.

Extract:
- **Output language** — all output documents must be in this language; all headings and labels must also use this language
- **Project name** — used in document headers and page titles
- **Workshop materials sources** — list of configured sources (local folders, Google Drive, Confluence)
- **Space key** and **Project root page ID** — Confluence publish targets
- **Has integrations** — if `no`, skip Step 5 (Integration Map) entirely and add note: "Integration Map not applicable — no system integrations in scope."

**Skeleton pages already exist:** fc-assistant created `Requirements Register`, `FDRs`, and `Integration Map` pages under the `Discovery` section of the project root in Confluence during Phase 0. Do not create new pages in Step 6 — update those existing skeletons instead.

### Step 1 — Inventory materials

Before analysis, list all available materials across all configured sources. Organize by **type of material**, not by session — workshop schedules change organically and session-based organization creates gaps when the agenda differs from what was planned.

| File / Document | Type | Source | Date | Functional Areas Covered | Coverage Quality |
|---|---|---|---|---|---|
| [filename or title] | Transcript / Notes / Process Diagram / Client System Doc / Commercial Doc / Other | Local / Google Drive / Confluence | [date or unknown] | [e.g. Lead Management, Integrations] | Full / Partial / Unavailable |

**Coverage Quality values:**
- `Full` — complete material for the areas it covers (transcript + notes, or detailed document)
- `Partial` — material exists but is incomplete (notes only, partial transcript, informal diagram)
- `Unavailable` — area was covered in a session but no materials are available

**Rules:**
- If a functional area identified in the Workshop Guide has no material of any type: mark it as `Unavailable` and flag before proceeding. Do not silently skip it.
- If coverage is `Partial`, note specifically what appears to be missing (e.g., "transcript missing — notes only for the first 30 minutes").
- Client system documentation (ERP manuals, API docs, DB schemas, AS-IS diagrams) must be listed — it frequently contains business rules not verbalized in workshops.
- Do not skip any file found in the configured sources, regardless of format.

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

### Step 4 — Resolve Ambiguities and Create FDRs

**Ambiguous requirements** and **FDRs are different things.** Handle them separately.

#### Ambiguous requirements

A requirement marked `Ambiguous` (stated only once, unclearly, or by a single stakeholder) does not need an FDR. Resolve it directly:

1. Present the requirement and the two or three possible interpretations to the consultant.
2. Ask which interpretation is correct — or propose the most reasonable one and ask for confirmation.
3. Once confirmed, update the requirement status to `Clear` and record the agreed interpretation in the Notes field.

Do not accumulate a backlog of unresolved `Ambiguous` requirements. Resolve them before moving to Step 5.

#### Open FDRs

Create an FDR only when the situation involves a real decision that is complex, counter-intuitive, or contested — not simply missing clarity.

**Create an FDR when:**

| Trigger | Description |
|---|---|
| Conflicting stakeholder positions | Two stakeholders described the same process in contradictory ways, and both positions have legitimate business backing — a deliberate choice must be made |
| Counter-intuitive interpretation | The most reasonable interpretation of the materials leads to a design choice that is non-obvious or could surprise the client at sign-off |
| Genuine design blocker | A critical detail is missing and no reasonable default exists — design cannot proceed without an explicit answer |
| Disputed scope boundary | There is active disagreement across materials about whether something is in or out of scope |

**Do not create an FDR when:**

- A requirement is ambiguous but interpretable → resolve it directly with the consultant (see above)
- A clear, sensible default exists → apply it; no FDR needed
- Terminology is inconsistent → normalize in the Key Data Entities table; no FDR needed
- An integration detail is unknown but a standard pattern applies → note it as an assumption in the Solution Overview

### Step 5 — Build the Integration Map

For each third-party system mentioned or confirmed across all materials:

| System | Direction | System of Record | Timing | Frequency | Data Objects | Volume (approx.) | System Owner | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|

**Column definitions:**
- **Direction:** `SF→External` | `External→SF` | `Bidirectional`
- **System of Record:** Which system is the source of truth for each data object? (e.g., "ERP is SoR for invoices; Salesforce is SoR for opportunities")
- **Timing:** `Real-time` | `Near real-time` | `Asynchronous / batch`
- **Frequency:** How often data flows (e.g., "on record creation", "hourly", "nightly")
- **Status:** `Confirmed` | `Needs Clarification` | `Out of Scope`

If any field is unknown, mark as `TBC` and create an Open FDR for that system.

**Integration Map rules:**
- This is a **functional** map — it describes business behavior, not technical implementation. Do not discuss whether the integration will use a REST API, event bus, middleware, or any other technical mechanism. Those decisions belong to the architect.
- Exception: if the client has explicitly stated a technical requirement (e.g., "our ERP only supports SFTP file transfer") and this is documented in commercial materials or workshop notes, record it in the Notes column as a **constraint**, not a design recommendation.
- The System of Record field is mandatory when Direction is Bidirectional. Without it, data ownership conflicts are guaranteed during implementation.
- If `Has integrations: no` in `agent-params.md`, skip this step entirely and note: "Integration Map not applicable — no system integrations in scope."

### Step 6 — Publish to Confluence

Update the three skeleton pages that fc-assistant created under the `Discovery` section of the project root. **Do not create new pages.** Use CQL to locate each page: `title = "[Page title]" AND ancestor = "[Project root page ID]" AND space = "[Space key]"`.

1. **Requirements Register** — update the existing `Requirements Register` skeleton with the full register per the format below
2. **FDRs** — update the existing `FDRs` skeleton with all FDRs created during this analysis
3. **Integration Map** — update the existing `Integration Map` skeleton with the integration table and per-system detail (or mark it N/A if `Has integrations: no`)

On incremental runs, update these same pages — do not create duplicates. Log the sessions analyzed in the page header.

After publishing all three pages, verify each one:
- Fetch the page content via the Confluence API and confirm no `[...]` placeholders remain
- Confirm language of headings matches `Output language` from `agent-params.md`
- Confirm tables rendered (Requirements Register, Integration Map)
- Report: "Published and verified: [Requirements Register URL] | [FDRs URL] | [Integration Map URL]"

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
- When client documents contradict workshop statements and both positions have legitimate business backing → Open FDR. For simpler contradictions, ask the consultant directly.
- Never invent requirements. If something seems obvious but was not stated → ask the consultant; do not invent an FDR for it.
- Source traceability is mandatory for every requirement. `Workshop 2, ~40min mark` is a valid source. A requirement with no source is not a valid requirement.
- When the client uses inconsistent terminology, log both terms in the Key Data Entities & Terminology table. Never silently normalize terminology — the client's language matters for adoption and training.
- Out-of-scope items must be logged, not discarded. They inform future phases and protect against scope creep disputes.
- Do not group unrelated requirements into a single entry to save space. One requirement = one row.
- **Language:** Generate all outputs — Requirements Register, FDR entries, Integration Map, and all headings — in the language specified by `Output language` in `agent-params.md`.
