# Functional Consultant Skills

A set of skills for WAM Global's AI-powered Salesforce functional consultant, running in **Claude Desktop**. Covers the full engagement lifecycle — from pre-workshop preparation through solution design, formal documentation, UAT, and end-user training.

---

## Overview

The functional consultant is responsible for leading discovery workshops with the client, analysing requirements, designing the Salesforce solution, producing the sign-off documentation, and preparing the testing and training materials.

These skills implement that role as a structured, phase-driven process. Each skill corresponds to a phase of the engagement. The orchestrator (`fc-assistant`) manages the lifecycle, enforces quality gates, and invokes the right skill at the right time.

---

## Setup

Complete these steps once. You will not need to repeat them for future projects.

---

### Step 1 — Install Claude Desktop

Download and install Claude Desktop from [claude.ai/download](https://claude.ai/download). Sign in with your Anthropic account.

---

### Step 2 — Install the FC Assistant skills

1. In Claude Desktop, open **Settings**
2. Go to **Skills** in the left panel
3. Click the **+** button next to **Personal plugins**
4. In the **Add marketplace** dialog, enter:
   ```
   wam-leadclic/functional-consultant-assistant-skills
   ```
5. Click **Sync**

This installs all 11 skills at once. You will not need to repeat this for future projects — only when there is a new version to update to.

---

### Updating the skills

To update to a newer version, go to **Settings → Skills**, find the plugin, and click **Sync** again.

---

### Step 3 — Connect to Atlassian (Confluence)

1. In Claude Desktop, click the **Settings** icon (bottom-left corner)
2. Go to **Integrations**
3. Find **Atlassian** and click **Connect**
4. A browser window will open — log in with your Atlassian account

> If Atlassian does not appear in the Integrations list, contact your Atlassian administrator to confirm that the Rovo MCP integration is enabled for your organisation.

---

### Step 4 — Connect to Google Drive *(optional)*

Only needed if commercial or workshop materials are stored in Google Drive.

1. In Claude Desktop Settings → **Integrations**
2. Find **Google Drive** and click **Connect**
3. Authenticate with your Google account when the browser opens

If all your project materials are stored locally in `resources/`, skip this step.

---

### Step 5 — Download this repository *(optional)*

Only needed if you will store project materials locally in `resources/commercial/` and `resources/workshops/`. If you will always provide materials via Google Drive, Confluence, or file attachments in the conversation, skip this step.

Download this repository as a ZIP from GitHub and unzip it on your computer, or clone it:

```bash
git clone https://github.com/wam-leadclic/functional-consultant-assistant-skills.git
```

---

## Starting a new project engagement

Follow these steps at the beginning of each client engagement.

---

### 1. Create a new Project in Claude Desktop

Each client engagement gets its own Project. This keeps all conversations for that engagement together and maintains shared context across sessions.

1. In Claude Desktop, click **+ New Project** in the sidebar
2. Name it after the client or engagement (e.g. `Acme Corp — Salesforce CRM`)

---

### 2. Start a conversation

Open a new conversation in the Project and type:

```
new project
```

Claude will read `agent-params.md` from the project folder. If it is missing or incomplete, Claude will ask you for the required details (project name, client, Confluence coordinates, language, integrations) and write the file automatically.

---

### 3. Add commercial materials

Place your pre-sales documents (proposals, SOW, RFPs, audit documents) in `resources/commercial/`. Alternatively, drag and drop files directly into the conversation or configure a Google Drive folder when Claude asks.

---

## Engagement Lifecycle

```
Commercial materials (local folder, Google Drive, or Confluence)
        │
        ▼
[fc-workshop-prep]          Phase 1 — Workshop guide from commercial + client system materials
        │
        ▼
   WORKSHOPS
        │
        ▼
[fc-workshop-analysis]      Phase 2 — Requirements Register + FDRs + Integration Map
        │
        ▼
[fc-solution-design] ◄──── iterative FDR resolution
        │
        ▼
[fc-functional-document]    Phase 4 — Formal sign-off document
        │
        ├──► [fc-uat-generator]          Phase 4.5 — UAT plan + test cases (immediately after sign-off)
        │
        ├──► [fc-training-materials]     Phase 6 — Training per user profile
        │
        └──► [fc-architect-handoff]      Optional — Input package for architect AI agent
```

`fc-scope-register`, `fc-fdr-format`, and `fc-change-log` are cross-cutting utilities used throughout the lifecycle.

**Scope changes after sign-off:** use the `scope-change` mode in `fc-assistant`. It guides you through the full chain: Scope Change Request → Change Log entry → Functional Document update → UAT regeneration → training update.

---

## Skills

### `fc-assistant` — Orchestrator

**Entry point for all engagement work.**

Detects the current project phase by querying Confluence for existing artifacts, proposes the next action, and invokes the appropriate skill. Enforces quality gates (e.g., the Functional Document cannot be generated while Open FDRs exist; UAT cannot start without a signed-off Functional Document). Carries embedded Salesforce expertise — it challenges skipped phases, flags licensing gaps, and surfaces complexity that warrants architect escalation.

If no project configuration is found in the system prompt, fc-assistant enters **Setup Mode**: asks the consultant for all required fields in a single message, then produces a ready-to-paste configuration block with instructions for adding it to the Project custom instructions.

Modes: `new project` · `resume [project name]` · `status` · `scope-change` · direct invocation (`"prepare workshops"`, `"design the solution"`, etc.)

---

### `fc-fdr-format` — Functional Decision Record Format

**Utility. Defines the FDR format used by all other skills.**

A Functional Decision Record (FDR) captures every design decision, working assumption, and unresolved question throughout the engagement. Three statuses:

- `Confirmed` — explicitly validated with the client
- `Assumed` — inferred by the consultant; must be surfaced in the Functional Document for client review
- `Open` — unresolved; blocks Functional Document sign-off

Each FDR includes a Revision History table that logs changes made after the Functional Document has been signed off, linked to the corresponding Change Log entry.

No assumption is ever buried in prose. Every assumption is an FDR.

---

### `fc-scope-register` — Scope Register

**Utility. Single source of truth for project scope.**

Tracks in-scope items, explicit exclusions, and Scope Change Requests (SCRs). Other skills invoke this when they detect something that may extend agreed scope — scope creep is never silently absorbed. Supports four modes: add to scope, exclude, scope check, and scope change request.

When an SCR is approved and the Functional Document already exists, the scope register automatically triggers `fc-change-log` to register the impact on the signed-off document.

Publishes to Confluence: `Solution Design / Scope Register`.

---

### `fc-change-log` — Change Log

**Utility. Manages all changes to the Functional Document after client sign-off.**

A signed-off Functional Document is a contractual artifact. Any modification after sign-off must be explicit, traced, and reflected across UATs and training materials. `fc-change-log` enforces this:

1. Verifies an approved SCR exists before registering a change
2. Produces a CL entry with downstream impact assessment (UATs, training materials, FDRs)
3. Applies the change to the Functional Document, incrementing its version
4. Flags affected UAT test cases as `Needs Review` and training modules as `Needs Update`

Four modes: `register-change` · `assess-impact` · `integrate-change` · `list-changes`

Publishes to Confluence: `Deliverables / Change Log`.

---

### `fc-workshop-prep` — Workshop Preparation

**Phase 1. Generates the workshop guide from commercial materials.**

Reads proposals, SOWs, RFPs, and pre-sales documents from configured sources (local `resources/commercial/`, files attached to the conversation, Google Drive, or Confluence). Also processes client system documentation (ERP manuals, API docs, process diagrams) if found alongside the commercial materials — this directly improves the quality of discovery questions.

Produces a structured workshop guide with:

- A recommended session plan (max 2–3 functional areas per session)
- Per-session discovery questions in plain business language (no Salesforce jargon)
- Hypotheses to validate with the client
- Documents to request before or during each session
- An integration investigation agenda (only if integrations are in scope)

All output is generated in the language specified in the project configuration.

Publishes to Confluence: `Discovery / Workshop Guide`.

---

### `fc-workshop-analysis` — Workshop Analysis

**Phase 2. Transforms raw workshop output into structured requirements.**

Reads transcripts, notes, client-provided materials, and system documentation from all configured sources (local `resources/workshops/`, conversation attachments, Google Drive, Confluence). Organises the material inventory by document type — not by session number, since workshop agendas often evolve organically.

Extracts functional requirements, business rules, non-functional requirements, user profiles, integration requirements, and reporting needs — all with source traceability. Applies a deliberate threshold before creating FDRs: only genuine conflicts, counter-intuitive decisions, real design blockers, and disputed scope boundaries warrant an FDR. Routine ambiguities and clear defaults are resolved directly in the Solution Overview.

Outputs:
- **Requirements Register** — every requirement with type, priority (MoSCoW), status, and source
- **FDRs** — Open records for genuine conflicts, counter-intuitive decisions, and real design blockers only
- **Integration Map** — functional map of third-party systems: direction, system of record, timing (real-time vs. batch), frequency, and data objects. Strictly functional — no technical implementation patterns.

Publishes to Confluence: `Discovery / Requirements Register`, `Discovery / FDRs`, `Discovery / Integration Map`.

---

### `fc-solution-design` — Solution Design

**Phase 3. The core skill. Designs the Salesforce solution.**

Operates in six sequential phases:

| Phase | What it does |
|---|---|
| A — Pre-design audit | Reviews inputs; counts Open FDRs; flags ambiguous requirements; verifies Must-Have items are in scope |
| B — FDR resolution | Resolves blocking FDRs one at a time, one question per message |
| C — Solution design | Designs each functional area across 7 dimensions: feature mapping, TO-BE process, UX per profile, automation needs, data requirements, reporting, and integration touchpoints |
| D — Critical challenge | Runs a 6-question challenge checklist on every major decision. Standard/obvious decisions go directly into the Solution Overview; non-obvious ones are recorded as Assumed FDRs; any concern raised stops and waits for deliberate input |
| E — Security model | Designs OWDs, role hierarchy, profiles vs. permission sets, sharing rules, and flags complex access patterns |
| F — Integration design | Documents functional integration requirements (direction, trigger, data objects, business criticality, error handling) without specifying technical implementation mechanisms |

Design principles are non-negotiable: standard over custom, declarative over programmatic, restrictive OWD, minimal profiles, no silent assumptions, licensing discipline, 3-year scalability.

Publishes to Confluence: `Solution Design / Solution Overview`.

---

### `fc-functional-document` — Functional Document

**Phase 4. Generates the formal sign-off document.**

Runs a quality gate before generating anything: Solution Overview must be Approved, zero Open FDRs, Scope Register current, no unresolved ambiguous requirements, and output language specified in the project configuration. The document is the contractual reference for the implementation — precise enough for the technical team, clear enough for the client to sign.

Key sections: executive summary, scope (in/out/assumptions/constraints), stakeholders and profiles, solution by functional area, security model, integrations, data migration, reporting, deliverables, explicit exclusions, and a full decisions log. Every Assumed FDR is listed in Section 3.3 for explicit client review.

After sign-off, the recommended next step is `fc-uat-generator`. `fc-architect-handoff` is available as an optional step at any time after sign-off.

Publishes to Confluence: `Deliverables / Functional Document`.

---

### `fc-uat-generator` — UAT Generator

**Phase 4.5. Generates the UAT plan and traceable test cases.**

Run immediately after the Functional Document is signed off — before development begins, not after. Test cases are derived exclusively from the Functional Document and FDRs.

First produces a coverage gap analysis (FD sections vs. planned test cases) and waits for confirmation. Then derives edge cases from FDRs (boundary conditions, exception paths, security scenarios). Test cases cover: happy path, alternate paths, negative cases, security checks (one per profile minimum), integration scenarios (success and failure), and FDR edge cases.

Every test case traces to a requirement (REQ-NNN) or FDR plus the FD section. Written to be executed by a business user without technical guidance.

After scope changes: use `regenerate [CL-ID]` mode to update only the affected test cases.

Publishes to Confluence: `Deliverables / UAT Plan`.

---

### `fc-training-materials` — Training Materials

**Phase 6. Generates profile-specific end-user training materials.**

Produces a training content plan first and waits for confirmation. Then generates, per profile:

- **User Guide** — comprehensive, task-oriented, step-by-step modules
- **Quick Reference Card** — one page maximum, key daily tasks only
- **Scenario-Based Exercises** — adapted from UAT test cases (if available) or from TO-BE process descriptions, reframed as business scenarios

If the UAT Plan is not yet available, exercises are generated from the Solution Overview process steps. After scope changes, use regeneration mode to update only affected profiles and modules.

Writing rules are strict: no Salesforce jargon, action-oriented headings, client terminology throughout, profile isolation, and short modules.

Publishes to Confluence: `Deliverables / Training Materials / [Profile Name]`, plus a shared Glossary.

---

### `fc-architect-handoff` — Technical Handoff *(optional)*

**Optional. Generates an input package for the architect AI agent.**

Only invoke this skill if the `architect-assistant` AI agent will be used for the technical architecture phase. If a human architect is working directly from the Functional Document, skip this skill.

Transforms the signed-off Functional Document into a structured, machine-readable package optimised for AI agent consumption — structured tables throughout, no narrative prose, no blank cells, every row traces to a REQ-ID or FDR-ID. Pre-flight verifies that the Functional Document, Integration Map, Requirements Register, and Scope Register are all available before generating.

Does not suggest implementation mechanisms — that is the architect's domain.

Publishes to Confluence: `Solution Design / Technical Handoff Package`.

---

## Directory Structure

```
functional-consultant-assistant-agent/
├── .gitignore
├── README.md
├── CLAUDE.md               Claude Desktop instructions
├── skills/
│   ├── fc-assistant/               Orchestrator — paste into Project custom instructions
│   ├── fc-fdr-format/              Utility — FDR format definition
│   ├── fc-scope-register/          Utility — Scope management
│   ├── fc-change-log/              Utility — Post-sign-off FD change management
│   ├── fc-workshop-prep/           Phase 1 — Workshop guide
│   ├── fc-workshop-analysis/       Phase 2 — Requirements analysis + Integration Map
│   ├── fc-solution-design/         Phase 3 — Solution design
│   ├── fc-functional-document/     Phase 4 — Formal document
│   ├── fc-uat-generator/           Phase 4.5 — UAT plan
│   ├── fc-training-materials/      Phase 6 — Training materials
│   └── fc-handoff-to-architect/    Optional — Architect AI agent handoff
└── resources/                      Client materials (gitignored)
    ├── commercial/                 Pre-sales materials — read by Claude, never written
    └── workshops/                  Workshop outputs — read by Claude, never written
```

Materials for each project go in `resources/` at the project root. Alternatively, share files directly in the conversation or configure Google Drive or Confluence sources when Claude asks during setup.

---

## Confluence Structure

Each project uses this page hierarchy in Confluence:

```
[Project Name]
├── Discovery
│   ├── Workshop Guide
│   ├── Requirements Register
│   ├── Integration Map
│   └── FDRs
├── Solution Design
│   ├── Solution Overview
│   ├── Scope Register
│   └── Technical Handoff Package  ← only if fc-architect-handoff is invoked
└── Deliverables
    ├── Functional Document
    ├── Change Log
    ├── UAT Plan
    └── Training Materials
```

The `fc-assistant` creates this structure automatically when starting a new project. Confluence is the single source of truth for all project deliverables — Claude never creates local Markdown files for deliverables.

---

## Key Concepts

**FDR (Functional Decision Record)** — the primary traceability mechanism. FDRs are created only for genuine decisions requiring human input: conflicting requirements, counter-intuitive design choices, real design blockers, and disputed scope boundaries. Routine decisions and sensible defaults are documented directly in the Solution Overview. FDRs include a Revision History field that links post-sign-off changes to their Change Log entry. See `fc-fdr-format` for the full format specification.

**Scope Register** — the authoritative record of what is and is not included in the project. Managed by `fc-scope-register`, referenced by all other skills. Scope additions without an approved SCR are blocked.

**Change Log** — the record of all changes to the signed-off Functional Document. Managed by `fc-change-log`. Every change requires an approved SCR and produces a new FD version, with impact flags on affected UAT test cases and training materials.

**Integration Map** — a functional map of third-party system integrations produced in Phase 2. Captures direction, system of record, timing, and frequency. Strictly functional — no technical implementation patterns (API types, middleware, etc.) unless explicitly required by the client.

**Quality gates** — enforced by `fc-assistant`. Key gates:
- Functional Document requires zero Open FDRs and an Approved Solution Overview
- UAT generation requires a signed-off Functional Document (runs immediately after sign-off, before development)
- Training phases require a signed-off Functional Document (UAT Plan is optional)
- Solution Design requires a clean Requirements Register (no unresolved conflicts)
- If integrations are in scope, an Integration Map is required before Solution Design begins

**Architect handoff** — `fc-architect-handoff` produces the input package consumed by the `architect-assistant` agent. It is optional — only generate it if the architect-assistant AI agent is being used. The package is optimised for machine reading, not human reading.
