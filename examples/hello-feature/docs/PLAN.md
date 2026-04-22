# Plan: greeting endpoint

This is the shape of `docs/PLAN.md` after `/decompose` has run against a brief plan note. The README walks through how this file gets used end to end.

- [x] GREET-1: Visitor sees greeting with their name
  - **Description**: `GET /hello?name=Paul` returns 200 with body `Hello, Paul!` (plain text).
  - **Files**: `src/app.ts`, `test/app.test.ts`
  - **Acceptance**: Given a request to `/hello?name=Paul`, when the server responds, then the status is 200 and the body is exactly `Hello, Paul!`.
  - **Depends on**: none

- [x] GREET-2: Name with HTML is rendered safely
  - **Description**: HTML in the `name` query parameter must be escaped in the response body.
  - **Files**: `src/app.ts`, `src/html.ts`, `test/app.test.ts`
  - **Acceptance**: Given a request to `/hello?name=<script>x</script>`, when the server responds, then the body is exactly `Hello, &lt;script&gt;x&lt;/script&gt;!`.
  - **Depends on**: GREET-1

- [x] GREET-3: Missing name returns a friendly 400
  - **Description**: Requests without a `name` parameter (or with an empty string) return 400 with body `Please provide a name.`
  - **Files**: `src/app.ts`, `test/app.test.ts`
  - **Acceptance**: Given a request to `/hello` with no name, when the server responds, then the status is 400 and the body is `Please provide a name.`
  - **Note**: whitespace-only names ("   ") are not addressed by the current item. Flagged during refine; deferred to a future item.
  - **Depends on**: none
