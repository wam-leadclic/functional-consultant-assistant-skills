# Lucid Chart Integration — Design Spec

Date: 2026-06-09 | Status: Approved

---

## Overview

Add Lucid Chart diagram generation to the FC Assistant via the official Lucid MCP. Diagrams are created when:
1. The user explicitly requests one at any point during an engagement.
2. The agent autonomously determines a diagram would significantly aid the reader's comprehension — limited to `fc-solution-design` and `fc-functional-document`.

All diagrams are embedded in Confluence via the official Lucid macro (not linked). Diagrams complement text — they never replace it.

---

## Approach

Hybrid (Option C): a new utility skill `fc-lucidchart` concentrates the Lucid MCP interaction protocol (the *how*). The two calling skills define their own rules for when to propose diagrams (the *when*).

---

## Components

### 1. New skill: `fc-lucidchart`

**File:** `skills/fc-lucidchart/SKILL.md`

**Role:** Utility skill. Handles authentication, diagram creation via Lucid MCP, and embedding the result into a Confluence page. No engagement logic. Always invoked by another skill, never directly by the user.

**Supported diagram types:**
- `process-flow` — TO-BE process steps (primary use case)
- `role-hierarchy` — Salesforce role hierarchy for security model
- `other` — any diagram the calling skill explicitly justifies

**Invocation protocol (inputs the skill receives):**
```
Type: [process-flow | role-hierarchy | other]
Title: [diagram title]
Context: [structured description of the content to diagram]
Target Confluence page: [URL or page ID]
Target section: [section name where the embed should be inserted]
```

**Execution steps:**
1. Authenticate with Lucid MCP (`mcp__claude_ai_Lucid__authenticate`)
2. Create the diagram document in Lucid Chart using the provided context
3. Generate the Lucid embed macro for Confluence
4. Insert the macro into the target section of the target Confluence page, immediately after the corresponding text content
5. Confirm to the calling skill: diagram title, Lucid document URL, Confluence section updated

**Explicit restriction:** `fc-lucidchart` never creates architecture diagrams, data model diagrams, or integration topology diagrams. If the calling skill passes `other` with a context that describes any of these, the skill must reject the request and explain why.

---

### 2. Changes to `fc-solution-design`

**File:** `skills/fc-solution-design/SKILL.md`

Add a **Diagrams Protocol** block. Applied at two specific points:

**Autonomous trigger — Phase C, Dimension 2 (Process Design TO-BE):**
After writing the numbered TO-BE steps for each functional area, evaluate:
- Does the flow have 5 or more steps? OR
- Does it contain at least one decision point (branching path)?

If either condition is true, propose to the consultant:
> "El flujo TO-BE de [Área] tiene [N pasos / bifurcaciones]. Un diagrama de proceso facilitaría la revisión del cliente. ¿Lo creo en Lucid Chart y lo embedo en la sección?"

Wait for confirmation before invoking `fc-lucidchart`.

**Autonomous trigger — Phase E (Security Model, Role Hierarchy):**
After designing the role hierarchy, if it has more than 2 levels, propose:
> "La jerarquía de roles tiene [N niveles]. ¿Creo un diagrama visual en Lucid Chart para incluirlo en el Solution Overview?"

Wait for confirmation before invoking `fc-lucidchart`.

**Explicit trigger:**
If the consultant requests a diagram at any point during skill execution, invoke `fc-lucidchart` without applying threshold conditions.

**Placement:** The diagram embed is inserted immediately after the corresponding textual content in the Confluence page. It does not replace text.

---

### 3. Changes to `fc-functional-document`

**File:** `skills/fc-functional-document/SKILL.md`

Add a **Diagrams Protocol** block. Thresholds are deliberately higher than in `fc-solution-design` — the Functional Document is a sign-off document where contractual text precision takes precedence.

**Autonomous trigger — Section 5.X.2 (Solution per functional area):**
After writing the solution description for each functional area, evaluate:
- Does the process have 6 or more steps? OR
- Are there branching paths relevant to the client's understanding?

If either condition is true, propose:
> "La descripción del proceso de [Área] es extensa. Un diagrama de flujo puede ayudar al cliente a validar que lo ha entendido correctamente antes de firmar. ¿Lo incluyo?"

Wait for confirmation before invoking `fc-lucidchart`.

**Autonomous trigger — Section 6.3 (Role Hierarchy):**
Same logic as `fc-solution-design`: if the hierarchy has more than 2 levels, propose a visual diagram.

**Explicit trigger:**
If the consultant requests a diagram at any point, invoke `fc-lucidchart` without conditions.

**Explicit restriction:** Do not propose diagrams for architecture, data model, or integrations under any circumstance. The Functional Document contains no technical diagrams.

**Placement:** Diagram embed inserted immediately after the corresponding section content. Text is not replaced.

---

### 4. Changes to `fc-assistant`

**File:** `skills/fc-assistant/SKILL.md`

Add one row to the Skill Registry table:

| Skill | Type | Role |
|---|---|---|
| fc-lucidchart | Utility | Creates diagrams in Lucid Chart and embeds them in Confluence. Invoked by fc-solution-design and fc-functional-document when a diagram adds significant value for the reader. |

No changes to Phase Detection logic, execution modes, or cross-cutting behaviors.

---

## Files Changed

| Action | File |
|---|---|
| Create | `skills/fc-lucidchart/SKILL.md` |
| Modify | `skills/fc-solution-design/SKILL.md` |
| Modify | `skills/fc-functional-document/SKILL.md` |
| Modify | `skills/fc-assistant/SKILL.md` |

---

## Constraints

- Lucid diagrams are embedded in Confluence (macro), never linked only.
- Diagrams always complement text — never replace it.
- Architecture, data model, and integration topology diagrams are explicitly out of scope.
- Autonomous proposals always require consultant confirmation before `fc-lucidchart` is invoked.
- `fc-lucidchart` is a utility skill — it is never invoked directly by the user.
