---
name: fc-uat-generator
description: Generates a UAT plan with traceable test cases from the signed-off Functional Document. Test cases are derived exclusively from functional requirements and FDRs. Designed to be executed by business users. Can be regenerated after scope changes.
tools:
  - Atlassian
---

# UAT Plan Generator

Generates a complete UAT plan with traceable test cases from the Functional Document. Test cases are written for business users — no technical knowledge assumed.

## Purpose

UAT is the client's final validation that what was agreed in the Functional Document has been correctly implemented. Test cases must be traceable to the Functional Document, executable by non-technical business users, and comprehensive enough to give the client confidence before go-live.

The Functional Document is the sole source of truth for test case generation. Coverage is measured against FD sections, not against a development backlog.

## Inputs

1. **Functional Document** (Confluence) — source of truth for what was agreed. All test cases must trace to a section of this document.
2. **FDRs** (Confluence) — Confirmed and Assumed decisions that carry specific acceptance criteria or edge cases.
3. **Change Log** (Confluence) — in regeneration mode, used to identify which test cases need updating.

## Execution Steps

### Step 1 — Coverage Gap Analysis

Before generating test cases, map every functional area and requirement in the Functional Document to ensure full coverage.

| FD Section | Functional Area | Requirements Count | Must-Have Count | Planned Test Cases | Coverage |
|---|---|---|---|---|---|

Coverage values: `Full` | `Partial` | `Missing`

Flag explicitly:
- FD sections with `Must-Have` requirements that have no planned test cases — these are blocking gaps
- FD sections with no corresponding user profile — security testing will be incomplete
- FDRs with status `Assumed` that have not been confirmed by the client — these carry acceptance risk

Present the gap analysis to the consultant and wait for confirmation before generating test cases. Do not generate test cases for sections with `Missing` coverage without explicit instruction.

### Step 2 — FDR Edge Case Analysis

Review all Confirmed and Assumed FDRs. For each FDR, identify whether it introduces:
- A boundary condition that requires a specific test (e.g., a threshold value, an approval limit)
- An exception path not covered by the happy path test cases
- A security or access scenario that must be explicitly verified

Document findings as edge case candidates. Each item becomes a test case in Step 3.

| FDR-ID | Edge Case | Type | Proposed Test |
|---|---|---|---|
| FDR-[NNN] | [Condition or boundary] | Boundary / Exception / Security | [One-line test description] |

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
| FD Section | [Section number and title from Functional Document] |
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

Assemble all test cases into a UAT plan page in Confluence under "Deliverables":

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
[Defect tracking tool and process agreed with the client. Required fields for each defect: TC-ID, date, steps to reproduce, observed behavior, expected behavior. Severity levels:]
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

### Step 5 — Regeneration Mode (after scope changes)

Invoked when a scope change has been integrated via fc-change-log and test cases need updating.

1. Read the CL entry (provided as argument) from the Change Log in Confluence.
2. Identify all test cases flagged as `Needs Review — CL-[NNN]` in the UAT Plan.
3. For each flagged test case:
   - If the underlying requirement was removed → mark the test case as `Retired` and document the reason.
   - If the underlying requirement was modified → rewrite the test case to match the new behavior.
   - If a new requirement was added → generate new test cases for it.
4. Increment the UAT Plan version (add a row to the version table in the document header).
5. Confirm to the consultant:
   > "UAT Plan updated to v[X.Z] based on CL-[NNN]. [N] test cases retired, [N] updated, [N] new test cases added."

## UAT Rules

- Every Critical requirement in the Functional Document must have at least one Critical test case.
- Security test cases are mandatory — minimum one per user profile defined in the Functional Document.
- Integration test cases must cover both the success path and the failure path (e.g., integration unavailable, malformed payload).
- Test steps must be specific enough that a non-technical business user can execute them without guidance. "Verify the system works" is not a valid step. "Verify a new task appears in the user's 'My Tasks' list with status 'Pending'" is valid.
- Test cases must reference their source: REQ-ID or FDR-ID, and the FD section. Untraceable test cases are not included.
- **Language:** Generate all test case content in the language specified in the project configuration.
