# Markdown Task List Adapter (default)

The skills in this repo assume a work tracker. The default is the simplest possible thing: a markdown checklist in `docs/PLAN.md`. No tools to install, nothing to set up.

If your team uses GitHub Issues, Jira, Azure DevOps, Linear, or a local tracker like beads, swap the create / read / update / close operations below for your tracker's equivalents. The rest of each skill (double-loop BDD, parallelization, preflight checks) stays the same.

## Conventions

Work items live as a flat checklist in `docs/PLAN.md`. Each item follows this shape:

```markdown
## Plan: <feature name>

- [ ] ITEM-1: Dealer submits single-line invoice and sees correct rebate amount
  - **Description**: Backend calculates rebate = unit_price * promotion_percentage. UI displays the result on the confirmation page.
  - **Files**: `src/rebate/calculator.ts`, `src/ui/ConfirmationPage.tsx`
  - **Acceptance**: Given a valid invoice with one line item, when the dealer submits, then the confirmation page shows the rebate amount rounded to the nearest cent.
  - **Depends on**: none

- [ ] ITEM-2: Dealer submits multi-line invoice and sees per-line pro-rated amounts
  - ...
```

Checkbox states:

- `- [ ]` — open
- `- [~]` — in progress
- `- [x]` — done
- `- [!]` — blocked (optional; follow with a reason)

IDs are freeform. Use whatever your team finds readable: `ITEM-1`, `REBATE-SINGLE-LINE`, plain numbers. The skills just need a stable handle to refer to each item.

## Operations (what each skill does)

| Skill needs to... | Markdown adapter does... |
|---|---|
| Look up item details | Grep `docs/PLAN.md` for the item, read its description block. |
| Mark item in progress | Edit the checkbox from `- [ ]` to `- [~]`. |
| Mark item done | Edit the checkbox from `- [~]` to `- [x]`. |
| List open items | Grep `docs/PLAN.md` for `- [ ]` and `- [~]` lines. |
| List blocked items | Grep for `- [!]` lines. |
| Record a dependency | Add a `**Depends on**:` line under the dependent item. |

## Swapping in a different tracker

Each skill has a small section near the top that does the lookup. To use a different tracker, change just those lookups. For example:

- **GitHub Issues**: `gh issue view <number>` / `gh issue edit <number>` / `gh issue close <number>`.
- **Jira**: `jira issue view <key>` (via the `jira-cli` tool) or the REST API.
- **Azure DevOps**: `az boards work-item show --id <id>`.
- **Linear**: `linear issue <id>` (via the `linear` CLI) or the GraphQL API.
- **beads**: `bd show <id>` / `bd update <id> --status=in_progress` / `bd close <id>`.

Leave the double-loop BDD workflow, parallelization rules, and scope guards exactly as they are. The discipline is the point; the tracker is plumbing.
