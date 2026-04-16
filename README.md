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

### Step 4 — Connect to Atlassian (Confluence + Jira)

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

### Step 5 — Connect to GitHub *(optional — only needed for the UAT phase)*

**Step 5a — Create your GitHub token**

1. Go to [github.com/settings/personal-access-tokens/new](https://github.com/settings/personal-access-tokens/new)
2. Name it `FC Assistant`
3. Set expiration to 90 days
4. Under **Repository access**, select the client repository
5. Under **Repository permissions → Contents**, choose `Read-only`
6. Click **Generate token** and copy the value — you will only see it once

**Step 5b — Save your token**

Create a new file called `.env` in the project root. In VSCode, right-click the root folder, select **New File**, name it `.env`, and paste this inside:

```
GITHUB_TOKEN=paste_your_token_here
```

Replace `paste_your_token_here` with the token you just copied and save the file.

This file is gitignored — its contents will never be uploaded to GitHub.

**Step 5c — Register the token with Claude Code**

```bash
source .env && claude mcp add-json github "{\"type\":\"http\",\"url\":\"https://api.githubcopilot.com/mcp\",\"headers\":{\"Authorization\":\"Bearer $GITHUB_TOKEN\"}}"
```

---

### Step 6 — Verify

```bash
claude mcp list
```

You should see `atlassian` in the list (and `github` if you completed Step 5).

---

## Starting a new project engagement

Follow these steps at the beginning of each client engagement.

### 1. Fill in CLAUDE.md

Open `CLAUDE.md` in VSCode and replace every `[...]` placeholder with the actual project details:

| Field | Where to find it |
|---|---|
| Project name | Name of the engagement |
| Client | Client company name |
| Confluence Base URL | `https://yourorg.atlassian.net/wiki` |
| Confluence Space key | Visible in the Confluence space URL |
| Project root page ID | In the Confluence page URL: `.../pages/`**123456**`/...` |
| Jira Project key | Prefix of all Jira tickets (e.g. `PROJ` in `PROJ-123`) |
| GitHub Repository | Full URL — only if using the UAT phase |

### 2. Create the resource folders

```bash
mkdir -p resources/commercial resources/workshops
```

### 3. Add the commercial materials

Copy the pre-sales documents (proposals, SOW, RFPs) into `resources/commercial/`.

### 4. Start Claude Code

```bash
claude
```

Then type:

```
/fc-assistant new project
```

The assistant will read `CLAUDE.md`, confirm the configuration, create the Confluence page structure, and guide you from there.

---

## Engagement Lifecycle

```
Commercial materials
        │
        ▼
[fc-workshop-prep]          Phase 1 — Workshop guide from pre-sales materials
        │
        ▼
   WORKSHOPS
        │
        ▼
[fc-workshop-analysis]      Phase 2 — Requirements Register + Open FDRs
        │
        ▼
[fc-solution-design] ◄──── iterative FDR resolution
        │
        ▼
[fc-functional-document]    Phase 4 — Formal sign-off document
        │
        ├──► [fc-handoff-to-architect]   Phase 5 — Input for architect agent
        │
        ├──► [fc-uat-generator]          Phase 6 — UAT plan + test cases
        │
        └──► [fc-training-materials]     Phase 7 — Training per user profile
```

`fc-scope-register` and `fc-fdr-format` are cross-cutting utilities used throughout the lifecycle.

---

## Skills

### `fc-assistant` — Orchestrator

**Entry point for all engagement work.**

Detects the current project phase by querying Confluence for existing artifacts, proposes the next action, and invokes the appropriate skill. Enforces quality gates (e.g., the Functional Document cannot be generated while Open FDRs exist; UAT cannot start without a signed-off Functional Document). Carries embedded Salesforce expertise — it challenges skipped phases, flags licensing gaps, and surfaces complexity that warrants architect escalation.

Modes: `new project` · `resume [project name]` · `status` · direct invocation (`"prepare workshops"`, `"design the solution"`, etc.)

---

### `fc-fdr-format` — Functional Decision Record Format

**Utility. Defines the FDR format used by all other skills.**

A Functional Decision Record (FDR) captures every design decision, working assumption, and unresolved question throughout the engagement. Three statuses:

- `Confirmed` — explicitly validated with the client
- `Assumed` — inferred by the consultant; must be surfaced in the Functional Document for client review
- `Open` — unresolved; blocks Functional Document sign-off

No assumption is ever buried in prose. Every assumption is an FDR.

---

### `fc-scope-register` — Scope Register

**Utility. Single source of truth for project scope.**

Tracks in-scope items, explicit exclusions, and Scope Change Requests (SCRs). Other skills invoke this when they detect something that may extend agreed scope — scope creep is never silently absorbed. Supports four modes: add to scope, exclude, scope check, and scope change request.

Publishes to Confluence: `2. Solution Design / Scope Register`.

---

### `fc-workshop-prep` — Workshop Preparation

**Phase 1. Generates the workshop guide from commercial materials.**

Reads proposals, SOWs, RFPs, and pre-sales documents from `resources/commercial/`. Identifies which Salesforce functional areas are in scope, extracts known facts and hypotheses, and produces a structured workshop guide with:

- A recommended session plan (max 2–3 functional areas per session)
- Per-session discovery questions in plain business language (no Salesforce jargon)
- Hypotheses to validate with the client
- Documents to request before or during each session
- An integration investigation agenda for third-party systems

Publishes to Confluence: `1. Discovery / Workshop Guide`.

---

### `fc-workshop-analysis` — Workshop Analysis

**Phase 2. Transforms raw workshop output into structured requirements.**

Reads transcripts, session notes, and client-provided materials from `resources/workshops/`. Extracts functional requirements, business rules, non-functional requirements, user profiles, integration requirements, and reporting needs — all with source traceability. Flags ambiguities, contradictions, and gaps as Open FDRs rather than resolving them silently.

Outputs:
- **Requirements Register** — every requirement with type, priority (MoSCoW), status, and source
- **FDRs** — Open records for every unresolved decision or ambiguity
- **Integration Map** — third-party systems with data direction, frequency, and volume

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
| F — Integration design | Documents functional integration requirements (direction, trigger, data, error handling, volume) without specifying the technical mechanism |

Design principles are non-negotiable: standard over custom, declarative over programmatic, restrictive OWD, minimal profiles, no silent assumptions, licensing discipline, 3-year scalability.

Publishes to Confluence: `2. Solution Design / Solution Overview`.

---

### `fc-functional-document` — Functional Document

**Phase 4. Generates the formal sign-off document.**

Runs a four-condition quality gate before generating anything: Solution Overview must be Approved, zero Open FDRs, Scope Register current, no unresolved ambiguous requirements. The document is the contractual reference for the implementation — precise enough for the technical team, clear enough for the client to sign.

Key sections: executive summary, scope (in/out/assumptions/constraints), stakeholders and profiles, solution by functional area, security model, integrations, data migration, reporting, deliverables, explicit exclusions, and a full decisions log. Every Assumed FDR is listed in Section 3.3 for explicit client review.

Publishes to Confluence: `3. Project Documentation / Functional Document`.

---

### `fc-handoff-to-architect` — Technical Handoff

**Phase 5. Bridges functional consulting and technical architecture.**

Transforms the signed-off Functional Document into a self-contained input package for the `architect-assistant` agent. The architect reads this and nothing else — no workshop transcripts, no commercial materials. Contains: business entities, business rules, user profiles and access requirements, automation needs (functional level only), integration requirements, security recommendations, and flagged technical risks.

Does not suggest implementation mechanisms — that is the architect's domain.

Publishes to Confluence: `2. Solution Design / Technical Handoff Package`.

---

### `fc-uat-generator` — UAT Generator

**Phase 6. Generates the UAT plan and traceable test cases.**

Requires: Functional Document (Confluence), Jira board, and optionally the GitHub repository. First produces a coverage gap analysis (Functional Document requirements vs. Jira completed items) and waits for confirmation before generating test cases. Reviews GitHub customizations for edge cases implied by the implementation.

Test cases cover: happy path, alternate paths, negative cases, security checks (one per profile minimum), integration scenarios (success and failure), and edge cases from code review. Every test case traces to a requirement (REQ-NNN) or FDR, and is written to be executed by a business user without technical guidance.

Publishes to Confluence: `3. Project Documentation / UAT Plan`.

---

### `fc-training-materials` — Training Materials

**Phase 7. Generates profile-specific end-user training materials.**

Produces a training content plan first and waits for confirmation. Then generates, per profile:

- **User Guide** — comprehensive, task-oriented, step-by-step modules
- **Quick Reference Card** — one page maximum, key daily tasks only
- **Scenario-Based Exercises** — adapted from UAT test cases, reframed as business scenarios

Writing rules are strict: no Salesforce jargon, action-oriented headings, client terminology throughout, profile isolation (a sales rep's guide never describes what the manager sees), and short modules (one page maximum per task).

Publishes to Confluence: `3. Project Documentation / Training Materials / [Profile Name]`, plus a shared Glossary.

---

## Directory Structure

```
functional-consultant-assistant-agent/
├── .gitignore
├── README.md
├── skills/
│   ├── fc-assistant/           Orchestrator
│   ├── fc-fdr-format/          Utility — FDR format definition
│   ├── fc-scope-register/      Utility — Scope management
│   ├── fc-workshop-prep/       Phase 1 — Workshop guide
│   ├── fc-workshop-analysis/   Phase 2 — Requirements analysis
│   ├── fc-solution-design/     Phase 3 — Solution design
│   ├── fc-functional-document/ Phase 4 — Formal document
│   ├── fc-handoff-to-architect/Phase 5 — Architect handoff
│   ├── fc-uat-generator/       Phase 6 — UAT plan
│   └── fc-training-materials/  Phase 7 — Training materials
└── resources/                  Client materials (gitignored)
    ├── commercial/
    └── workshops/
```

Client materials for each project go in `resources/` at the project root:

```
resources/
├── commercial/         Proposals, SOW, RFPs, pre-sales materials
└── workshops/
    ├── session-01/     Transcripts, notes, client system docs, whiteboard photos
    └── session-02/
```

---

## Confluence Structure

Each project uses this page hierarchy in Confluence:

```
[Project Name]
├── 1. Discovery
│   ├── Workshop Guide
│   ├── Requirements Register
│   └── FDRs
├── 2. Solution Design
│   ├── Solution Overview
│   ├── Scope Register
│   └── Technical Handoff Package
└── 3. Project Documentation
    ├── Functional Document
    ├── UAT Plan
    └── Training Materials
```

The `fc-assistant` creates this structure automatically when starting a new project.

---

## Key Concepts

**FDR (Functional Decision Record)** — the primary traceability mechanism. Every design decision, working assumption, or open question is captured as an FDR. No assumption is ever implicit. See `fc-fdr-format` for the full format specification.

**Scope Register** — the authoritative record of what is and is not included in the project. Managed by `fc-scope-register`, referenced by all other skills. Scope additions without an approved SCR are blocked.

**Quality gates** — enforced by `fc-assistant`. Key gates:
- Functional Document requires zero Open FDRs and an Approved Solution Overview
- UAT and Training phases require a signed-off Functional Document
- Solution Design requires a clean Requirements Register (no unresolved conflicts)

**Architect handoff** — `fc-handoff-to-architect` produces the input package consumed by the `architect-assistant` agent (in `architect-assistant-skills/`). The two agents form a pipeline: functional consultant → architect → development.
