# Harness Engineering

The term comes from Mitchell Hashimoto and the OpenAI Codex team. The short version: **every mistake the agent makes should become a deterministic fix in the system around it, so that mistake can't recur.**

## The compounding pattern

- **Every agent mistake → deterministic fix.**
  Add a lint rule. Add a test. Add a line in `CLAUDE.md`. Each guardrail is cheap on its own. Together they become the shape of the codebase that makes agents productive.

- **Every decision → written down.**
  Architecture Decision Record. Project instructions. Design brief. The agent reads all of these next time.

- **Every slice shipped → protected.**
  The behavioral test stays. The unit tests stay. The regression harness grows.

## Why it matters

Without harness engineering, you're hoping the next model will magically be better. You're treating each session as an isolated event. The model doesn't learn from yesterday; neither does the project.

With harness engineering, you're making sure **your specific mistakes can't recur**. Week 4 is easier than week 1. The team's thinking becomes durable infrastructure instead of tribal knowledge that evaporates at the end of a Slack thread.

## Concrete examples

- The agent keeps skipping TDD → write down the double-loop BDD process in `CLAUDE.md`. Baseline shifts from "correct every session" to "occasionally nudge."
- The agent keeps using a deprecated utility → add a lint rule that forbids it.
- The agent keeps over-engineering error handling → add a project rule: "Trust internal code. Validate at system boundaries only."
- The agent keeps modifying deployment config → add `/preflight` to the pre-push routine and make the scope guard explicit in `CLAUDE.md`.

Each of these is cheap. None of them require a new model, a new framework, or even a new tool. They require paying attention to what's going wrong and writing it down in a place the agent reads.

## What to instrument

If your harness records sessions (Claude Code does this locally), the data is already there. You can extract:

- **Friction patterns** — places where you repeatedly correct the agent.
- **Session classification** — what kinds of work are actually happening.
- **Time allocation** — where cycles are spent vs. where value lives.

You don't need a fancy pipeline. Grep for your own corrections ("no, actually...", "stop doing X", "that's not what I asked"). Each one is a candidate for a deterministic fix.

## The compounding is the point

Vibe-coded work doesn't compound. The rework rate stays high. Every session feels like starting over.

Harness-engineered work compounds. Week 4 really is easier than week 1. The discipline pays itself back and then some.

This is why the skills in this repo exist. They encode the parts of the harness that are portable — the procedures, the scope guards, the double-loop. The parts that are specific to your project (the domain, the test command, the deployment constraints) live in your `CLAUDE.md` and grow over time.

## Further reading

- Mitchell Hashimoto — talks and posts on harness engineering.
- DORA 2025 — empirical evidence that practice quality determines AI outcomes.
- Kent Beck — *Tidy First?* and related essays on the economics of investment in structure.
