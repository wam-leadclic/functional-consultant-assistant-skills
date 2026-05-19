# FC → Architect Handoff

Converts the signed-off Functional Document into a structured technical handoff package. The architect should be able to start the architecture process using only this document — no commercial context, no workshop notes, no re-reading.

## Purpose

This is an **optional step**. Generate this handoff package only if the architect-assistant AI agent will be used downstream. If architecture work will be done by a human architect reading the Functional Document directly, skip this skill.

This document is consumed by an AI agent, not a human. It must be optimized for machine readability:
- No narrative transitions or filler prose
- Every field has a defined value or `N/A` — no blank cells
- Avoid ambiguous pronouns and implicit references
- Every row traces to a REQ-ID or FDR-ID
- Structure over narrative throughout

## What the Architect-Assistant Needs

The downstream architect-assistant agent requires:

- Functional requirements organized by entity or process area
- Business rules (what the system must enforce)
- User profiles and access requirements
- Key business entities and their relationships
- Automation needs at business level — the architect decides the mechanism
- Integration requirements
- Reporting needs
- Known constraints and non-functional requirements
- Open technical questions with functional context

## Pre-flight Checks

Verify all four inputs exist in Confluence before generating anything. If any input is missing: stop and report.

- [ ] Functional Document — status = **Signed Off** (not Draft, not In Review)
- [ ] Integration Map — exists under `Discovery / Integration Map`. If `Has integrations: no` in the project configuration, mark this check as N/A.
- [ ] Requirements Register — exists under `Discovery / Requirements Register`
- [ ] Scope Register — exists under `Solution Design / Scope Register` and is current

## Execution Steps

### Step 1 — Extract and Distill from Functional Document

Read the Functional Document in Confluence. For each section, extract what is relevant to the architect. Omit client-facing narrative, project history, and commercial context. Retain specification-grade information: what the system must do, what it must enforce, who accesses what, and what connects to what.

### Step 2 — Identify Technical Risk Flags

Review all Confirmed and Assumed FDRs for decisions with high technical complexity:

- Real-time integrations at high volume
- Complex sharing rules (large hierarchies, many criteria-based rules)
- Unusual or chained automation sequences
- Data migration complexity or poor source data quality
- Any decision where the consultant wrote "feasibility TBC" or equivalent

Flag these explicitly in Section 13 of the handoff package. Do not attempt to resolve them — surface them for the architect.

### Step 3 — Generate the Handoff Package

Create a Confluence page titled "Technical Handoff Package" under the "Solution Design" space section. Use the following structure exactly:

```
# Technical Handoff Package — [Project Name]
Prepared by: FC Agent | For: Architecture Team
Date: [date] | Source: Functional Document v[X]

---

## 1. Project Context
| Key | Value |
|---|---|
| Project name | [value] |
| Client | [value] |
| Industry | [value] |
| Salesforce products in scope | [comma-separated list] |
| Primary objective | [one sentence — business outcome, not implementation description] |
| Go-live target | [date or TBD] |
| FD version | [X.Y] |
| FD sign-off date | [date] |

## 2. Salesforce Products & Licenses in Scope
| Product | Edition / License | Purpose |

---

## 3. Functional Requirements — Architect View
| REQ-ID | Requirement | Area | Priority | Notes for Architect |

---

## 4. Business Entities
| Entity (Business Name) | Proposed Salesforce Object | Key Attributes (business) | Approx. Volume | Relationships |

---

## 5. Business Rules to Implement
| BR-ID | Rule Description | When Triggered | Objects Affected | Complexity Signal |

Complexity Signal: Low | Medium | High | TBD

---

## 6. User Profiles & Access Requirements
| Profile | Approx. Count | Must See | Must Not See | Must Do | Must Not Do |

---

## 7. Security Requirements

### OWD Recommendations
| Object | Recommended OWD | Reason |

### Role Hierarchy
[Text representation of the proposed hierarchy]

### Sharing Rules Needed
| Object | Criteria | Share With |

### Complex Access Scenarios
[Any scenario requiring manual sharing, account teams, territory management, or Apex-managed sharing — flag here]

---

## 8. Automation Needs (Functional Level)
| ID | When | What Happens | Objects Involved | Frequency | Complexity Signal |

---

## 9. Integration Requirements
| System | Direction | Business Objects | Frequency | Volume | Constraints | Technical Risk |

---

## 10. Reporting Requirements
| Report / Dashboard | Audience | Source Objects | Key Metrics |

---

## 11. Data Migration (if in scope)
| Object | Volume | Source System | Data Quality Notes | Complexity Signal |

---

## 12. Known Constraints
[Technical constraints from client systems, licensing boundaries, timeline constraints, any mechanism exclusions agreed in FDRs]

---

## 13. Technical Risk Flags
| Item | Context | FDR Reference | Risk |

---

## 14. Open Technical Questions
| Question | Context | FDR Reference |

---

## 15. FDR Reference Index
*(Machine-readable reference only. Include all Confirmed and Assumed FDRs. Status must be one of: Confirmed / Assumed. No Open FDRs should appear — these block sign-off.)*
| FDR-ID | Title | Status | Architecture relevance | Decision summary |
```

## Handoff Rules

- **The package must be self-contained.** The architect reads this and nothing else.
- **Every item must trace to a source.** Each row must reference a REQ-ID or FDR-ID. Untraceable items are not included.
- **Do not suggest implementation mechanisms.** Whether something is a Flow, Apex trigger, validation rule, or Process Builder is the architect's decision. Describe what must happen, not how.
- **"Standard Salesforce" is not a valid specification.** Be specific: "Account object", "Opportunity Stages picklist", "Lead assignment rules by territory".
- **Flag scope changes discovered during generation.** If the Functional Document contains something not reflected in the scope register, halt and report it before continuing. Do not silently include out-of-scope items.
- **Optimized for AI consumption.** Every section uses tables or structured lists — no prose paragraphs except Section 1. An AI agent cannot reliably extract structured data from unstructured narrative.
- **No blank cells.** If a value is unknown, write `TBD`. If not applicable, write `N/A`. Blank cells cause parsing failures in downstream agents.
- **No implicit references.** Do not write "as mentioned above" or "per the security model". Every row must be self-contained.
- **This document is optional.** If generated, it must be kept in sync with the Functional Document. If a scope change (CL entry) affects the handoff package, regenerate the relevant sections.
