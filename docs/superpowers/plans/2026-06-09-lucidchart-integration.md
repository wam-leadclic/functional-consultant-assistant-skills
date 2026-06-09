# Lucid Chart Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add Lucid Chart diagram generation to the FC Assistant so process flows and role hierarchy diagrams can be created and embedded in Confluence during solution design and functional document phases.

**Architecture:** New utility skill `fc-lucidchart` owns all Lucid MCP interaction. Two existing skills (`fc-solution-design`, `fc-functional-document`) get a Diagrams Protocol section defining when to autonomously propose diagrams. `fc-assistant` skill registry gets one new row.

**Tech Stack:** SKILL.md files (Markdown), Lucid MCP (`mcp__claude_ai_Lucid__authenticate`), Atlassian MCP for Confluence embeds.

---

## File Map

| Action | File | Change |
|---|---|---|
| Create | `skills/fc-lucidchart/SKILL.md` | New utility skill |
| Modify | `skills/fc-solution-design/SKILL.md` | Insert Diagrams Protocol section after line 252 (after Phase F `---`, before `## Output`) |
| Modify | `skills/fc-functional-document/SKILL.md` | Insert Diagrams Protocol section after line 200 (after document structure `---`, before `## Writing Rules`) |
| Modify | `skills/fc-assistant/SKILL.md` | Add one row to Skill Registry table after line 305 (after `fc-change-log` row) |

---

## Task 1: Create `fc-lucidchart` skill

**Files:**
- Create: `skills/fc-lucidchart/SKILL.md`

- [ ] **Step 1: Verify the skills directory exists and check existing skill structure for conventions**

Run: `ls skills/`
Expected: list of skill directories including `fc-fdr-format`, `fc-scope-register`, etc.

- [ ] **Step 2: Create the skill file with the full content below**

Create `skills/fc-lucidchart/SKILL.md` with this exact content:

```markdown
---
name: fc-lucidchart
description: Utility skill for creating diagrams in Lucid Chart and embedding them in Confluence via the Lucid macro. Invoked by fc-solution-design and fc-functional-document. Never invoked directly by the user.
tools:
  - Lucid
  - Atlassian
---

# FC Lucid Chart

Utility skill for diagram creation. Creates a diagram in Lucid Chart from structured content provided by the calling skill, then embeds it in the target Confluence page using the Lucid macro. Has no engagement logic of its own.

---

## Invocation Protocol

Always invoked with the following parameters from the calling skill:

| Parameter | Description |
|---|---|
| Type | `process-flow`, `role-hierarchy`, or `other` |
| Title | Diagram title (used in Lucid and as caption in Confluence) |
| Context | Structured description of the content to diagram |
| Target Confluence page | URL or page ID |
| Target section | Section name where the embed should be inserted |

---

## Execution Steps

### Step 1 — Validate request

Check the Type parameter:
- If `process-flow`: proceed.
- If `role-hierarchy`: proceed.
- If `other`: read the Context. If it describes an architecture diagram, data model, integration topology, or any technical system diagram: **reject** the request with:
  > "Este tipo de diagrama (arquitectura / modelo de datos / topología de integración) está fuera del alcance de fc-lucidchart y no debe incluirse en la documentación funcional."

### Step 2 — Authenticate with Lucid

Use `mcp__claude_ai_Lucid__authenticate` to establish the Lucid session.

### Step 3 — Create diagram in Lucid Chart

Create a new Lucid Chart document using the Title and Context provided. Build the diagram from the structured data in Context:

- For `process-flow`: flowchart with numbered steps as nodes, decision points as diamond shapes, directional arrows between steps. Use the exact step descriptions from Context as node labels.
- For `role-hierarchy`: org chart reflecting the hierarchy levels and role names from Context. Each role on its own node, parent-child relationships as connecting lines.
- For `other`: most appropriate diagram type for the described content.

### Step 4 — Embed in Confluence

Insert the Lucid embed macro into the target section of the target Confluence page, immediately after the corresponding text content. Use the official Lucid Confluence macro. The embed must not replace the existing text.

### Step 5 — Confirm to calling skill

> "Diagrama '[Title]' creado en Lucid Chart e integrado en '[Target section]' de [page title]."

---

## Constraints

- **Never create:** architecture diagrams, data model diagrams, integration topology diagrams.
- **Always embed** via Lucid macro — never link only.
- **Always place** the embed after the corresponding text content, never replacing it.
- This skill does not decide whether a diagram is appropriate. That judgment belongs to the calling skill.
```

- [ ] **Step 3: Review the file against the spec**

Read `docs/superpowers/specs/2026-06-09-lucidchart-integration-design.md` Section 1 and verify:
- Supported types match: `process-flow`, `role-hierarchy`, `other`
- Rejection logic present for architecture/data model/integration topology
- All 5 execution steps present
- Constraints section present

- [ ] **Step 4: Commit**

```bash
git add skills/fc-lucidchart/SKILL.md
git commit -m "feat(skill): add fc-lucidchart utility skill"
```

---

## Task 2: Add Diagrams Protocol to `fc-solution-design`

**Files:**
- Modify: `skills/fc-solution-design/SKILL.md` — insert after line 252 (the `---` that ends Phase F, immediately before `## Output: Solution Overview`)

- [ ] **Step 1: Confirm insertion point**

Read `skills/fc-solution-design/SKILL.md` lines 248–256.
Expected: line 252 is `---`, line 254 is `## Output: Solution Overview`.

- [ ] **Step 2: Insert the Diagrams Protocol section**

Insert the following block between the `---` at line 252 and `## Output: Solution Overview` at line 254:

```markdown

## Diagrams Protocol

Apply at two specific points during execution. In all cases, wait for consultant confirmation before invoking `fc-lucidchart`.

### Trigger 1 — Phase C, Dimension 2 (Process Design TO-BE)

After writing the numbered TO-BE steps for each functional area, evaluate:
- Does the flow have 5 or more steps? **OR**
- Does it contain at least one decision point (branching path)?

If either condition is true, propose:

> "El flujo TO-BE de [Área] tiene [N pasos / bifurcaciones]. Un diagrama de proceso facilitaría la revisión del cliente. ¿Lo creo en Lucid Chart y lo embedo en la sección?"

If the consultant confirms, invoke `fc-lucidchart` with:
```
Type: process-flow
Title: Flujo TO-BE — [Área]
Context: [the numbered steps already written, including decision points]
Target Confluence page: [Solution Overview page URL]
Target section: [Área] → Process Design (TO-BE)
```

### Trigger 2 — Phase E (Security Model, Role Hierarchy)

After designing the role hierarchy, evaluate:
- Does the hierarchy have more than 2 levels?

If true, propose:

> "La jerarquía de roles tiene [N niveles]. ¿Creo un diagrama visual en Lucid Chart para incluirlo en el Solution Overview?"

If the consultant confirms, invoke `fc-lucidchart` with:
```
Type: role-hierarchy
Title: Jerarquía de Roles — [Project Name]
Context: [the text tree already written]
Target Confluence page: [Solution Overview page URL]
Target section: Security Model → Role Hierarchy
```

### Explicit trigger

If the consultant requests a diagram at any point during execution of this skill, invoke `fc-lucidchart` with the relevant type and context. Do not apply threshold conditions.

### Placement rule

The diagram embed is always inserted immediately after the corresponding textual content in Confluence. It never replaces text.

---
```

- [ ] **Step 3: Review the modified file**

Read the section you just inserted and verify against the spec:
- Threshold for process flow is **5 steps** (not 6 — that's the FD threshold)
- Role hierarchy trigger is **more than 2 levels**
- Explicit trigger present (no threshold conditions)
- Invocation parameters match the fc-lucidchart protocol exactly

- [ ] **Step 4: Commit**

```bash
git add skills/fc-solution-design/SKILL.md
git commit -m "feat(skill): add Diagrams Protocol to fc-solution-design"
```

---

## Task 3: Add Diagrams Protocol to `fc-functional-document`

**Files:**
- Modify: `skills/fc-functional-document/SKILL.md` — insert after line 200 (the `---` that ends the Document Structure section, immediately before `## Writing Rules`)

- [ ] **Step 1: Confirm insertion point**

Read `skills/fc-functional-document/SKILL.md` lines 197–204.
Expected: line 199 is ` ``` ` (closes the document structure code block), line 200 is blank, line 201 is `---`, line 202 is blank, line 203 is `## Writing Rules`.

- [ ] **Step 2: Insert the Diagrams Protocol section**

Insert the following block between the `---` at line 201 and `## Writing Rules` at line 203:

```markdown

## Diagrams Protocol

Apply at two specific points during document generation. Thresholds are higher than in `fc-solution-design` — contractual text precision takes precedence in this document.

In all cases, wait for consultant confirmation before invoking `fc-lucidchart`.

### Trigger 1 — Section 5.X.2 (Solution per functional area)

After writing the solution description for each functional area, evaluate:
- Does the process have 6 or more steps? **OR**
- Are there branching paths relevant to the client's understanding?

If either condition is true, propose:

> "La descripción del proceso de [Área] es extensa. Un diagrama de flujo puede ayudar al cliente a validar que lo ha entendido correctamente antes de firmar. ¿Lo incluyo?"

If the consultant confirms, invoke `fc-lucidchart` with:
```
Type: process-flow
Title: Proceso — [Área]
Context: [the solution steps already written for this area]
Target Confluence page: [Functional Document page URL]
Target section: 5.[X] [Área] → 5.[X].2 Solución
```

### Trigger 2 — Section 6.3 (Role Hierarchy)

After writing the role hierarchy, evaluate:
- Does the hierarchy have more than 2 levels?

If true, propose:

> "La jerarquía de roles tiene [N niveles]. ¿Creo un diagrama visual en Lucid Chart para incluirlo en el documento?"

If the consultant confirms, invoke `fc-lucidchart` with:
```
Type: role-hierarchy
Title: Jerarquía de Roles — [Project Name]
Context: [the text tree already written in Section 6.3]
Target Confluence page: [Functional Document page URL]
Target section: 6.3 Jerarquía de Roles
```

### Explicit trigger

If the consultant requests a diagram at any point during execution of this skill, invoke `fc-lucidchart` with the relevant type and context. Do not apply threshold conditions.

### Restriction

Do not propose diagrams for architecture, data model, or integrations under any circumstance. The Functional Document contains no technical diagrams.

### Placement rule

The diagram embed is inserted immediately after the corresponding section content in Confluence. Text is never replaced.

---
```

- [ ] **Step 3: Review the modified file**

Read the section you just inserted and verify against the spec:
- Threshold for process flow is **6 steps** (not 5 — that's the solution design threshold)
- Role hierarchy trigger is **more than 2 levels** (same as solution design)
- Explicit restriction on architecture/data model/integration diagrams is present
- Invocation parameters match the fc-lucidchart protocol exactly

- [ ] **Step 4: Commit**

```bash
git add skills/fc-functional-document/SKILL.md
git commit -m "feat(skill): add Diagrams Protocol to fc-functional-document"
```

---

## Task 4: Update `fc-assistant` Skill Registry

**Files:**
- Modify: `skills/fc-assistant/SKILL.md` — add one row to the Skill Registry table after line 305 (after the `fc-change-log` row)

- [ ] **Step 1: Confirm insertion point**

Read `skills/fc-assistant/SKILL.md` lines 301–315.
Expected: the Skill Registry table is present, `fc-change-log` row is at line 305.

- [ ] **Step 2: Insert the new row**

After the `fc-change-log` row (line 305), add:

```
| fc-lucidchart | Utility | Creates diagrams in Lucid Chart and embeds them in Confluence. Invoked by fc-solution-design and fc-functional-document when a diagram adds significant value for the reader. |
```

The table should now read:
```markdown
| fc-fdr-format | Utility | Defines the FDR format. Not invoked directly — reference for all other skills. |
| fc-scope-register | Utility | Manages the Scope Register. Invoked whenever scope may be affected. |
| fc-change-log | Utility | Registers and manages changes to the Functional Document post-sign-off. |
| fc-lucidchart | Utility | Creates diagrams in Lucid Chart and embeds them in Confluence. Invoked by fc-solution-design and fc-functional-document when a diagram adds significant value for the reader. |
| fc-workshop-prep | Phase 1 | Generates workshop guide from commercial and client materials. |
```

- [ ] **Step 3: Review**

Read the Skill Registry section and confirm all utility skills are grouped before phase skills, and `fc-lucidchart` entry is accurate and complete.

- [ ] **Step 4: Commit**

```bash
git add skills/fc-assistant/SKILL.md
git commit -m "feat(skill): register fc-lucidchart in fc-assistant skill registry"
```
