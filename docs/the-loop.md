# The Loop Around the Agent

> Keeping quality in as you build with AI. Understand → Express → Check → Tighten.

This is the methodology the skills in this repo encode. The skills are the automation; this doc is the reasoning.

## AI is an amplifier

The 2025 DORA report surveyed around 5,000 developers. The single biggest finding: AI doesn't create new capability patterns. It magnifies whatever is already there.

- Teams with strong practices (tests, clear domain boundaries, fast feedback) get faster sustainably with AI.
- Teams without those practices ship more problems, faster. Not neutral. Not "it depends." Amplifier.

This reframes every "should we use AI?" conversation into "what's it amplifying?"

## The perception gap

A separate study by METR, cited in DORA 2025, ran a controlled experiment. Experienced developers, on codebases they knew well, with and without AI assistance.

- AI-assisted group was **19% slower** on average.
- They believed they were **20% more efficient**.

If you can't tell you're slower, you definitely can't tell your discipline is broken. That's the whole reason the practices in this repo exist. If your gut says "this is flying" and your delivery metrics say "we're slower and defects are up," believe the metrics.

## Every major shift looks like removing discipline

Chad Fowler named this pattern ("Relocating Rigor"):

| Shift | What people said was lost | Where the rigor actually moved |
|---|---|---|
| Dynamic languages | the compiler | tests, executable specifications |
| XP | planning documents | TDD, pair programming, CI |
| Continuous deployment | release windows | observability, reversibility, automation |
| **AI coding** | **authorship** | **sense-making + the harness** |

Each shift *looked* like removing discipline. The rigor just moved. AI coding is the next turn of the crank. The rigor has moved to sense-making and to the harness around the agent.

## Four activities. Every turn.

```
 Understand  →  Express
     ↑             ↓
  Tighten  ←  Check
```

- **Understand** — sense-making. Domain. What we're building, for whom, with what constraints. This is the phase you can't delegate.
- **Express** — tests, scenarios, specs. `CLAUDE.md`. Without expression, AI guesses at your intent. With it, AI is constrained by it.
- **Check** — does what we built match the spec? And does the spec still match reality?
- **Tighten** — refactor, improve the harness, codify what we learned so the next time is easier.

Every feature. Every turn.

## Where work actually lives (the quadrant map)

Two axes: how fast you're shipping, and how much structure you invest in.

|  | Low shipping | High shipping |
|---|---|---|
| **High structure** | Analysis paralysis | **Sustainable flow** |
| **Low structure** | (stalled) | Vibe coding |

- **Vibe coding** isn't wrong for prototypes, spikes, tooling, throwaway experiments. The problem is when it becomes the default for production software.
- **Over-engineering / analysis paralysis** are the opposite traps. Beautiful architecture, nothing ships.
- **Sustainable flow** is where production work needs to live. Shipping fast *and* with discipline. AI generating, TDD and domain boundaries keeping it honest.

The rest of the loop is about how to move from vibe-coded work into sustainable flow without losing velocity.

## Sense-making is the work that remains

> "A little bit of mist in your mind is going to be an impenetrable fog for the AI."

AI has no sense-making capability. It has pattern-matching from training data. The phase AI accelerates most (code generation) is the least important. The phase that matters most (understanding the domain) still requires **you**.

Before you hand a task to an agent, you should have:

- A spec the team could argue about. If it's too vague to disagree with, it's too vague for an agent.
- At least one concrete example, not just abstract principles.
- At least one explicit "no." What you're NOT building matters as much as what you are.
- One named user with one named journey.

## The workflow

```
Research → Plan → Decompose → Refine → Implement → Preflight → push
```

- **Research** — evaluate options, weigh pros and cons. When the AI does generation for you, you can afford to invest in this phase in ways you never could before. Kent Beck calls these early investments "futures."
- **Plan** — mockups, architecture, slices of user value (not technical layers).
- **Decompose** — break the plan into small work items the AI can execute independently. → `/decompose`.
- **Refine** — 3 or 4 passes, each tightening the scenarios and catching scope creep. → `/refine`.
- **Implement** — double-loop BDD, parallel agents against independent slices. → `/implement`.
- **Preflight** — last human-judgment checkpoint before anything leaves your machine. Audits scope, runs tests. → `/preflight`.

**Clear context between every stage.** Stay near the bottom of the context window. Each stage benefits from a fresh context, not a bloated one full of stale associations from the last stage.

## Double-loop BDD

- **Outer loop (behavioral)**: write a failing scenario that describes what the user actually needs. Drive inner loops until it passes. This is specifying intent.
- **Inner loop (TDD)**: red, green, refactor. Each cycle shapes the implementation one small step at a time.

The key insight for AI-assisted development: **you work with the AI to define the scenarios. The AI drives the red-green-refactor loop.** The test is your executable intent. It survives code regeneration.

Without tests, AI tends to jump straight to generating code without understanding. With double-loop BDD, you force the shaping step first.

For visual work, the same loop applies. The AI takes a screenshot with Playwright (or similar), compares it to the expected outcome, and iterates. Generate, check, adjust.

## The harness

The harness is everything around the agent that keeps it honest: tests, linters, type checks, git hooks, skill files, project instructions, ADRs. Every mistake the agent makes should become a **deterministic fix** in the harness so that mistake can't recur.

- **Every agent mistake → deterministic fix.** Add a lint rule, a test, a line in `CLAUDE.md`.
- **Every decision → written down.** ADRs, design briefs, project instructions. The agent reads them next time.
- **Every slice shipped → protected.** The scenario test stays. The unit tests stay.

Week 4 is genuinely easier than week 1 *if* you pay this forward. That doesn't happen with vibe-coded work. That's the difference between sustainable velocity and a sugar high.

## Verification

**Generation is probabilistic. Verification must be deterministic.**

- Pre-push checks (`/preflight`) — automated audit of scope, tests, config before code leaves your machine.
- Git hooks — linters, type checks, formatting on every commit.
- Test suite as gate — nothing merges without green. AI-generated code gets the same scrutiny as human code.
- Manual diff review — AI can explain its changes, but you decide if they belong. That's judgment, not automation.

## Durable orientation

Session context is lossy. Next session starts fresh. What persists is what you write down.

| What | Purpose |
|---|---|
| `CLAUDE.md` / `AGENTS.md` | Project-wide rules. The agent reads it every session. |
| Skills | Reusable procedures the agent invokes on demand. |
| Plans & design briefs | Per-slice context. Why this, how, what not. |
| ADRs | Decisions and their reasoning. Search, don't re-litigate. |

Keep them short. If `CLAUDE.md` becomes a novel, nobody reads it — not the agent, not the team.

## Credits

- Chad Fowler — "Relocating Rigor."
- Mitchell Hashimoto — harness engineering.
- Kent Beck — "tidy first," "futures."
- Liz Keogh, Dan North, Dave Farley, Seb Rose, and many others — BDD lineage.
- DORA 2025 — empirical grounding.
- METR 2025 — perception-vs-reality study.
