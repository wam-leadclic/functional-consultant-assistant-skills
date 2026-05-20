---
name: fc-workshop-prep
description: Generates a structured workshop guide from commercial materials (proposals, SOW, scope documents). Organizes discovery questions by Salesforce functional area, identifies hypotheses to validate, and surfaces integration touchpoints to investigate.
tools:
  - Atlassian
  - Google Drive
---

# fc-workshop-prep

Prepares a structured workshop guide before the first client engagement. Reads commercial materials and produces a ready-to-use guide that maximizes session value and ensures the consultant arrives fully prepared.

**Output target: Confluence only.** The Workshop Guide is always published to Confluence — never as a Word document, local Markdown file, or any other format. Do not invoke `docx` or any file-export skill for this deliverable.

## Purpose

Before the first workshop, the consultant must arrive fully prepared. This skill reads commercial materials and produces a workshop guide that maximizes the value of each session by organizing discovery questions by Salesforce functional area.

Discovery is expensive time. Walking into a session without a structured plan wastes the client's time and reduces credibility. This skill ensures every session has a clear purpose, targeted questions, and defined information to extract.

## Inputs

Sources are defined in `agent-params.md`. Read all configured sources:

- **Local:** `resources/commercial/` — proposals, SOW, RFPs, email summaries, audit documents, previous Salesforce assessments. If the folder is not directly accessible, ask the consultant to attach the files to this conversation.
- **Attachments:** documents shared directly in this conversation — same document types.
- **Google Drive:** folder configured in `agent-params.md` (if present) — same document types.
- **Confluence:** page configured in `agent-params.md` (if present) — pre-sales documentation.

If multiple sources are configured, read all of them. Resolve contradictions between sources by flagging them as Open FDRs, not by silently choosing one.

Additionally, look for **client system documentation** in all configured sources (ERP manuals, API documentation, data dictionaries, process diagrams). This type of material is often placed alongside commercial materials. If found, process it in Step 1 alongside the commercial documents — it provides business rules and data structures that directly improve discovery question quality.

## Execution Steps

### Step 0 — Read project configuration

**Where to find `agent-params.md`:** Read it from the **root of the user's workspace folder** — the top-level directory the consultant has selected in Claude Desktop. There is exactly one `agent-params.md` per project; it always lives at the root. Do not search subdirectories. If it is not found at the root, stop and ask the consultant to confirm its location.

Extract the following values — these will be substituted directly into the template in Step 3:

| Field in agent-params.md | Template placeholder | Required for |
|---|---|---|
| `Project name` | `[Project Name]` | Document header, page title |
| `Client` | `[Client Name]` | Project Context section |
| `Output language` | — | All generated text (including headers) |
| `Has integrations` | — | Integration Deep-Dive section gate |
| `Space key` | — | Confluence publish target |
| `Project root page ID` | — | Confluence publish target |

If `agent-params.md` is incomplete or any required field still contains a placeholder value (`[...]`): stop and report exactly which fields are missing.

**Skeleton page already exists:** When fc-assistant ran Phase 0, it created a `Workshop Guide` page under the `Discovery` section of the project root page in Confluence. Do not create a new page. In Step 4, you will **update** that existing skeleton page with the generated content.

### Step 1 — Analyze commercial materials

Read all available materials. Extract and document each item, noting which section of the Workshop Guide template it feeds:

| Extracted item | Template destination |
|---|---|
| **Project objectives** — what the client wants to achieve, in business terms | Project Context → Objectives |
| **Agreed scope** — what was promised; this feeds the Scope Register | Project Context → Agreed Scope |
| **Salesforce products/clouds identified** — note if license confirmation pending | Open Items; also informs session areas |
| **Third-party systems mentioned** — ERP, PMS, billing, HR, any named system | Project Context → Known Systems; Integration Deep-Dive |
| **Client profile** — industry, company size, existing systems, current SF usage | Project Context → Client |
| **Pain points** — problems surfaced during pre-sales | Per-session "What we know" sections |
| **Named stakeholders** — names, titles, likely workshop roles | Project Context → Key Stakeholders; session Participants |
| **Timeline and constraints** — go-live date, known dependencies, budget flags | Project Context → Timeline; Open Items |

If any item cannot be found in the materials, explicitly mark it as `Unknown`. Do not infer or fabricate.

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

### Step 3 — Generate the Workshop Guide content

> **⚠️ LANGUAGE — Read this before writing a single word of content.**
> ALL content — including section headings, table headers, notes, and labels — must be written in the language specified by `Output language` in `agent-params.md`. Do not default to English. If `Output language` is `es`, write everything in Spanish, including headings like "Contexto del Proyecto" instead of "Project Context". The template below uses English labels for reference only.

Replace every bracketed placeholder with its actual value. Values for `[Project Name]` and `[Client Name]` come directly from `agent-params.md` (see mapping in Step 0). Do not leave any `[...]` placeholder in the final output.

**Integration Deep-Dive section:**
- If `Has integrations: yes` → include the Integration Deep-Dive section for each named third-party system identified in Step 1.
- If `Has integrations: no` → omit the Integration Deep-Dive section entirely and add a single line after Cross-Cutting Topics: *"No system integrations are in scope for this project."*

---

```
# Workshop Guide — [Project Name from agent-params.md → Project name]
Generated: [today's date] | Source: [list of materials analyzed]
Prepared by: WAM Global Functional Consultant

---

## Project Context
<!-- Populated from: Step 1 extractions — Project objectives, Client profile, Agreed scope, Named stakeholders, Timeline -->

**Client:** [Client from agent-params.md → Client — add industry and size from materials]
**Objectives:** [Project objectives extracted in Step 1 — bullet list]
**Agreed Scope:** [Agreed scope extracted in Step 1 — bullet list]
**Known Systems:** [Third-party systems and current Salesforce usage from Step 1]
**Key Stakeholders:** [Named stakeholders from Step 1 — Name, role, expected session involvement]
**Timeline:** [Timeline and constraints from Step 1 — go-live date and key milestones if known]

---

## Recommended Session Plan
<!-- Populated from: Step 2 functional area mapping -->

| Session | Focus Areas | Estimated Duration | Recommended Participants |
|---|---|---|---|
| 1 | [Area 1, Area 2 from Step 2] | [e.g. 2h] | [Roles from Named stakeholders] |
| 2 | [Area 3, Area 4 from Step 2] | [e.g. 2h] | [Roles] |
| N | Cross-Cutting + Open Items | 1.5h | All stakeholders |

> Note: Max 2–3 functional areas per session. Allow 30 min buffer for open discussion.
> Do not schedule more than one integration-heavy area per session.

---

## Session [N] — [Focus Area(s) from Step 2]
<!-- One section per session in the Recommended Session Plan above -->
<!-- Populated from: Step 1 pain points and known facts for these areas; Step 2 hypotheses -->

### What we know
[Pain points and known facts from Step 1 relevant to this session's areas — sets context for the session]

### Discovery Questions
<!-- Plain business language. Zero Salesforce jargon. -->

1. Walk us through how [process] works today, from start to finish.
2. Who is involved at each step, and where do handoffs happen?
3. What are the most common points where this process breaks down?
4. How do you currently track [key entity]? Where does that data live?
5. What does success look like for this process 12 months after go-live?

### Hypotheses to Validate
<!-- Populated from: Step 2 hypotheses for this area -->

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

<!-- INTEGRATION DEEP-DIVE: include only if Has integrations = yes -->

## Integration Deep-Dive
<!-- Populated from: Third-party systems identified in Step 1 -->

For each third-party system identified in commercial materials:

| System | Known Purpose | Key Questions | Documents to Request |
|---|---|---|---|
| [System name from Step 1] | [What it does for the client] | [What we need to understand] | [API docs, data dictionary, etc.] |

<!-- END INTEGRATION DEEP-DIVE -->

---

## Open Items Before First Workshop
<!-- Populated from: license gaps, unknown stakeholders, scope ambiguities, timeline flags from Step 1 -->

Items that must be resolved or requested before workshops begin:

- [ ] [Item — e.g. Confirm Salesforce licenses purchased]
- [ ] [Item — e.g. Identify system owner for [System]]
- [ ] [Item — e.g. Clarify if [feature] is in scope per SOW section X]
```

---

### Step 4 — Publish to Confluence

The `Workshop Guide` page already exists as a skeleton under the `Discovery` section of the project root page (created by fc-assistant during Phase 0 setup). **Do not create a new page.**

1. Find the existing `Workshop Guide` page: search for a page titled `Workshop Guide` that is a descendant of `Project root page ID` (from `agent-params.md`) in space `Space key`.
   - Use CQL: `title = "Workshop Guide" AND ancestor = "[Project root page ID]" AND space = "[Space key]"`
2. Update that page with the content generated in Step 3.
3. Confirm to the consultant:
   > Workshop Guide updated in Confluence: [page URL]
   > Page title: Workshop Guide — [Project Name]
   > Space: [Space key] | Parent: [project root page title]

Do not create a local file. Do not write the content to `outputs/` or any other directory.

### Step 5 — Verify

After publishing, verify the page rendered correctly:

1. Fetch the published page content via the Confluence API and confirm:
   - [ ] Page title matches: `Workshop Guide — [Project Name]`
   - [ ] No `[...]` placeholder strings remain in the body
   - [ ] The language of headings and body text matches `Output language`
   - [ ] Tables are present (at least: Session Plan, Hypotheses tables)
   - [ ] Integration Deep-Dive section: present if `Has integrations: yes`; absent if `Has integrations: no`
2. Report the result:
   - If all checks pass: "Verification complete — Workshop Guide looks correct."
   - If any check fails: state exactly which check failed and fix it before closing the task.

---

## Rules

- All client-facing questions must be in plain business language — zero Salesforce jargon. "Would you use a Flow?" is wrong. "How does this approval currently work?" is right.
- Never presuppose a technical solution in discovery questions. Discovery is about understanding the business, not validating a technical approach.
- Flag scope ambiguities from commercial materials immediately as Open FDRs — do not wait for workshops to surface them.
- If a Salesforce product is mentioned in commercial materials but the license is unclear, flag it before the first workshop. Do not assume it is available.
- Session plan must be realistic — do not compress too many areas into one session. Two focused areas with depth beats five areas skimmed.
- If commercial materials are thin, note explicitly what is unknown and prioritize those areas as open discovery in the first session.
- If a named stakeholder's role is ambiguous, flag it. The wrong person in a workshop session is costly.
- Never invoke `docx` or any file-export skill for the Workshop Guide. Output target is Confluence exclusively.
