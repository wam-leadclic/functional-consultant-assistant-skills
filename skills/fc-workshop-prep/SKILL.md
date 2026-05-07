---
name: fc-workshop-prep
description: Generates a structured workshop guide from commercial materials (proposals, SOW, scope documents). Organizes discovery questions by Salesforce functional area, identifies hypotheses to validate, and surfaces integration touchpoints to investigate.
argument-hint: Point to resources/commercial/ directory with pre-sales materials, or provide a Confluence space key with pre-sales documentation.
tools:
  - Atlassian
  - Google Drive
---

# fc-workshop-prep

Prepares a structured workshop guide before the first client engagement. Reads commercial materials and produces a ready-to-use guide that maximizes session value and ensures the consultant arrives fully prepared.

## Purpose

Before the first workshop, the consultant must arrive fully prepared. This skill reads commercial materials and produces a workshop guide that maximizes the value of each session by organizing discovery questions by Salesforce functional area.

Discovery is expensive time. Walking into a session without a structured plan wastes the client's time and reduces credibility. This skill ensures every session has a clear purpose, targeted questions, and defined information to extract.

## Inputs

Sources are defined in `agent-params.md` under `## Commercial materials sources`. Read all configured sources:

- **Local:** `resources/commercial/` — proposals, SOW, RFPs, email summaries, audit documents, previous Salesforce assessments
- **Google Drive:** folder configured in agent-params.md (if present) — same document types
- **Confluence:** page configured in agent-params.md (if present) — pre-sales documentation

If multiple sources are configured, read all of them. Resolve contradictions between sources by flagging them as Open FDRs, not by silently choosing one.

Additionally, look for **client system documentation** in all configured sources (ERP manuals, API documentation, data dictionaries, process diagrams). This type of material is often placed alongside commercial materials. If found, process it in Step 1 alongside the commercial documents — it provides business rules and data structures that directly improve discovery question quality.

## Execution Steps

### Step 0 — Read project configuration

Read `agent-params.md` from the project root. Extract:
- **Output language** — all generated documents must be written in this language
- **Commercial materials sources** — the list of sources to read (local paths, Google Drive folder IDs, Confluence page IDs)
- **Has integrations** — determines whether the Integration Deep-Dive section is included in the Workshop Guide

If `agent-params.md` is missing or any required field is a placeholder: stop and report.

### Step 1 — Analyze commercial materials

Read all available materials. Extract and document:

- **Project objectives** — what the client wants to achieve, in business terms
- **Agreed scope** — what was promised; this feeds the Scope Register
- **Salesforce products/clouds identified** — note if license confirmation is pending
- **Third-party systems mentioned** — ERP, PMS, billing, HR, any named system
- **Client profile** — industry, company size, existing systems, current Salesforce usage
- **Pain points** — problems surfaced during pre-sales
- **Named stakeholders** — names, titles, and their likely workshop roles
- **Timeline and constraints** — go-live date, known dependencies, budget flags

If any of the above cannot be found in the materials, explicitly mark it as unknown. Do not infer or fabricate.

### Step 2 — Map to Salesforce functional areas

Identify which Salesforce functional areas are in scope. For each relevant area, document:

- What is already known from commercial materials
- What hypotheses exist (assumptions that need validation)
- What is missing (critical information not yet available)

**Standard functional areas to evaluate — include only those relevant to this project:**

| Area | Include if... |
|---|---|
| Lead Management & Acquisition | Sales teams, marketing handoff, inbound channels mentioned |
| Opportunity Management & Sales Process | B2B sales cycle, pipeline, forecasting, deal approvals |
| Account & Contact Management | Multi-entity relationships, hierarchy, territory management |
| Quote-to-Cash / CPQ | Product catalog, pricing, quoting, contract management |
| Customer Service & Case Management | Support team, SLAs, ticketing, escalations mentioned |
| Field Service Lightning | On-site technicians, work orders, scheduling mentioned |
| Marketing & Campaign Management | Pardot/Marketing Cloud, campaigns, lead nurturing |
| Experience Cloud / Portals | Partner portal, customer self-service, community |
| Analytics, Reports & Dashboards | Reporting needs, KPIs, leadership dashboards |
| Data Migration | Historic data, previous CRM, data cleansing mentioned |
| System Integrations | Any named third-party system with data exchange |
| Security & Access Model | Multi-business unit, data visibility constraints, compliance |
| Org Configuration & Platform Administration | Multi-org, sandbox strategy, dev process |

### Step 3 — Generate the Workshop Guide

Produce the following document. Replace all bracketed placeholders with actual content.

---

```
# Workshop Guide — [Project Name]
Generated: [date] | Source: [materials analyzed]
Prepared by: WAM Global Functional Consultant

---

## Project Context

**Client:** [Client name, industry, size]
**Objectives:** [Business objectives from commercial materials — bullet list]
**Agreed Scope:** [What is in scope per SOW/proposal — bullet list]
**Known Systems:** [Third-party systems and current Salesforce usage]
**Key Stakeholders:** [Name, role, expected session involvement]
**Timeline:** [Go-live date and key milestones if known]

---

## Recommended Session Plan

| Session | Focus Areas | Estimated Duration | Recommended Participants |
|---|---|---|---|
| 1 | [Area 1, Area 2] | [e.g. 2h] | [Roles] |
| 2 | [Area 3, Area 4] | [e.g. 2h] | [Roles] |
| N | Cross-Cutting + Open Items | 1.5h | All stakeholders |

> Note: Max 2–3 functional areas per session. Allow 30 min buffer for open discussion.
> Do not schedule more than one integration-heavy area per session.

---

## Session [N] — [Focus Area(s)]

### What we know
[From commercial materials — sets context and shared understanding for the session]

### Discovery Questions
[Numbered list. Plain business language. No Salesforce jargon.]

1. Walk us through how [process] works today, from start to finish.
2. Who is involved at each step, and where do handoffs happen?
3. What are the most common points where this process breaks down?
4. How do you currently track [key entity]? Where does that data live?
5. What does success look like for this process 12 months after go-live?

### Hypotheses to Validate

| # | Hypothesis | Source | Expected Answer |
|---|---|---|---|
| H1 | [Assumption from commercial materials] | [Doc or conversation] | [What we expect to hear] |

### Documents to Request

- [Specific document or data the client should bring to this session]
- [Process diagram, org chart, pricing sheet, SLA policy, etc.]

---

## Cross-Cutting Topics

Cover in the final session or a dedicated session.

- Data volumes — how many records per key entity (Accounts, Contacts, Cases, etc.)
- Reporting and analytics needs — who needs what, at what frequency
- User adoption concerns and change management approach
- Training expectations and timeline
- Go-live approach — big bang vs. phased rollout
- Naming conventions the client uses for key entities (what do they call a "lead"? an "account"?)
- Language and localization requirements
- Accessibility or compliance requirements

---

## Integration Deep-Dive

For each third-party system identified in commercial materials:

| System | Known Purpose | Key Questions | Documents to Request |
|---|---|---|---|
| [System name] | [What it does for the client] | [What we need to understand] | [API docs, data dictionary, etc.] |

---

## Open Items Before First Workshop

Items that must be resolved or requested before workshops begin:

- [ ] [Item — e.g. Confirm Salesforce licenses purchased]
- [ ] [Item — e.g. Identify system owner for [System]]
- [ ] [Item — e.g. Clarify if [feature] is in scope per SOW section X]
```

---

## Rules

- All client-facing questions must be in plain business language — zero Salesforce jargon. "Would you use a Flow?" is wrong. "How does this approval currently work?" is right.
- Never presuppose a technical solution in discovery questions. Discovery is about understanding the business, not validating a technical approach.
- Flag scope ambiguities from commercial materials immediately as Open FDRs — do not wait for workshops to surface them.
- If a Salesforce product is mentioned in commercial materials but the license is unclear, flag it before the first workshop. Do not assume it is available.
- Session plan must be realistic — do not compress too many areas into one session. Two focused areas with depth beats five areas skimmed.
- If commercial materials are thin, note explicitly what is unknown and prioritize those areas as open discovery in the first session.
- If a named stakeholder's role is ambiguous, flag it. The wrong person in a workshop session is costly.
- **Language:** Generate all output documents (Workshop Guide, session plans, discovery questions) in the language specified in `agent-params.md`. Use the same language the client uses — if the client's materials are in Spanish, write in Spanish.
- **Integration section:** Include the Integration Deep-Dive section only if `Has integrations: yes` in `agent-params.md`. If integrations are not in scope, omit the section and add a note: "No system integrations are in scope for this project."
