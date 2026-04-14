---
name: fc-uat-generator
description: Generates a UAT (User Acceptance Testing) plan with traceable test cases from the Functional Document, Jira board, and GitHub repository. Test cases trace to functional requirements. Designed to be executed by business users, not developers.
argument-hint: Requires Functional Document (Confluence) and Jira project key. Optionally provide GitHub repository URL for edge case analysis. Run after development is complete.
tools:
  - Atlassian (Confluence)
  - Atlassian (Jira)
  - GitHub
---

# UAT Plan Generator

Generates a complete UAT plan with traceable test cases from the Functional Document, Jira board, and GitHub repository. Test cases are written for business users — no technical knowledge assumed.

## Purpose

UAT is the client's final validation that what was built matches what was agreed. Test cases must be traceable to the Functional Document, executable by non-technical business users, and comprehensive enough to give the client confidence before go-live. Coverage gaps and scope deviations must be surfaced before test cases are written.

## Inputs

1. **Functional Document** (Confluence) — source of truth for what was agreed
2. **Jira board** — what was actually built (completed tasks, user stories)
3. **GitHub repository** — customizations built, for edge case analysis (optional)
4. **FDRs** — design decisions with specific acceptance implications

## Execution Steps

### Step 1 — Coverage Gap Analysis

Cross-reference every section of the Functional Document against completed Jira items. Produce a gap table:

| FD Section | Requirement | Jira Task | Coverage | Gap |

Coverage values: `Full` | `Partial` | `Missing`

Flag explicitly:
- In-scope requirements with no Jira task — likely not built
- Jira tasks not traceable to the Functional Document — possible scope creep

Present the gap analysis and wait for confirmation before proceeding to test case generation. Do not generate test cases for requirements with `Missing` coverage without explicit instruction.

### Step 2 — GitHub Review

Scan the repository for:
- Custom objects, fields, validation rules, flows, Apex classes and triggers, LWC components
- Behaviors implied by the code that require explicit testing (e.g., a validation rule with a specific condition → test both the pass and the fail case)
- Automation triggers that create edge cases or ordering dependencies

Document findings as an edge case input list. Each item becomes a candidate test case in Step 3.

### Step 3 — Generate Test Cases

For each functional area, generate test cases covering:

- **Happy path** — standard flow works as designed
- **Alternate path** — valid variations of the same process
- **Negative case** — invalid input, access restriction, validation rule triggered
- **Security check** — each profile sees what they should; cannot access what they should not
- **Integration scenario** — data flows correctly to and from external systems (success AND failure)
- **Edge case** — boundary conditions and unusual inputs identified in Step 2

Use this format for every test case:

```
## TC-[NNN] — [Test Case Title]

| Property | Value |
|---|---|
| Functional Area | |
| Requirement | REQ-NNN / FDR-NNN |
| Jira Task | PROJ-NNN |
| Test Type | Happy Path / Alternate / Negative / Security / Integration / Edge Case |
| User Profile | [which profile executes this test] |
| Priority | Critical / High / Medium |

### Prerequisites
[System state before the test begins: existing records required, user session, sandbox configuration, sample data.]

### Steps
1. [Specific action — what to click, what to enter, where to navigate]
2. [Next action]

### Expected Result
[What the user observes. Specific enough that a second person executing the same steps reaches the same conclusion.]

### Pass Criteria
- [ ] [Observable criterion 1]
- [ ] [Observable criterion 2]
```

### Step 4 — Compile UAT Plan

Assemble all test cases into a UAT plan page in Confluence under "3. Project Documentation":

```
# UAT Plan — [Project Name]
Version: 1.0 | Date: [date] | Prepared by: WAM Global

## 1. UAT Scope
[What is being tested. What is explicitly NOT being tested.]

## 2. Test Environment
[Sandbox name, URL, test user setup, sample data requirements]

## 3. Participants
| Role | Name | Functional Areas |

## 4. Schedule
| Session | Date | Functional Areas | Participants |

## 5. Coverage Matrix
| Functional Area | Requirements | Test Cases | Critical Count |

## 6. Test Cases
[All test cases organized by functional area]

## 7. Defect Reporting
[Jira project and issue type for defect logging. Required fields. Severity levels:]
- Critical: functional area is unusable
- High: major process is blocked
- Medium: workaround available
- Low: cosmetic or minor issue

## 8. Sign-Off Criteria
UAT passes when:
- 100% of Critical test cases pass
- ≥ 95% of High test cases pass
- All Critical and High defects are resolved or formally accepted
- Client sponsor signs the UAT completion form
```

### Step 5 — Optional: Create Jira UAT Tasks

If requested, create one Jira task per functional area. Add the corresponding test cases as a checklist within the task description. Link each task to its source requirement.

## UAT Rules

- Every Critical requirement in the Functional Document must have at least one Critical test case.
- Security test cases are mandatory — minimum one per user profile defined in the Functional Document.
- Integration test cases must cover both the success path and the failure path (e.g., integration unavailable, malformed payload).
- Test steps must be specific enough that a non-technical business user can execute them without guidance. "Verify the system works" is not a valid step. "Verify a new task appears in the user's 'My Tasks' list with status 'Pending'" is valid.
- Test cases must reference their source: REQ-ID, FDR-ID, and Jira task. Untraceable test cases are not included.
