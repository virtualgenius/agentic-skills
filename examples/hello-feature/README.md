# Example: hello-feature

A short end-to-end walkthrough of the loop on a trivial feature. Read this alongside [`docs/the-loop.md`](../../docs/the-loop.md) to see how the skills connect in practice.

**Scenario**: we're adding a "greet user" feature to a toy web app. A user visits `/hello?name=Paul` and sees `Hello, Paul!`. The name is sanitized to prevent HTML injection.

Trivial on purpose. The point is the shape of the loop, not the complexity of the feature.

---

## 0. Starting state

Project layout:

```
hello-feature/
├── CLAUDE.md          # starter template, slightly edited
├── docs/
│   └── PLAN.md        # where work items live
├── src/
│   └── app.ts         # existing minimal server
└── test/
    └── app.test.ts    # existing test harness
```

`CLAUDE.md` says:

> Test command: `npm test`
> Work items live in `docs/PLAN.md` as a checklist.

## 1. Research & Plan (you, with AI as thinking partner)

You sketch a quick note in `docs/plan-notes.md`:

> We want a greeting endpoint. `GET /hello?name=X` → `Hello, X!`. Sanitize HTML in `X`. Reject empty / missing names with a friendly 400.

No AI automation here yet. This is sense-making. The point is to arrive at something specific enough that the team could argue about it.

## 2. Decompose → [`docs/PLAN.md`](docs/PLAN.md)

You invoke `/decompose` pointing at the plan note. The skill writes items to `docs/PLAN.md` using the markdown adapter.

```markdown
## Plan: greeting endpoint

- [ ] GREET-1: Visitor sees greeting with their name
  - **Description**: `GET /hello?name=Paul` returns 200 with body `Hello, Paul!` (plain text).
  - **Files**: `src/app.ts`, `test/app.test.ts`
  - **Acceptance**: Given a request to `/hello?name=Paul`, when the server responds, then the status is 200 and the body is exactly `Hello, Paul!`.
  - **Depends on**: none

- [ ] GREET-2: Name with HTML is rendered safely
  - **Description**: HTML in the `name` query parameter must be escaped in the response body.
  - **Files**: `src/app.ts`, `test/app.test.ts`
  - **Acceptance**: Given a request to `/hello?name=<script>x</script>`, when the server responds, then the body is exactly `Hello, &lt;script&gt;x&lt;/script&gt;!`.
  - **Depends on**: GREET-1

- [ ] GREET-3: Missing name returns a friendly 400
  - **Description**: Requests without a `name` parameter (or with an empty string) return 400 with body `Please provide a name.`
  - **Files**: `src/app.ts`, `test/app.test.ts`
  - **Acceptance**: Given a request to `/hello` with no name, when the server responds, then the status is 400 and the body is `Please provide a name.`
  - **Depends on**: none
```

Notice: three vertical slices. Each one is independently observable. GREET-1 and GREET-3 can run in parallel (different files in principle, but here they touch the same handler, so this example keeps them serial). GREET-2 depends on GREET-1.

## 3. Refine → same [`docs/PLAN.md`](docs/PLAN.md)

You invoke `/refine`. The skill runs 3-4 passes and suggests tightening:

- GREET-1's acceptance uses "exactly" — good. No change.
- GREET-2 flagged: the description says "escaped," the acceptance uses the HTML entity encoding. Consistent enough. No change.
- GREET-3 flagged: should a whitespace-only name count as empty? Not currently specified. Skill **adds a note**, does not silently expand scope:
  > Note: whitespace-only names ("   ") are not addressed by the current item. Flagging for the user to decide whether to add a new item or expand acceptance.

You decide whitespace-only names are out of scope for v1. Leave the note in the description; file a follow-up later if it matters.

## 4. Implement → [`implementation-log.md`](implementation-log.md)

You invoke `/implement GREET-1 GREET-2 GREET-3`.

The skill:

1. Reads each item's description and acceptance.
2. Marks each `- [ ]` → `- [~]` in `docs/PLAN.md`.
3. Plans work streams. GREET-1 and GREET-3 touch the same file but can be worked in sequence cheaply; GREET-2 depends on GREET-1. In a real project with more items, this is where parallel agents fan out.
4. For each item, runs the double-loop:

```
[OUTER RED] Visitor sees greeting with their name
  [RED] GET /hello?name=Paul → expects 200 + "Hello, Paul!"
  [GREEN] add handler returning template literal
  [REFACTOR] extract `greet(name)` pure function
[OUTER GREEN] Visitor sees greeting ✓

[OUTER RED] Name with HTML is rendered safely
  [RED] GET /hello?name=<script>x</script> → expects entity-encoded body
  [GREEN] add `escapeHtml(name)` call before formatting
  [REFACTOR] move `escapeHtml` into `src/html.ts`
[OUTER GREEN] Name with HTML rendered safely ✓

[OUTER RED] Missing name returns a friendly 400
  [RED] GET /hello → expects 400 + "Please provide a name."
  [GREEN] guard clause at top of handler
  [REFACTOR] (none needed)
[OUTER GREEN] Missing name returns 400 ✓
```

5. Marks each `- [~]` → `- [x]` in `docs/PLAN.md`.
6. Runs `npm test`. All tests pass. Reports total count.
7. Commits:

```
feat(greet): add /hello endpoint with sanitized name (GREET-1, GREET-2)
feat(greet): reject missing name with 400 (GREET-3)
```

8. Prints `git log --oneline` of new commits. **Does not push.**

## 5. Preflight → PASS

You invoke `/preflight`.

```
Commits to push: 2
  - feat(greet): add /hello endpoint with sanitized name (GREET-1, GREET-2)
  - feat(greet): reject missing name with 400 (GREET-3)

Config file audit: no changes to config files.
Scope trace:
  - commit 1 references GREET-1, GREET-2 — files match (src/app.ts, src/html.ts, test/app.test.ts) ✓
  - commit 2 references GREET-3 — files match (src/app.ts, test/app.test.ts) ✓
Untracked commits: none.
Possible scope creep: none.
Test result: 14 passed.

Verdict: GO
```

You push.

## 6. Tighten (retro)

After shipping, you notice the agent almost added a logger during GREET-1 that wasn't in scope. Caught by the scope guard in `CLAUDE.md`. Add a line to `CLAUDE.md`:

> Do not add logging unless the work item explicitly asks for it.

Next time, the agent won't make that mistake. The harness just got a little stronger. This is what compounding looks like.

---

## The loop in summary

1. **Research / Plan** — you, with AI as thinking partner. Produces specific intent.
2. **Decompose** — vertical slices with observable acceptance criteria.
3. **Refine** — 3-4 passes. Flag gaps as notes; don't silently expand scope.
4. **Implement** — double-loop BDD. Parallel where independent.
5. **Preflight** — audit before push.
6. **Tighten** — every mistake becomes a rule in the harness.

Clear context between each stage.
