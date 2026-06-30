# AGENTS.md

## Project guidelines

- Keep diffs small.
- Preserve existing conventions.
- Do not invent requirements.
- Do not introduce broad rewrites without explicit request.
- Do not log secrets, tokens, credentials, personal data, or private logs.
- Treat changes to CI permissions, authentication, authorization, deployment, dependency management, and billing as high risk.
- Never auto-merge.

## Review guidelines

When reviewing a pull request:

- Review only the changes introduced by the PR.
- Focus on P0/P1 issues that should block merge.
- Check whether the implementation satisfies the PR description.
- Check for missing tests or missing verification.
- Check for accidental unrelated changes.
- Check for security, privacy, permission, dependency, CI, and deployment regressions.
- Do not block on style-only issues unless they create maintainability risk.
- Do not request broad rewrites.
- Prefer concrete, actionable comments that Claude Auto-fix can resolve.

## Severity classification

Classify issues as:

- P0: Critical. Must block merge.
- P1: Serious. Should block merge.
- P2: Non-blocking improvement.

Only leave GitHub review comments for P0/P1 issues.

## Recommended review output style

Use concise, actionable comments.

Good:

```text
P1: This branch changes the deployment workflow permissions from read-only to write-all. That is outside the PR scope and increases risk. Revert the permission change unless the PR explicitly requires it.
```

Bad:

```text
Consider improving the structure.
```

## Responsibility boundary

Codex is the reviewer. Claude is the implementation and auto-fix agent.

Do not push fixes from Codex in this workflow. Avoid `@codex fix` unless a human intentionally decides to override this template's role split.
