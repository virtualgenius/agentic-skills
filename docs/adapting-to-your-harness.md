# Adapting to Your Harness

The skills in this repo are first-class for **Claude Code**. Everything else is paste-in.

## Claude Code

The files in `skills/` are ready to drop in.

- **User-wide** (available in every project on your machine): copy `skills/*.md` into `~/.claude/skills/` (or wherever your Claude Code install looks for skills; earlier versions used `~/.claude/commands/`).
- **Project-scoped** (only in this repo): copy into `.claude/skills/` in the project root.

Then invoke as slash commands: `/implement`, `/decompose`, `/refine`, `/preflight`.

## GitHub Copilot Chat

Copilot Chat doesn't have a skill / slash-command system in the same shape. The pattern that works:

1. Paste the body of a skill into a "project instructions" or custom-instructions file (e.g. `.github/copilot-instructions.md`).
2. Invoke it by name in chat: "Run the decompose workflow against `docs/plan.md`."
3. Double-loop BDD is still the core discipline; the skill just becomes the procedure you paste into the chat window when you want Copilot to follow it.

## Cursor

- **Composer rules**: paste skill bodies into `.cursor/rules` or the Cursor Rules UI.
- **Slash commands** (if your install supports them): Cursor's slash-command format is close enough to Claude Code's that the skill files in `skills/` work with minimal edits. The main change: Cursor doesn't have a built-in agent-team primitive, so the `implement` skill falls back to serial execution (one item at a time) rather than parallel agents.

## Codex / OpenAI assistants

- Paste the skill body into the system prompt.
- Treat each skill as a named procedure: "When I say 'decompose,' follow these steps."
- Codex generally lacks the tool surface to spawn parallel agents; run `implement` one item at a time.

## Anything else

Any harness with a system prompt or persistent-instruction file can run the methodology. The delivery differs; the loop doesn't.

Two things stay constant regardless of harness:

1. **Double-loop BDD**. Outer loop = scenario test. Inner loop = red-green-refactor. Don't let the harness talk you out of either.
2. **The scope guards**. No deployment-config changes without explicit authorization. No untracked work landing in commits. `/preflight` runs before push no matter what.

## Parallel agents

`/implement` spawns a team of parallel agents against independent work streams. That capability is harness-specific:

- **Claude Code**: native (the harness has agent-team primitives).
- **Copilot / Cursor / Codex**: currently serial. Run one work item at a time.

The double-loop discipline inside each work item is identical regardless. Parallelism is a speed multiplier, not a correctness requirement.

## Where paste-in skill files live

Some harnesses expect one persistent instructions file; others scan a directory. Pick whichever maps to your tool:

| Harness | Where paste-in skills go |
|---|---|
| Claude Code | `~/.claude/skills/` or `.claude/skills/` |
| Copilot | `.github/copilot-instructions.md` |
| Cursor | `.cursor/rules/*.mdc` or the Rules UI |
| Codex / custom | system prompt, or a single `AGENTS.md` at the repo root |

When in doubt, drop a copy at the repo root as `AGENTS.md` — most modern harnesses look for it, and teammates using different tools can all find it.
