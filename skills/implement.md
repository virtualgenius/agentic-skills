---
name: implement
description: Spawn a parallel agent team to implement work items using double-loop BDD/TDD.
---

Start working on the specified work item(s) by spawning an agent team to implement them in parallel.

Usage: `/implement <id-or-title> [<id-or-title> ...]`

Work items can come from any tracking source your project uses (see `adapters/markdown-tasks.md` for the default: a checklist in `docs/PLAN.md`). Adapt the lookup commands in step 1 to your work tracker. Everything else is the same.

## Steps

1. **Gather context**: For each work item, look up the full description in the project's work tracker. Also read `CLAUDE.md` / `AGENTS.md`, `VISION.md` (if present), and the source files referenced in the item's description.

2. **Mark in-progress**: Update each item's status in the work tracker to `in_progress`. With the default markdown adapter, change `- [ ]` to `- [~]` (or whatever convention the project uses) on the matching line.

3. **Plan the work**: Analyze the items for:
   - **Parallelizability**: Which items touch different files and can be worked on simultaneously? Group into independent work streams.
   - **Dependencies**: Which items share files or have logical ordering? Sequence these within a stream.
   - **Double-loop applicability**: Most feature and bug items benefit from the double-loop process. Skip only for pure-CSS changes, config tweaks, or documentation.

4. **Spawn agent team**: Create a team and spawn parallel agents (one per independent work stream) using the harness's agent-spawning tool. Each agent's prompt must include:
   - The full description of the item(s) it's responsible for.
   - Instruction to follow double-loop BDD/TDD as defined in `CLAUDE.md` / `AGENTS.md`: write a failing scenario-level behavioral test FIRST (outer loop), then run inner-loop RED / GREEN / REFACTOR cycles to make it pass, then return to verify the scenario test passes. Print a scenario checklist at the start and update it at each OUTER GREEN. Skip the outer loop only for pure-CSS changes, config tweaks, or documentation.
   - Instruction to run the project's test command (discover it: `package.json` scripts, `Makefile` targets, `pytest`, `cargo test`, `go test ./...`, etc.) and verify tests pass.
   - Instruction to follow all `CLAUDE.md` / `AGENTS.md` guidelines (commit message conventions, code design, no over-engineering).
   - The specific files to read and modify.
   - Instruction to commit completed work with descriptive commit messages.
   - **CRITICAL**: Instruction to NEVER modify deployment config files (pipeline YAML, `appsettings*`, connection strings, env config, infra-as-code) unless the item explicitly describes a config change. If the agent discovers a config change is needed, it must skip that part and report it back instead of implementing it.

5. **Coordinate**: As agents complete, review their work. Ensure:
   - All tests pass. Report the total test count.
   - No file conflicts between parallel agents.
   - Each item's acceptance criteria are met.
   - No deployment config files were modified unless explicitly authorized by the item.

6. **Close items**: Mark each completed item as done in the work tracker. With the default markdown adapter, change the checkbox to `- [x]`.

7. **Wrap up**: Stage and commit all changes. Show `git log --oneline` of new commits. **Do not push.** Let the user review and decide when to push (use `/preflight` first).

## Key principles

- **Maximize parallelism**: If 3 items touch 3 different files, spawn 3 agents simultaneously.
- **Double-loop by default**: Outer loop (failing scenario test) before inner loop (unit TDD). Skip outer loop only for pure-CSS changes, config tweaks, or documentation.
- **Small commits**: Each item gets its own commit (or one commit per TDD cycle if the item is large).
- **No over-engineering**: Implement exactly what the item describes, nothing more.
- **No surprise config changes**: Deployment config is off-limits unless the item says otherwise.
- **No auto-push**: Commit locally; leave pushing to the user.
