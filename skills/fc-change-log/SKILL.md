# FC Change Log

Manages all changes to the Functional Document after client sign-off. Every modification to a signed-off document is a contractual event — it must be explicit, traced, and reflected across all downstream artifacts.

## Purpose

A signed-off Functional Document is the contract between WAM Global and the client. Changes to it after sign-off require:
1. An approved Scope Change Request (from fc-scope-register)
2. A formal entry in this Change Log
3. A new document version
4. An impact assessment on UATs and training materials

Never modify the Functional Document without a corresponding Change Log entry. Silent changes invalidate the contract and create traceability gaps.

---

## Change Log Entry Format

```markdown
## CL-[NNN] — [Short title]

**Date:** YYYY-MM-DD
**Requested by:** [Client stakeholder name / WAM internal]
**SCR reference:** SCR-[NNN]
**Status:** Pending Review | Approved | Rejected | Integrated

### Description
[What is changing in the Functional Document. Specific — which sections, what is being added, removed, or modified, and why.]

### Functional Document Sections Affected
| Section | Change Type | Description |
|---|---|---|
| [e.g. 5.3 — Opportunity Management] | Added / Modified / Removed | [Specific change] |

### Impact: UAT Test Cases
| TC-ID | Impact | Action Required |
|---|---|---|
| TC-[NNN] | Invalidated / Needs Review / Unaffected | [What the consultant must do] |

### Impact: Training Materials
| Profile | Impact | Action Required |
|---|---|---|
| [Profile name] | Needs Update / Unaffected | [What module to update] |

### FDR Impact
| FDR-ID | Impact | Action Required |
|---|---|---|
| FDR-[NNN] | Needs Revision / Unaffected | [What to update in the FDR] |

### New FD Version
**Previous version:** [X.Y]
**New version:** [X.Z]
**Version type:** Minor (clarification or correction, no scope change) | Major (approved scope change)
```

---

## Change Log Confluence Document Structure

```markdown
# Change Log — [Project Name]
Last updated: [date] | Total changes: [N] | Pending integration: [N]

---

## Summary
| CL-ID | Date | Description | SCR | FD Version | Status |
|---|---|---|---|---|---|

---

## Pending Changes *(approved but not yet integrated into FD)*

[CL entries with Status = Approved]

---

## Integrated Changes *(newest first)*

[CL entries with Status = Integrated]

---

## Rejected Changes

[CL entries with Status = Rejected]
```

**Page location:** Under `Deliverables / Change Log`

---

## Execution Modes

### Mode: register-change

1. **Verify SCR exists.** Check fc-scope-register for an approved SCR matching this change. If no approved SCR exists: stop. Instruct the consultant to create and approve an SCR first via fc-scope-register before registering a change.
2. **Create CL entry** with status `Pending Review`. Assign the next sequential CL-ID.
3. **Run impact assessment** (see Mode: assess-impact below) and populate the impact tables.
4. **Present the complete CL entry** to the consultant for review and confirmation before saving to Confluence.
5. Once confirmed: publish the entry to the Change Log page with status `Pending Review`. Do not change the FD yet.

### Mode: assess-impact

Given a CL-ID (or a description of the change), determine downstream impact:

**UAT impact:** Read all test cases in the UAT Plan. For each test case, check:
- Is the REQ-ID or FDR-ID it references in the affected FD sections? → `Needs Review`
- Does it test a user action or system behavior described in the affected sections? → `Needs Review`
- No connection to the affected sections? → `Unaffected`

**Training impact:** Read training materials by profile. For each profile, check:
- Is the changed functional area covered in their User Guide or Quick Reference Card? → `Needs Update`
- No overlap? → `Unaffected`

**FDR impact:** For each Confirmed or Assumed FDR:
- Does the change alter the decision or context of this FDR? → `Needs Revision`
- No overlap? → `Unaffected`

Present all three impact tables and wait for confirmation.

### Mode: integrate-change

Pre-condition: CL entry must be in `Approved` status. If status is `Pending Review`, ask for explicit approval before proceeding.

1. **Update the Functional Document** in Confluence:
   - Increment version number (minor for clarifications, major for scope changes)
   - Add a row to Section 1 (Document Control) with: new version, date, author, and CL-ID reference
   - Apply changes to all affected sections
   - Update Section 3.3 (Assumptions) if any Assumed FDR was affected
2. **Update affected FDRs:** Add a Revision History row to each impacted FDR, referencing this CL-ID.
3. **Mark UAT test cases:** Add a `[Needs Review — CL-[NNN]]` flag to each affected test case in the UAT Plan.
4. **Mark training modules:** Add a `[Needs Update — CL-[NNN]]` flag to each affected section in the training materials.
5. **Update the CL entry status** to `Integrated` and record the integration date.
6. **Confirm to the consultant:**
   > "Change CL-[NNN] integrated. Functional Document updated to v[X.Z]. [N] UAT test cases flagged for review. [N] training modules flagged for update. [N] FDRs revised."

### Mode: list-changes

Display the Change Log summary table from Confluence. Highlight entries with `Pending Review` or `Approved` status that have not yet been integrated.

---

## Rules

- Every change to a signed-off Functional Document requires a CL entry. No exceptions.
- An approved SCR must exist before a change can be registered in the Change Log.
- Never delete a CL entry. If a change is reversed, create a new CL entry documenting the reversal.
- **Version numbering:** X.Y format. X = major version (scope changes that add or remove deliverables). Y = minor version (clarifications, corrections, assumption updates). Initial signed-off version is always 1.0.
- UAT test cases marked `Needs Review` must be reviewed before the next UAT session. Running UAT with stale test cases produces invalid results.
- The Change Log is a living Confluence page — updated throughout the project, never archived or recreated.
