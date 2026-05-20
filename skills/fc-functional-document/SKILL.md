---
name: fc-functional-document
description: Generates the formal Functional Document from the approved Solution Design. The client-facing, sign-off document that defines the agreed scope and solution. Professional, precise, non-verbose. The contractual reference for the implementation.
tools:
  - Atlassian
---

# FC Functional Document

Generates the formal Functional Document from an approved Solution Overview. This is the client-facing, sign-off document — the contractual reference for the implementation. It must be precise enough for the technical team to build from, and clear enough for the client to understand and sign.

---

## Purpose

The Functional Document is not a requirements dump. It is a designed solution description: what will be built, how it will work, what is explicitly excluded, and what assumptions the project is proceeding on. It is the document a stakeholder signs to authorize implementation.

Once signed, it is the binding reference for scope disputes. Section 3.2 (Out of Scope) is the most legally and commercially significant section in the document.

---

## Pre-flight Checks

Run all checks before generating anything. If any check fails: **stop**, list what needs to be resolved, and do not begin document generation.

- [ ] Output language confirmed in `agent-params.md`. **Every word of the document** — including section headings, table headers, labels, and sign-off text — must be in that language.
- [ ] Solution Overview exists in Confluence (Solution Design / Solution Overview) and status = **Approved**
- [ ] Zero Open FDRs — all FDRs must be Closed before generation begins
- [ ] Scope Register is current and agreed
- [ ] Requirements Register has no items with status Ambiguous or Conflicting

If all checks pass, confirm:

> "Pre-flight complete. Proceeding to generate the Functional Document."

---

## Document Structure

Generate the complete Functional Document using this structure. Every section is required. If a section does not apply, include it and state explicitly that it is not applicable and why.

```markdown
# Functional Document — [Project Name]
Version: 1.0 | Date: [date] | Status: Draft for Client Review
Prepared by: WAM Global | Client: [Client Name] | Language: [language code from agent-params.md]

---

## 1. Document Control
| Version | Date | Author | Description of Changes |
|---|---|---|---|
| 1.0 | [date] | WAM Global | Initial version |

---

## 2. Executive Summary
[3–5 sentences: client context, the business problem being solved, the chosen solution, the value expected. No Salesforce jargon. Written for a business executive who has not attended the workshops.]

---

## 3. Project Scope

### 3.1 In Scope
[Numbered list of included functional areas and concrete deliverables. Specific — not "CRM implementation" but "Opportunity management for the commercial sales team, including pipeline reporting and sales stage automation."]

### 3.2 Out of Scope
[Numbered list of explicitly excluded items, each with a brief reason. Include **both**:
- Items never included in scope
- Items that were discussed during workshops or design, evaluated, and explicitly decided against — with the reason

This section prevents future disputes. Ambiguity about exclusions causes the most project disputes. Be specific.]

### 3.3 Constraints
[Known constraints that affect the solution. Examples: implementation timeline, licensing agreed, third-party system limitations, client IT policies, data residency requirements, support model post-go-live.]

---

## 4. Stakeholders and User Profiles
| Profile | Role Description | Approx. Volume | Key Responsibilities in System |
|---|---|---|---|
| | | | |

---

## 5. Solution Design

[One subsection per functional area. Use plain language throughout — no Salesforce jargon in sections 2 through 5. Use the terms the client uses for their own processes.]

### 5.X [Functional Area Name]

#### 5.X.1 Current Situation
[How this process works today. 2–4 sentences, drawn from the client's own descriptions in workshops. Factual — not evaluative.]

#### 5.X.2 Solution
[How it will work in Salesforce. User perspective, plain language. What the user does. What the system does in response. What the outcome is. Cover the main path and the most relevant exception paths. No jargon.]

#### 5.X.3 Salesforce Capabilities Used
[Bullet list of standard Salesforce features leveraged, in plain terms. Examples: "Automated notifications when a record changes status", "Role-based visibility so each team sees only their own data". Not feature API names.]

#### 5.X.4 User Access
[Who can do what in this functional area. Table format.]

| Profile | Can Do | Cannot Do |
|---|---|---|
| | | |

---

## 6. Security Model

### 6.1 Access Principles
[2–3 sentences on the security philosophy applied to this project: what drives visibility decisions, the default position on data access, and how exceptions are granted.]

### 6.2 Organization-Wide Defaults
| Object | Default Access | Reason |
|---|---|---|
| | | |

### 6.3 Role Hierarchy
[Text tree — plain format. Each role on its own line, indented to show hierarchy.]

```
[Top Role]
├── [Role]
│   └── [Role]
└── [Role]
```

### 6.4 Profiles
| Profile | User Type | Estimated Volume |
|---|---|---|
| | | |

### 6.5 Permission Sets
| Permission Set | Purpose | Granted To |
|---|---|---|
| | | |

### 6.6 Sharing Rules
| Object | Rule | Shared With |
|---|---|---|
| | | |

---

## 7. Integrations
| System | Direction | Frequency | Data Exchanged | Pattern | Notes |
|---|---|---|---|---|---|
| | | | | | |

[If no integrations are in scope: state "No system integrations are in scope for this project."]

---

## 8. Data Migration
[If in scope: objects to migrate, estimated volumes, data quality requirements, migration approach (Big Bang or Phased), cutover strategy, who is responsible for data extraction and cleansing.]

[If out of scope: "Data migration is out of scope for this project. All data will be entered manually or imported by the client post-go-live."]

---

## 9. Reporting and Analytics
| Report / Dashboard | Audience | Description |
|---|---|---|
| | | |

[If no reporting is in scope: state it explicitly.]

---

## 10. Deliverables
[High-level list of what will be built — described in functional terms the client can verify. Not technical specifications. What the client receives and can use after go-live.]

---

## 11. Decisions Log
| FDR | Decision | Date Closed |
|---|---|---|
| FDR-XXX | [Decision description] | [date] |

---

## 12. Glossary
| Term Used in Document | Plain Language Definition |
|---|---|
| | |

---

*Sign-off*

By signing this document, both parties confirm agreement with the scope, solution design, and assumptions described above. Any item listed in Section 3.3 (Assumptions) that the client does not explicitly confirm is accepted as-is and will be treated as agreed for the purposes of implementation.

| Role | Name | Signature | Date |
|---|---|---|---|
| Client Sponsor | | | |
| WAM Global — Functional Consultant | | | |
```

---

## Writing Rules

These rules are mandatory. Deviation degrades the document's contractual and communicative value.

| Rule | Detail |
|---|---|
| Language | Every word — headings, tables, labels, sign-off text — must be in the language from `Output language` in `agent-params.md`. No English defaults. |
| No verbose | Every sentence carries information. Delete filler. If a sentence can be removed without losing meaning, remove it. |
| Sections 2–5 | Client-facing. Zero Salesforce jargon. Use the terms the client uses for their own processes, people, and data. |
| Sections 6–10 | Technical enough for the implementation team. Precision matters more than readability. |
| Section 3.2 | The most important section in the document. Ambiguity about exclusions causes the most project disputes. Include everything that is out of scope — both items never discussed and items explicitly rejected during workshops or design. When in doubt, add more detail. |
| No design decisions in prose | Every decision is in a table or structured list, traceable to an FDR. Prose paragraphs do not replace structured entries. |
| Version discipline | Each revision increments the version number. All changes are tracked in Section 1. Never overwrite a version without incrementing. |
| Completeness | Every section is present. If a section does not apply, it is included with an explicit statement that it is not applicable and why. Blank sections are not acceptable. |

---

## Publishing

- Publish to Confluence: **Deliverables / Functional Document**
- Status on first publish: **Draft for Client Review**

After publishing, confirm to the consultant:

> "Functional Document v1.0 published to [Confluence URL]. Status: Draft for Client Review.
>
> **Recommended next steps:**
> 1. Share with client stakeholders and schedule sign-off review.
> 2. Once the document is signed off → invoke **fc-uat-generator** to generate the UAT Plan.
> 3. Optionally, invoke **fc-architect-handoff** to generate the technical handoff package for the architect agent (can be done at any time after sign-off)."
