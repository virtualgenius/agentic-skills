---
name: refine
description: Review and tighten work items in 3-4 passes before implementation.
---

Do a thorough review, proofreading, refining, and polishing of work items to ensure workers (human or agent) have as smooth a time as possible when implementing them.

Works against any tracker; the default is a markdown checklist in `docs/PLAN.md` (see `adapters/markdown-tasks.md`).

## Scoping (CRITICAL)

- If invoked with a specific item ID or title, review ONLY that item and its direct children (if it is a parent / epic). Do NOT review unrelated open items.
- If invoked with no arguments, review all open items.

## Scope guard (CRITICAL)

- Do NOT expand the scope of any item. Only clarify, correct, or refine what is already there.
- If an item is missing something important, add a note flagging it for the user (e.g., "Note: this may also need X, but that is not currently in scope"). Do not silently add scope.
- Never add new items during review. If you discover missing work, mention it in your review output so the user can decide whether to file it.
- Do not promote observations into implementation tasks. "Credentials are hardcoded" is an observation; "migrate credentials to env vars" is scope expansion.

## Review checklist

- Clear, unambiguous titles and descriptions.
- Sufficient technical detail for implementation (clarify what exists, do not add new requirements).
- All code examples and designs follow the project's coding style and design guidelines (see `CLAUDE.md` / `AGENTS.md`).
- Correct dependencies (no missing or circular deps).
- File paths and code references are accurate.
- Edge cases and acceptance criteria are documented (only for behavior already described in the item).
- No duplicate or redundant items.
- Items with code changes have acceptance criteria written as observable behavior (a worker should be able to write a failing scenario-level behavioral test from the description alone, before writing any unit tests or production code). Criteria that describe implementation details rather than what the user/system observes need rewriting.
- No item modifies deployment config (pipeline YAML, `appsettings*`, connection strings, infra-as-code) unless its title and description explicitly say so.

## Passes

Run 3-4 passes, each with a different lens. Stop when a pass finds nothing new.

1. **Clarity pass**: titles and descriptions unambiguous, file paths accurate.
2. **Behavior pass**: acceptance criteria are observable, testable, and match the item's title.
3. **Parallelization pass**: which items can run simultaneously? Flag shared files or hidden ordering.
4. **Scope pass**: any item quietly expanded beyond the plan? Any items that should be split?
