# Starter CLAUDE.md

> Copy this file into the root of your project and edit. It's the starter template that the `agentic-skills` skills assume exists. Same content works for `AGENTS.md` if your harness reads that instead.

The skills in this repo are opinionated about a few things. They all flow from one idea: when AI writes most of the code, the work that matters is understanding what to build, expressing it precisely, and checking reality against that intent. The skills assume a project that is set up to support that.

## Work tracking

- Work items live in `docs/PLAN.md` as a markdown checklist (see `adapters/markdown-tasks.md`).
- Each item is a single testable slice of user value, written with acceptance criteria as observable behavior.
- If your team uses a different tracker (GitHub Issues, Jira, ADO, Linear, beads, etc.), swap the create / read / update / close operations in each skill; leave the methodology alone.

## Double-Loop BDD / TDD

When implementing features or fixing bugs, use double-loop development by default. The outer loop anchors observable behavior; the inner loop drives implementation.

### Hierarchy: Scenario > Rule > Example

- **Scenario**: an acceptance criterion describing observable behavior ("Dealer submits duplicate invoice and sees rejection"). Scenarios are the unit of progress.
- **Rule**: a generalized invariant within a scenario ("A dealer cannot submit the same invoice number twice for the same promotion").
- **Example**: a concrete instance illustrating a rule, often expressed as a table row.

### Outer loop (behavioral)

1. **OUTER RED**: Write one failing test for a scenario (exercises enough of the stack to verify behavior; doesn't need to be fully end-to-end).
2. **Verify the failure**: Confirm it fails for the right reason (feature missing, not a broken test).
3. **Inner loop**: Run as many inner TDD cycles as needed to build the pieces.
4. **OUTER GREEN**: Return to the scenario test, confirm it passes.
5. **OUTER REFACTOR**: Cross-module cleanup if needed.
6. Next scenario.

### Inner loop (unit TDD)

Standard RED / GREEN / REFACTOR on pure functions and focused units. One or more inner cycles run inside a single outer-loop iteration.

- **RED**: Test fails for the right reason.
- **GREEN**: Make the test pass by any means.
- **REFACTOR**: Change structure / naming / duplication only; tests must remain passing.

### When to skip the outer loop

- Pure refactors (behavior unchanged, existing tests protect you).
- Pure-CSS changes, config tweaks, documentation.
- Extracting / moving code where existing tests already cover the behavior.

### Progress visibility

Print the scenario checklist at the start of a feature. Update it at each OUTER GREEN. Announce phase transitions inline.

```
[OUTER RED] Dealer submits single-line invoice, sees correct rebate amount
  [RED] rebate amount = unit price * rule percentage
  [GREEN] implementing calculation
  [REFACTOR] extracting rounding helper
[OUTER GREEN] Dealer submits single-line invoice ✓
```

## Code design

- **Pure functions first**: extract logic into testable pure functions. Keep I/O separate from business logic.
- **Single responsibility**: each function does one thing.
- **Low complexity**: target cyclomatic complexity ≤ 3; refactor when > 5.
- **Small functions**: ~25 lines max; extract when longer.
- **Reveal intent through names**: predicate functions (`shouldDoX`) and handler functions (`handleY`) beat inline conditionals.
- **No magic numbers**: extract to named constants.
- **Default to no comments**: well-named code is self-documenting. Only comment WHY (non-obvious business reasons, workarounds, edge cases), never WHAT or HOW.

## Commit messages

Follow Conventional Commits:

```
<type>[optional scope]: <description>
```

Types: `feat`, `fix`, `docs`, `config`, `refactor`, `test`, `chore`.

Group related changes into one commit (a feature plus its tests). Reference work items in commit messages using your tracker's convention (`(#123)`, `(ITEM-1)`, etc.) so `/preflight` can trace scope.

## Scope guards

- Only implement what the work item describes. No infrastructure improvements, security hardening, DevOps changes, or refactoring unless the item calls for them.
- Never modify deployment config files (pipeline YAML, `appsettings*`, connection strings, env config, infra-as-code) unless the item explicitly says so. If you notice a config change is needed, stop and report it instead of making it.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Validate at system boundaries only.

## Test command

Tell the agent how to run tests. For example:

```
Test command: npm test
```

Or `pytest`, `cargo test`, `go test ./...`, `make test`, etc. The skills run this to verify work is done.

---

Keep this file short. If `CLAUDE.md` becomes a novel, nobody reads it. Add links to longer docs (`docs/VISION.md`, ADRs, design briefs) rather than inlining them.
