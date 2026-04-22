# Implementation log (illustrative)

What `/implement GREET-1 GREET-2 GREET-3` prints to the terminal. Captured here so readers can see the shape without running the skill.

---

## Scenarios: greeting endpoint

- [ ] GREET-1: Visitor sees greeting with their name ← current
- [ ] GREET-2: Name with HTML is rendered safely
- [ ] GREET-3: Missing name returns a friendly 400

[OUTER RED] GREET-1: Visitor sees greeting with their name
  Writing behavioral test: `GET /hello?name=Paul` → expects status 200, body `Hello, Paul!`
  Running `npm test` → 1 failing (route not implemented). Failure confirmed for the right reason.

  [RED] greet(name) returns `Hello, Paul!` for `Paul`
  [GREEN] implementing greet() as template literal
  [REFACTOR] extracting greet() into its own module

  Re-running behavioral test... passes.

[OUTER GREEN] GREET-1 ✓

## Scenarios: greeting endpoint

- [x] GREET-1: Visitor sees greeting with their name
- [ ] GREET-2: Name with HTML is rendered safely ← current
- [ ] GREET-3: Missing name returns a friendly 400

[OUTER RED] GREET-2: Name with HTML is rendered safely
  Writing behavioral test: `GET /hello?name=<script>x</script>` → expects `Hello, &lt;script&gt;x&lt;/script&gt;!`
  Running `npm test` → 1 failing (response contains raw script tag). Failure confirmed for the right reason.

  [RED] escapeHtml('<script>') returns '&lt;script&gt;'
  [GREEN] implementing minimal HTML entity replacement (&, <, >, ", ')
  [REFACTOR] moving escapeHtml into src/html.ts; greet() imports it

  Re-running behavioral test... passes.

[OUTER GREEN] GREET-2 ✓

## Scenarios: greeting endpoint

- [x] GREET-1: Visitor sees greeting with their name
- [x] GREET-2: Name with HTML is rendered safely
- [ ] GREET-3: Missing name returns a friendly 400 ← current

[OUTER RED] GREET-3: Missing name returns a friendly 400
  Writing behavioral test: `GET /hello` (no name) → expects status 400, body `Please provide a name.`
  Running `npm test` → 1 failing (handler returns 200 with empty greeting). Failure confirmed for the right reason.

  [RED] handler returns 400 with message when name is missing
  [GREEN] adding guard clause at top of handler
  [REFACTOR] (none needed; logic is three lines)

  Re-running behavioral test... passes.

[OUTER GREEN] GREET-3 ✓

## Scenarios: greeting endpoint

- [x] GREET-1: Visitor sees greeting with their name
- [x] GREET-2: Name with HTML is rendered safely
- [x] GREET-3: Missing name returns a friendly 400

Running full suite: `npm test` → 14 passed.

Staging changes, committing:

```
feat(greet): add /hello endpoint with sanitized name (GREET-1, GREET-2)
feat(greet): reject missing name with 400 (GREET-3)
```

Not pushing. Run `/preflight` before pushing.
