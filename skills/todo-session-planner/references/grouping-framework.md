# Task Grouping Framework: Detailed Guidelines

This document provides detailed reference guidelines for `todo-session-planner`. Load it when deeper analysis or judgment is required.

## 1. Identifying and Handling the Four Task Categories

### 1.1. Acceptance / Real-World Validation Tasks

**Identification signals**: Terms such as "real-world testing", "acceptance testing", "end-to-end", "real account", "physical device", "clean device", "power outage", "disk disconnection", "recovery drill", or "integration with a real API". Another indicator is when the definition of done specifies "verify behavior X" rather than "implement X".

**Nature**: These tasks typically require little coding. The main blockers are **external resources** (credentials, accounts, hardware, or manual operations). The expected deliverables are validation reports or capability matrices.

**Handling guidelines**:

- Group tasks by **shared external resources**. Validation tasks requiring the same credentials, device, or manual operation should belong to the same group.
- Order tasks within each group according to their dependencies, usually sequentially (e.g., A validates authorization, then B validates read/write operations using that authorization).
- Explicitly identify steps requiring human intervention (console registration, browser-based authorization, physical device operations). The AI should focus on preparing scripts, running checks, and recording results.
- Separate "script and validator implementation" from "real-world execution" when appropriate. Implementation can proceed before the required external resources become available.

### 1.2. Implementation Tasks

**Identification signals**: Adding an independent module or directory (such as a new Provider or adapter), or substantially completing a self-contained subsystem.

**Nature**: These tasks are relatively large and have self-contained contexts.

**Handling guidelines**:

- Assign each implementation task to its own group. Do not combine multiple implementation tasks into a single group.
- Independent implementation tasks that do not share files may run in parallel using separate worktrees or subagents.
- If the definition of done includes real-world validation, move that portion into an acceptance group or explicitly separate the implementation and validation phases within the group.

### 1.3. Core / Refactoring Tasks

**Identification signals**: Changes to shared foundational modules (core, common, or protocol layers), performance optimizations, data format changes, or additional regression tests.

**Nature**: These tasks have a broad impact and often affect multiple test suites.

**Handling guidelines**:

- Group tasks by **shared code hotspots**. Tasks modifying the same upload, indexing, or manifest logic should belong to the same group.
- Combining related tasks avoids repeatedly exploring and understanding the same code paths.
- If a task changes data formats or version contracts, highlight the change explicitly and check for related pending TODOs that have not yet been included.

### 1.4. UI / Configuration Tasks

**Identification signals**: Settings pages, advanced settings, informational messages, status displays, or connection management interfaces.

**Nature**: Changes are concentrated in the presentation and configuration layers and are often independent.

**Handling guidelines**:

- Group tasks by shared UI areas or configuration surfaces (e.g., tasks related to the Backup Center should be grouped together).
- If a task also requires core changes (such as introducing a new status source), align it with the core group providing that functionality or mark it as "Depends on Group X".

## 2. Task Size Heuristics

Use these heuristics to determine whether a group exceeds the reasonable context budget of a single session:

| Signal                                                                              | Recommendation                                                             |
| ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| A task requires adding or substantially rewriting a single module larger than 15 KB | Assign that task to its own group                                          |
| A group spans more than two independent subsystems that do not share files          | Split the group                                                            |
| A group contains 5 or more TODOs spanning at least 3 task categories                | The grouping is likely incorrect; reassess the classification              |
| Tasks within a group share the same files and test suites                           | Combining them is reasonable                                               |
| A group contains only one small TODO                                                | Consider merging it with other small tasks affecting the same code hotspot |

Rule of thumb: **Aim for 2–4 TODOs per group, focused on 1–2 modules.**

## 3. Dependency Handling Checklist

For each group, verify the following:

1. Does the group have a shared **prerequisite group** (gate group)?
2. Must tasks within the group execute **strictly sequentially**, or can some run in parallel?
3. Does the group **modify the same files** as another group? If so, they must not run concurrently unless properly isolated using separate worktrees.
4. Can implementation proceed while real-world validation must wait for external resources? If so, explicitly define the phase boundary.
5. Are there **shared state files** (such as `TODO.md` or `CHANGELOG`)? If so, define when updates should be written back to avoid concurrent overwrites.

## 4. Output Checklist

Before delivering the grouping plan, verify that it includes:

- [ ] A one-sentence conclusion
- [ ] A task classification table
- [ ] A grouping table (group name / tasks / prerequisites / parallel execution)
- [ ] Reasons for every "Do not merge" or "Must execute sequentially" decision
- [ ] Explicit identification of gate groups
- [ ] Steps requiring human intervention that the AI cannot perform
- [ ] Recommended execution order and parallelization strategy
- [ ] Cross-session handoff procedures (including updating checklist completion states)

## 5. Complete Example

**Input**: A cloud backup project with 18 TODOs (P0–P3), covering OAuth registration, Google Drive end-to-end acceptance testing, recovery on a second device, Dropbox Provider, OneDrive Provider, resumable upload sessions, paginated indexing, attachment verification modes, recovery storage estimation, packaging and distribution, custom Client ID, account UI, network probing, capacity testing, tenant policies, hidden mode, and related work.

**Reasoning**:

- The TODO list introduction states that "the core framework and Google Drive end-to-end workflow have already been implemented, while Dropbox/OneDrive are only Beta placeholders". This helps distinguish real-world validation tasks from new implementation tasks.
- File size reference: The existing Google Drive Provider source file is already 23 KB, indicating that each Provider should have its own group.
- Four workstreams: Real authorization gate (74 → 75 → 76), Provider implementation (82, 83), Google core enhancements (84, 85), and UI/configuration (78, 81, 86, 87).

**Recommended grouping**:

| Group                                       | Tasks                 | Prerequisites                             | Parallel Execution    |
| ------------------------------------------- | --------------------- | ----------------------------------------- | --------------------- |
| A: Real Authorization Gate                  | 74 → 75 → 76, plus 89 | User completes OAuth registration         | Can run alongside B–F |
| B: Dropbox Provider                         | 82                    | Validation phase depends on A             | Can run alongside C   |
| C: OneDrive Provider                        | 83                    | Validation phase depends on A             | Can run alongside B   |
| D: Google Core Enhancements                 | 84 + 85               | None                                      | —                     |
| E: Verification Levels and Budget Semantics | 78 + 79               | None                                      | —                     |
| F: Configuration Entry Points and Packaging | 81 + 80               | None                                      | —                     |
| G: Backup Center Status and Account UI      | 86 + 87               | Part of D                                 | —                     |
| H: Design First                             | 90                    | Lifecycle protocol must be designed first | —                     |

**Key explanations**:

- Group A must execute sequentially and should be completed as one coordinated effort. Its tasks form a single dependency chain, share the same credentials and device, and collectively represent the only gate group.
- Implementation work for B and C can begin immediately. Do not leave these tasks idle while waiting for credentials. Complete their real-world validation phases after A is finished.
- Tasks in D are grouped because they modify overlapping upload-session and manifest-reading code paths, sharing both implementation context and regression tests.
- Task 89 belongs in A because it also requires real-tenant validation. Separating it would result in redundant environment setup.
- Task 90 remains independent because it is a design task explicitly requiring "complete the protocol before evaluation". It should not be mixed into an implementation group.

**Gate group**: A.

**Human intervention required**: OAuth application registration, real-account authorization, recovery acceptance testing on a clean device, and organization-level tenant policy validation.
