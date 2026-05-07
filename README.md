# Functional Consultant Skills

A set of Claude Code skills for WAM Global's AI-powered Salesforce functional consultant. Covers the full engagement lifecycle — from pre-workshop preparation through solution design, formal documentation, UAT, and end-user training.

---

## Overview

The functional consultant is responsible for leading discovery workshops with the client, analysing requirements, designing the Salesforce solution, producing the sign-off documentation, and preparing the testing and training materials.

These skills implement that role as a structured, phase-driven process. Each skill corresponds to a phase of the engagement. The orchestrator (`fc-assistant`) manages the lifecycle, enforces quality gates, and invokes the right skill at the right time.

---

## Setup

Complete these steps once on your machine. You will not need to repeat them for future projects.

---

### Step 1 — Clone this repository

Open the terminal in VSCode (`Terminal → New Terminal`) and run:

```bash
git clone https://github.com/wamglobal/functional-consultant-assistant-agent.git
cd functional-consultant-assistant-agent
```

---

### Step 2 — Install Node.js

Download and install **Node.js LTS** from [nodejs.org](https://nodejs.org). Accept all defaults during installation.

Verify it installed correctly:

```bash
node --version
```

You should see a version number starting with `v18` or higher. If you see an error, restart VSCode and try again.

---

### Step 3 — Install Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Verify:

```bash
claude --version
```

---

### Step 4 — Connect to Atlassian (Confluence)

First, check whether the Atlassian MCP server is already configured:

```bash
claude mcp list
```

If you see `Atlassian` in the list (even with `Needs authentication`), you are done — skip to Step 5. The browser will ask you to log in the first time the assistant uses it.

If `Atlassian` is **not** in the list, run:

```bash
claude mcp add --transport http atlassian https://mcp.atlassian.com/v1/mcp
```

> If you see an error mentioning that the Rovo MCP server is not enabled, contact your Atlassian administrator.

---

### Step 5 — Connect to Google Drive *(optional — needed if project materials are stored in Drive)*

If your commercial or workshop materials are stored in Google Drive, add the Google Drive MCP:

```bash
claude mcp add --transport http google-drive https://mcp.google.com/drive
```

The browser will prompt you to authenticate with your Google account the first time it is used.

If all your project materials are stored locally in `resources/`, you can skip this step.

---

### Step 6 — Verify

```bash
claude mcp list
```

You should see `atlassian` in the list (and `google-drive` if you completed Step 5).

---

## Starting a new project engagement

Follow these steps at the beginning of each client engagement.

### 1. Fill in agent-params.md

Open `agent-params.md` in VSCode and replace every `[...]` placeholder with the actual project details:

| Field | Where to find it |
|---|---|
| Project name | Name of the engagement |
| Client | Client company name |
| Output language | ISO 639-1 code — `es` for Spanish, `en` for English, etc. Defaults to `es`. |
| Has integrations | `yes` if the project involves third-party system integrations; `no` otherwise |
| Confluence Base URL | `https://yourorg.atlassian.net/wiki` |
| Confluence Space key | Visible in the Confluence space URL |
| Project root page ID | In the Confluence page URL: `.../pages/`**123456**`/...` |

To add Google Drive or Confluence as material sources, uncomment the relevant entries under `## Commercial materials sources` or `## Workshop materials sources` and fill in the folder/page IDs.

### 2. Create the resource folders

```bash
mkdir -p resources/commercial resources/workshops
```

### 3. Add the commercial materials

Copy the pre-sales documents (proposals, SOW, RFPs) into `resources/commercial/`. Alternatively, configure a Google Drive folder in `agent-params.md`.

### 4. Start Claude Code

```bash
claude
```

Then type:

```
/fc-assistant new project
```

The assistant will read `agent-params.md`, confirm the configuration, create the Confluence page structure, and guide you from there.

---

## Engagement Lifecycle

```
Commercial materials (local, Google Drive, or Confluence)
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

Publishes to Confluence: `2. Solution Design / Scope Register`.

---

### `fc-change-log` — Change Log

**Utility. Manages all changes to the Functional Document after client sign-off.**

A signed-off Functional Document is a contractual artifact. Any modification after sign-off must be explicit, traced, and reflected across UATs and training materials. `fc-change-log` enforces this:

1. Verifies an approved SCR exists before registering a change
2. Produces a CL entry with downstream impact assessment (UATs, training materials, FDRs)
3. Applies the change to the Functional Document, incrementing its version
4. Flags affected UAT test cases as `Needs Review` and training modules as `Needs Update`

Four modes: `register-change` · `assess-impact` · `integrate-change` · `list-changes`

Publishes to Confluence: `3. Project Documentation / Change Log`.

---

### `fc-workshop-prep` — Workshop Preparation

**Phase 1. Generates the workshop guide from commercial materials.**

Reads proposals, SOWs, RFPs, and pre-sales documents from configured sources (local `resources/commercial/`, Google Drive, or Confluence). Also processes client system documentation (ERP manuals, API docs, process diagrams) if found alongside the commercial materials — this directly improves the quality of discovery questions.

Produces a structured workshop guide with:

- A recommended session plan (max 2–3 functional areas per session)
- Per-session discovery questions in plain business language (no Salesforce jargon)
- Hypotheses to validate with the client
- Documents to request before or during each session
- An integration investigation agenda (only if integrations are in scope)

All output is generated in the language configured in `agent-params.md`.

Publishes to Confluence: `1. Discovery / Workshop Guide`.

---

### `fc-workshop-analysis` — Workshop Analysis

**Phase 2. Transforms raw workshop output into structured requirements.**

Reads transcripts, notes, client-provided materials, and system documentation from all configured sources (local, Google Drive, Confluence). Organises the material inventory by document type — not by session number, since workshop agendas often evolve organically.

Extracts functional requirements, business rules, non-functional requirements, user profiles, integration requirements, and reporting needs — all with source traceability. Flags ambiguities, contradictions, and gaps as Open FDRs rather than resolving them silently.

Outputs:
- **Requirements Register** — every requirement with type, priority (MoSCoW), status, and source
- **FDRs** — Open records for every unresolved decision or ambiguity
- **Integration Map** — functional map of third-party systems: direction, system of record, timing (real-time vs. batch), frequency, and data objects. Strictly functional — no technical implementation patterns.

Publishes to Confluence: `1. Discovery / Requirements Register`, `1. Discovery / FDRs`, `1. Discovery / Integration Map`.

---

### `fc-solution-design` — Solution Design

**Phase 3. The core skill. Designs the Salesforce solution.**

Operates in six sequential phases:

| Phase | What it does |
|---|---|
| A — Pre-design audit | Reviews inputs; counts Open FDRs; flags ambiguous requirements; verifies Must-Have items are in scope |
| B — FDR resolution | Resolves blocking FDRs one at a time, one question per message |
| C — Solution design | Designs each functional area across 7 dimensions: feature mapping, TO-BE process, UX per profile, automation needs, data requirements, reporting, and integration touchpoints |
| D — Critical challenge | Runs a 6-question challenge checklist on every major decision before recording it — surfaces suboptimal designs before they reach UAT |
| E — Security model | Designs OWDs, role hierarchy, profiles vs. permission sets, sharing rules, and flags complex access patterns |
| F — Integration design | Documents functional integration requirements (direction, trigger, data objects, business criticality, error handling) without specifying technical implementation mechanisms |

Design principles are non-negotiable: standard over custom, declarative over programmatic, restrictive OWD, minimal profiles, no silent assumptions, licensing discipline, 3-year scalability.

Publishes to Confluence: `2. Solution Design / Solution Overview`.

---

### `fc-functional-document` — Functional Document

**Phase 4. Generates the formal sign-off document.**

Runs a quality gate before generating anything: Solution Overview must be Approved, zero Open FDRs, Scope Register current, no unresolved ambiguous requirements, and language configured in `agent-params.md`. The document is the contractual reference for the implementation — precise enough for the technical team, clear enough for the client to sign.

Key sections: executive summary, scope (in/out/assumptions/constraints), stakeholders and profiles, solution by functional area, security model, integrations, data migration, reporting, deliverables, explicit exclusions, and a full decisions log. Every Assumed FDR is listed in Section 3.3 for explicit client review.

After sign-off, the recommended next step is `fc-uat-generator`. `fc-architect-handoff` is available as an optional step at any time after sign-off.

Publishes to Confluence: `3. Project Documentation / Functional Document`.

---

### `fc-uat-generator` — UAT Generator

**Phase 4.5. Generates the UAT plan and traceable test cases.**

Run immediately after the Functional Document is signed off — before development begins, not after. Test cases are derived exclusively from the Functional Document and FDRs. No dependency on Jira or GitHub.

First produces a coverage gap analysis (FD sections vs. planned test cases) and waits for confirmation. Then derives edge cases from FDRs (boundary conditions, exception paths, security scenarios). Test cases cover: happy path, alternate paths, negative cases, security checks (one per profile minimum), integration scenarios (success and failure), and FDR edge cases.

Every test case traces to a requirement (REQ-NNN) or FDR plus the FD section. Written to be executed by a business user without technical guidance.

After scope changes: use `regenerate [CL-ID]` mode to update only the affected test cases.

Publishes to Confluence: `3. Project Documentation / UAT Plan`.

---

### `fc-training-materials` — Training Materials

**Phase 6. Generates profile-specific end-user training materials.**

Produces a training content plan first and waits for confirmation. Then generates, per profile:

- **User Guide** — comprehensive, task-oriented, step-by-step modules
- **Quick Reference Card** — one page maximum, key daily tasks only
- **Scenario-Based Exercises** — adapted from UAT test cases (if available) or from TO-BE process descriptions, reframed as business scenarios

If the UAT Plan is not yet available, exercises are generated from the Solution Overview process steps. After scope changes, use regeneration mode to update only affected profiles and modules.

Writing rules are strict: no Salesforce jargon, action-oriented headings, client terminology throughout, profile isolation, and short modules.

Publishes to Confluence: `3. Project Documentation / Training Materials / [Profile Name]`, plus a shared Glossary.

---

### `fc-architect-handoff` — Technical Handoff *(optional)*

**Optional. Generates an input package for the architect AI agent.**

Only invoke this skill if the `architect-assistant` AI agent will be used for the technical architecture phase. If a human architect is working directly from the Functional Document, skip this skill.

Transforms the signed-off Functional Document into a structured, machine-readable package optimised for AI agent consumption — structured tables throughout, no narrative prose, no blank cells, every row traces to a REQ-ID or FDR-ID. Pre-flight verifies that the Functional Document, Integration Map, Requirements Register, and Scope Register are all available before generating.

Does not suggest implementation mechanisms — that is the architect's domain.

Publishes to Confluence: `2. Solution Design / Technical Handoff Package`.

---

## Directory Structure

```
functional-consultant-assistant-agent/
├── .gitignore
├── README.md
├── CLAUDE.md               Claude Code instructions (not project config)
├── agent-params.md         Project configuration — fill this in for each engagement
├── skills/
│   ├── fc-assistant/               Orchestrator
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
    ├── commercial/
    └── workshops/
```

Client materials for each project go in `resources/` at the project root:

```
resources/
├── commercial/         Proposals, SOW, RFPs, pre-sales materials
└── workshops/          Transcripts, notes, client system docs, process diagrams
```

Alternatively, configure Google Drive or Confluence as material sources in `agent-params.md`.

---

## Confluence Structure

Each project uses this page hierarchy in Confluence:

```
[Project Name]
├── 1. Discovery
│   ├── Workshop Guide
│   ├── Requirements Register
│   ├── Integration Map
│   └── FDRs
├── 2. Solution Design
│   ├── Solution Overview
│   ├── Scope Register
│   └── Technical Handoff Package  ← only if fc-architect-handoff is invoked
└── 3. Project Documentation
    ├── Functional Document
    ├── Change Log
    ├── UAT Plan
    └── Training Materials
```

The `fc-assistant` creates this structure automatically when starting a new project.

---

## Key Concepts

**FDR (Functional Decision Record)** — the primary traceability mechanism. Every design decision, working assumption, or open question is captured as an FDR. No assumption is ever implicit. FDRs include a Revision History field that links post-sign-off changes to their Change Log entry. See `fc-fdr-format` for the full format specification.

**Scope Register** — the authoritative record of what is and is not included in the project. Managed by `fc-scope-register`, referenced by all other skills. Scope additions without an approved SCR are blocked.

**Change Log** — the record of all changes to the signed-off Functional Document. Managed by `fc-change-log`. Every change requires an approved SCR and produces a new FD version, with impact flags on affected UAT test cases and training materials.

**Integration Map** — a functional map of third-party system integrations produced in Phase 2. Captures direction, system of record, timing, and frequency. Strictly functional — no technical implementation patterns (API types, middleware, etc.) unless explicitly required by the client.

**Quality gates** — enforced by `fc-assistant`. Key gates:
- Functional Document requires zero Open FDRs and an Approved Solution Overview
- UAT generation requires a signed-off Functional Document (runs immediately after sign-off, before development)
- Training phases require a signed-off Functional Document (UAT Plan is optional)
- Solution Design requires a clean Requirements Register (no unresolved conflicts)
- If `Has integrations: yes` in agent-params.md, an Integration Map is required before Solution Design begins

**Architect handoff** — `fc-architect-handoff` (skill name) produces the input package consumed by the `architect-assistant` agent. It is optional — only generate it if the architect-assistant AI agent is being used. The package is optimised for machine reading, not human reading.
