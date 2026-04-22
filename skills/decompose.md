---
name: decompose
description: Break a plan into small, independent work items ready for implementation.
---

File a set of detailed work items to track all the work from the plan, focusing carefully on dependencies, designs, and potential parallelization.

Default output is a markdown checklist in `docs/PLAN.md` (see `adapters/markdown-tasks.md`). If your project uses a different tracker (GitHub Issues, Jira, ADO, beads, etc.), substitute its create/link operations but keep everything else the same.

## Scope rules

- Only file items for work explicitly described in the plan. Do not add infrastructure improvements, security hardening, DevOps changes, or refactoring unless the plan specifically calls for them.
- If you notice something that should be done but is not in the plan, file it as a separate low-priority item with a note like "Not in current plan; flagging for review." Do not bundle it with in-scope work.
- Each item should represent a single, testable slice of user value. If an item touches both application code and deployment config, split it into separate items.
- Write acceptance criteria as observable behavior (what the user/system sees), not implementation details. A worker should be able to write a failing scenario-level behavioral test directly from the acceptance criteria.
- Never file items that modify deployment pipeline files (YAML, `appsettings*`, connection strings, env config, infra-as-code) without explicit plan language authorizing the change.
- Before filing each item, verify it would pass the `/refine` review checklist (see `refine.md`). File items that are implementation-ready from the start.

## Structure of a good item

Each item should have:

- **Title**: a clear, unambiguous summary of the slice (what the user/system will observe when done).
- **Description**: enough technical detail for implementation, including file paths and any known constraints.
- **Acceptance criteria**: observable behavior. A checklist, or a scenario stated in plain language, that a worker could turn into a failing behavioral test.
- **Dependencies**: other items this one depends on, if any.

Prefer vertical slices (a thin path through the stack that delivers observable behavior) over horizontal layers (all the model changes, then all the API changes, then all the UI changes). Vertical slices are independently testable and deliver user value.
