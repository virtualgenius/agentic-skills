---
name: preflight
description: Pre-push safety check. Audit scope, tests, and config before pushing.
---

Pre-push safety check. Run this before pushing to verify that no unauthorized scope expansion or config changes slipped through.

Usage: `/preflight`

## Steps

1. **Identify what's being pushed**: Run `git log --oneline @{upstream}..HEAD` (or `origin/main..HEAD` if no upstream is set) to see all unpushed commits.

2. **Config file audit**: Check if any deployment or dependency config files were modified in unpushed commits:
   ```
   git diff --name-only @{upstream}..HEAD -- '*.yml' '*.yaml' 'appsettings*' '*.tf' '*.tfvars' Dockerfile 'docker-compose*' package.json pyproject.toml Cargo.toml go.mod .env.example
   ```
   Flag any changes to: pipeline YAML, `appsettings*` files, connection strings, env config, Terraform, Dockerfiles, dependency manifests. Ignore: test fixtures, mock data, documentation (`CLAUDE.md` / `AGENTS.md` / `README.md`).

3. **Scope trace**: For each unpushed commit:
   - Extract work-item references from commit messages (whatever convention the project uses, e.g. `(#123)`, `(ABC-4)`, `(abc)`).
   - If a commit references no work item, flag it as "untracked work".
   - If a commit modifies files not mentioned in its item's description, flag it as "possible scope creep".

4. **Test run**: Run the project's test command and confirm it exits clean. Report the total test count.

5. **Report**: Output a clear summary:
   - Total commits to push.
   - Any config file changes (with file names and commit hashes).
   - Any untracked commits (no work-item reference).
   - Any possible scope creep.
   - Test result (pass / fail, count).
   - A **GO** / **REVIEW NEEDED** verdict.

If everything is clean, say **GO**. If anything is flagged, say **REVIEW NEEDED** and list the concerns. Do not push; let the user decide.
