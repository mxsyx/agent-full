---
name: todo-session-planner
description: "Categorize and split TODO lists (TODO/ROADMAP/backlog) into appropriately scoped sessions or task groups, producing actionable groupings, dependencies, and parallel execution plans. Use when the user asks whether TODOs should be completed in one session or split across multiple sessions, how to merge or organize TODOs, whether work should be treated as one task or multiple tasks, which tasks can run in parallel, or how to structure execution sessions. Triggers on: group TODOs, split TODOs into sessions, task batching, work breakdown, session planning, parallel planning, merge TODO items, 合并 TODO, 任务分组, 会话切分, 任务编排, 并行规划."
---

# TODO Session Planning (Task Grouping & Session Planner)

## Overview

Split a TODO list into task groups, each corresponding to one session or a single execution unit.

The goal is to ensure that each group has self-contained context, minimizes overlapping code modification areas, reuses external resources where possible, and maintains clear dependencies and safe parallel execution boundaries between groups.

## When to Use

Use this skill when the user:

- Asks whether a set of TODOs should be completed in one session or split across multiple sessions.
- Asks how to merge, categorize, sequence, or organize TODO items.
- Asks which tasks can run in parallel or how sessions should be divided.
- Needs an actionable execution plan for a large TODO list.

## Core Principles

Use only three grouping dimensions, evaluated in descending order of priority:

1. **Shared code hotspots**: Group tasks that modify the same set of files and share the same tests. Otherwise, keep them separate. This is the primary rule for preventing context interference.
2. **Shared external resources**: Group tasks blocked by the same credentials, accounts, devices, or manual operations. Process them as a batch to avoid repeated environment setup and waiting.
3. **Strong dependency chains**: When A is a prerequisite for B, organize A → B as sequential steps within the same group rather than splitting them into separate sessions that require handoff documents to transfer state.

**Grouping boundary**: Merge two tasks if and only if they share both the same file area and the same test suite, or depend on the same batch of external resources.

## Workflow

### Step 1: Ingest and Parse

Read the complete TODO list, not just excerpts pasted by the user, and analyze it together with the following context:

- Priority labels for each item (P0/P1/P2, etc.).
- Definition of done for each item.
- Statements in the list's introduction about what has already been implemented versus what remains incomplete, as these often determine the nature of the tasks.
- Relevant directory structure, module sizes, and existing test locations.

**Critical action**: Identify TODO items containing terms such as "real-world testing," "acceptance testing," "end-to-end," or "real environment." Their completion criteria usually depend on **external resources**, rather than code changes.

### Step 2: Classify

Assign each TODO item to one of four categories (see `references/grouping-framework.md`):

| Category                        | Characteristics                                                                            | Grouping Strategy                                         |
| ------------------------------- | ------------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| Acceptance / Real-world Testing | Minimal coding; blocked by real credentials, accounts, clean devices, or manual operations | Group by shared external resource batches                 |
| Implementation                  | Independent modules or directories with substantial scope                                  | Assign each to a separate group; allow parallel execution |
| Core / Refactoring              | Modifies shared foundational modules and affects regression tests                          | Group by shared code hotspots                             |
| UI / Configuration              | Settings pages, user-facing messages, and status presentation                              | Group by UI surface or configuration area                 |

### Step 3: Group Tasks

Apply the three core principles in order to produce an initial grouping.

Then validate each group:

- Does the group contain **conflicting write operations** targeting different regions of the same file? If these operations must run sequentially, keep them in one group and explicitly specify their execution order.
- Are there tasks within the group that **share no resources at all**? If so, the grouping is incorrect; split them apart.
- Does the group's workload exceed the reasonable context capacity of a single session? Refer to the workload heuristics in `references/grouping-framework.md`.

### Step 4: Sequence and Parallelize

1. Build an **inter-group dependency graph** identifying which groups are prerequisites for others (gate groups).
2. Identify **parallelizable groups** that share no files and have no dependency relationships.
3. Provide **execution recommendations**: Start gate groups first while running independent groups in parallel. For parallel groups with potential file conflicts, recommend separate Git worktrees or subagents.
4. Explicitly separate **implementation** from **real-world testing** within the same group. Implementation can begin without external resources and should not remain idle while waiting for credentials or access.

### Step 5: Produce the Output

Start with a one-sentence conclusion, followed by the rationale, grouping table, and recommended execution order.

Always use the following template:

```text
**Conclusion**: (Do not put everything into one session / do not create one session per TODO). Divide the work into N groups based on <dimension>.

## Rationale
(Task classification table + 2–3 reasons for the grouping decisions)

## Recommended Groups
| Group | Tasks | Prerequisites | Parallelizable |

## Key Considerations
(Explain each case where merging is not recommended or sequential execution is required)

## Execution Recommendations
(Startup order, parallel execution strategy, and handoff approach)
```

**The output must conclude with all three of the following:**

- Explicitly identify the **gate group(s)** that unlock the largest number of downstream tasks.
- Explicitly identify any **manual operations that AI cannot perform independently**, such as console registration, account authorization, or physical-device acceptance testing.
- Require every session to **update the TODO list's completion checkboxes** before ending, using the updated list as the source of truth for cross-session handoffs.

## Common Pitfalls

- **Grouping by priority**: P0/P1/P2 indicates importance, not coupling. Priority must not be used as a grouping criterion.
- **One TODO per session**: Ignores shared code hotspots and causes repeated exploration of the same codebase areas.
- **Putting everything into one session**: Large independent modules can exhaust the context window, while waiting for external resources leaves the session idle.
- **Conflating testing with implementation**: Implementation can proceed offline, whereas real-world testing requires an actual environment. Their blocking conditions are fundamentally different.
- **Grouping without dependency planning**: A grouping plan without a dependency graph and parallel execution recommendations is not directly actionable.

## Resources

- `references/grouping-framework.md` — Detailed classification criteria for the four task categories, workload heuristics, dependency-handling checklist, and complete examples.
