---
name: fc-scope-register
description: Manages the project scope register. Tracks in-scope items, explicit exclusions, and scope change requests. Invoked by other skills whenever something may affect agreed scope. Primary defense against scope creep.
argument-hint: Use "add [item] to scope", "exclude [item] from scope", "check scope for [item]", "log scope change request for [item]", or "generate scope register" to publish to Confluence.
tools:
  - Atlassian
---

# Scope Register

Scope creep is the primary risk in consulting engagements. This skill is the single source of truth for what is and is not included in the project. Other skills invoke it when they detect a potential scope boundary issue.

## Scope Register Structure

**In-Scope Items**
| SCO-ID | Description | Salesforce Area | Source Document | Date Added | Notes |
|---|---|---|---|---|---|

**Out-of-Scope Exclusions**
| EXC-ID | Description | Reason for Exclusion | Source | Date |
|---|---|---|---|---|

**Scope Change Requests (SCR)**
| SCR-ID | Description | Requested By | Date | Impact Assessment | Status |
|---|---|---|---|---|---|

SCR Status values: `Pending Approval` | `Approved` | `Rejected`

## Execution Modes

### Mode: add-to-scope

Verify the item is traceable to commercial materials or an approved SCR. If traceable, add to In-Scope with the source reference. If not traceable, create a Scope Change Request and do NOT add the item silently.

### Mode: exclude

Add the item to Out-of-Scope exclusions with an explicit reason. This prevents future misunderstandings and provides a clear record if the client raises it later.

### Mode: check-scope

Given a description of a requirement or design decision, determine whether it falls within agreed scope. Return one of:

- **IN SCOPE** — with reference to the source document or SCR that authorises it
- **OUT OF SCOPE** — with reference to what was agreed and where the boundary was set
- **AMBIGUOUS** — with a recommendation to open an FDR before proceeding

### Mode: scope-change-request

Document a new SCR. Include:

- Description of what is being requested
- Who requested it and when
- Impact assessment: effort signal (Low / Medium / High), timeline impact, licensing implications, dependency on existing in-scope items
- Recommended position (Approve / Reject) with rationale

Do not approve or reject SCRs unilaterally. This requires explicit agreement from the client and WAM Global.

**Post-approval trigger:** When an SCR transitions to `Approved` status and a Functional Document already exists (Phase 4 complete or past), immediately invoke fc-change-log in `register-change` mode. An approved SCR that does not produce a corresponding Change Log entry and a new FD version is incomplete. The SCR is not considered fully closed until the FD has been updated and the CL entry is `Integrated`.

### Mode: generate-scope-register

Compile the full Scope Register and publish it to Confluence under "2. Solution Design" > "Scope Register". Include all three tables, a revision history table, and a last-updated timestamp.

## Scope Creep Detection Rules

These rules apply to all skills. Any trigger should invoke `check-scope` before proceeding with design.

- Any requirement not traceable to commercial materials → potential SCR
- Any integration not mentioned in the original proposal → out of scope by default until an SCR is approved
- Any Salesforce product requiring a license not included in the agreed package → out of scope
- Any process area not covered in the Workshop Guide → flag before designing
- Scope absorbed silently without a flag = project failure. Always surface it explicitly.

## Confluence Output

- **Page location:** "Scope Register" under "2. Solution Design"
- **Nature:** Living document — updated throughout the engagement, not generated once at the end
- **Header:** Last-updated date and document version displayed prominently at the top
- **Revision table:** All changes logged with date, author, and description of what changed
