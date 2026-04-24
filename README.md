# agentic-skills

> Opinionated Claude Code skills for sustainable agentic development. The loop around the agent.

A small, opinionated library of skills that encode the **research / plan / decompose / refine / implement / preflight** loop. This is what keeps quality in when AI writes most of the code.

This is not a framework. It's a tight set of markdown prompts plus conventions that make the loop reproducible on someone else's machine. No code to install. Just markdown files and a starter `CLAUDE.md` template.

## Why

Every major shift in software engineering looks like removing discipline. Dynamic languages lost the compiler; XP lost planning documents; continuous deployment lost release windows. In each case, the rigor didn't vanish — it **moved**. To tests, to TDD, to observability.

AI coding is the next shift. The rigor is moving again: from authorship to sense-making, specification, and verification. This repo is a small, reusable piece of that infrastructure.

See [`docs/the-loop.md`](docs/the-loop.md) for the full methodology.

## What's in v0.1

Four skills, one adapter, and a starter `CLAUDE.md` template.

| Skill | What it does |
|---|---|
| [`decompose`](skills/decompose.md) | Break a plan into small, independent work items with observable acceptance criteria. |
| [`refine`](skills/refine.md) | 3-4 pass review of work items to catch scope creep, ambiguity, and hidden dependencies. |
| [`implement`](skills/implement.md) | Spawn a parallel agent team to implement work items using double-loop BDD/TDD. |
| [`preflight`](skills/preflight.md) | Pre-push safety check: tests, scope, unauthorized config changes. |

Plus:

- [`adapters/markdown-tasks.md`](adapters/markdown-tasks.md) — default work tracker (a checklist in `docs/PLAN.md`). Swap for GitHub Issues, Jira, ADO, Linear, or beads by changing just the lookup commands in each skill.
- [`CLAUDE.md`](CLAUDE.md) — starter template for your project. Copy into the root of the project you're working on.

## Quick start

1. Copy `CLAUDE.md` into the root of your project and edit it (add your test command, note any project-specific rules).
2. Copy the files from `skills/` into your harness's skill directory.
   - **Claude Code**: `~/.claude/skills/` (user-wide) or `.claude/skills/` (project-scoped).
   - **Other harnesses**: paste the body of each skill into your system prompt or a persistent note. See [`docs/adapting-to-your-harness.md`](docs/adapting-to-your-harness.md).
3. Create `docs/PLAN.md` with a markdown checklist of work items (see [`adapters/markdown-tasks.md`](adapters/markdown-tasks.md) for the shape).
4. Run the loop: `/decompose` → `/refine` → `/implement <item>` → `/preflight`.

If your project already has a work tracker you like (GitHub Issues, Jira, ADO, beads, etc.), skip step 3 and swap the lookup commands in each skill.

## The loop

```
Research → Plan → Decompose → Refine → Implement → Preflight
                                                       ↓
                                                     push
```

Clear context between every stage. Each stage benefits from a fresh context, not a bloated one full of stale associations from the last stage. Full write-up: [`docs/the-loop.md`](docs/the-loop.md).

## Usage examples

- Break a plan into implementable items: `/decompose` (or `/decompose docs/some-plan.md` if your plan lives elsewhere).
- Polish those items before work starts: `/refine` (or `/refine ITEM-1` to review a single item).
- Run parallel agents against a batch: `/implement ITEM-1 ITEM-2 ITEM-3`.
- Audit before pushing: `/preflight`.

An end-to-end worked example lives in [`examples/hello-feature/`](examples/hello-feature/).

## Who this is for

Teams and individuals who:

- Want AI-assisted development to produce sustainable velocity, not a sugar high.
- Already believe in tests, domain boundaries, and small slices of user value — and want to keep those principles intact as AI takes over more of the code generation.
- Are willing to trade a little ceremony up front for dramatically less rework later.

If you're vibe-coding a prototype, you don't need this. If you're shipping production software and the team is growing an AI habit, you probably do.

## Non-goals

- Porting any one person's private skills.
- Becoming a framework. No code to install; just markdown and conventions.
- Supporting every harness equally. Claude Code is first-class; others get paste-in specs.
- Owning the methodology. The loop is a synthesis of Chad Fowler ("Relocating Rigor"), Mitchell Hashimoto (harness engineering), DORA 2025, Kent Beck's "tidy first" and "futures," and decades of XP / BDD / DDD practice. Credit liberally.

## Related reading

- *BDD with Cucumber* — Lawrence & Rayner, 2019.
- *EventStorming Handbook* — Rayner, ongoing.
- DORA 2025 report on AI-assisted development.
- METR 2025 study on measured vs. perceived developer productivity with AI.

## License

MIT. Use it, fork it, adapt it. Credit the repo if you ship a derivative set of skills.

## Contributing

This is v0.1. Real feedback from real use is more useful than speculation. If a skill's wording is fighting you, open an issue with the transcript snippet that went wrong — that's gold.
