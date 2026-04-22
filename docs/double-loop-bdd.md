# Double-Loop BDD with AI

Short primer. If you want the long version, see *BDD with Cucumber* (Lawrence & Rayner, 2019).

## Hierarchy: Scenario > Rule > Example

- **Scenario**: an acceptance criterion describing observable behavior. "Dealer submits duplicate invoice and sees rejection."
- **Rule**: a generalized invariant within a scenario. "A dealer cannot submit the same invoice number twice for the same promotion."
- **Example**: a concrete instance illustrating a rule, often as a table row. "Invoice #INV-100 for Promotion P1 already exists → submission is rejected with message X."

Examples explain rules by showing specific inputs and outcomes. Rules generalize across examples. Scenarios are the unit of progress.

## Outer loop (behavioral)

1. **OUTER RED**: Write one failing test for a scenario. It should exercise enough of the stack to verify behavior. Doesn't have to be end-to-end. A component test that renders the UI and drives the key interaction is fine.
2. **Verify the failure**: Confirm it fails for the right reason (feature missing, not a broken test). This is non-negotiable. A test that fails for the wrong reason is worse than no test.
3. **Inner loops**: Run as many inner TDD cycles as needed to build the pieces (see below).
4. **OUTER GREEN**: Return to the scenario test. Confirm it passes.
5. **OUTER REFACTOR**: Cross-module cleanup if needed. The passing outer test protects you.
6. Next scenario.

## Inner loop (unit TDD)

Standard red-green-refactor on pure functions and focused units. One or more inner cycles run inside a single outer-loop iteration.

1. **RED**: Write a single failing unit test.
2. **GREEN**: Make it pass by any means (including hardcoding values, tuning parameters).
3. **REFACTOR**: Structure / naming / duplication only. Tests stay green.
4. Repeat until the outer test should pass, then return to the outer loop.

### Phase rules

- **RED** — test fails for the right reason.
- **GREEN** — make it pass. Any means. Parameter tuning happens here, not in refactor.
- **REFACTOR** — only change structure. If tests fail during refactor, you're changing behavior.

## Who does what (with AI)

- You work with the AI to define the **scenarios**. This is sense-making, and it's where human judgment matters most.
- The AI drives the **red-green-refactor** loop. The `implement` skill in this repo is the process that runs here.
- You review the diff.

The tests are your executable intent. They survive code regeneration. When the next model comes along and rewrites the implementation, the scenarios still pin down what "done" looks like.

## When to skip the outer loop

- Pure refactors (behavior unchanged, existing tests protect you).
- Pure-CSS changes, config tweaks, documentation.
- Extracting / moving code where existing tests already cover the behavior.

If in doubt, don't skip. The outer loop is cheap insurance against "I thought this was working."

## Progress visibility

Print the scenario checklist at the start of a feature. Update it at each OUTER GREEN. Announce phase transitions.

```
## Scenarios: rebate calculation
- [ ] Dealer submits single-line invoice, sees correct rebate amount
- [ ] Dealer submits multi-line invoice, sees per-line pro-rated amounts
- [ ] Dealer submits invoice with zero-eligible SKUs, sees rejection

[OUTER RED] Dealer submits single-line invoice, sees correct rebate amount
  [RED] rebate amount = unit price * rule percentage
  [GREEN] implementing calculation
  [REFACTOR] extracting rounding helper
[OUTER GREEN] Dealer submits single-line invoice ✓

## Scenarios: rebate calculation
- [x] Dealer submits single-line invoice, sees correct rebate amount
- [ ] Dealer submits multi-line invoice, sees per-line pro-rated amounts ← current
- [ ] Dealer submits invoice with zero-eligible SKUs, sees rejection
```

This is for the human. It also keeps the agent anchored when the context gets long.
