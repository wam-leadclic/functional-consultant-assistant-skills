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
  > "This diagram type (architecture / data model / integration topology) is outside the scope of fc-lucidchart and must not be included in functional documentation."

### Step 2 — Authenticate with Lucid

Use `mcp__claude_ai_Lucid__authenticate` to establish the Lucid session.

### Step 3 — Create diagram in Lucid Chart

Create a new Lucid Chart document using the Title and Context provided. Build the diagram from the structured data in Context:

- For `process-flow`: flowchart with numbered steps as nodes, decision points as diamond shapes, directional arrows between steps. Use the exact step descriptions from Context as node labels.
- For `role-hierarchy`: org chart reflecting the hierarchy levels and role names from Context. Each role on its own node, parent-child relationships as connecting lines.
- For `other`: most appropriate diagram type for the described content.

If Lucid tool authentication fails or diagram creation returns an error, halt and report to the calling skill:
> "Failed to create diagram '[Title]' in Lucid Chart. Error: [error description]. The corresponding Confluence section has not been modified."

### Step 4 — Embed in Confluence

Insert the Lucid embed macro into the target section of the target Confluence page, immediately after the corresponding text content. Use the official Lucid Confluence macro. The embed must not replace the existing text.

If the target section is not found on the Confluence page, halt and report to the calling skill:
> "Section '[Target section]' not found on the Confluence page. The diagram has not been inserted."
Do not create the section or append to the end of the page.

### Step 5 — Confirm to calling skill

> "Diagram '[Title]' created in Lucid Chart and embedded in '[Target section]' of [Target Confluence page]."

---

## Constraints

- **Never create:** architecture diagrams, data model diagrams, integration topology diagrams.
- **Always embed** via Lucid macro — never link only.
- **Always place** the embed after the corresponding text content, never replacing it.
- This skill does not decide whether a diagram is appropriate. That judgment belongs to the calling skill.
