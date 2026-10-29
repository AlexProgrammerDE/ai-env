# Global working agreements

Keep project-specific commands, architecture, and framework rules in the relevant repository's `AGENTS.md`.

## Git

- Stay on the current branch. Do not create a branch unless I explicitly request one, even when a skill recommends it.
- Preserve unrelated worktree changes. Stage only files that belong to the requested change.
- Use Conventional Commits: `<type>(<scope>): <description>`. Omit the scope when there is no meaningful scope.
- Prefer `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`, `build`, and `perf`.
- Write concise, specific, imperative descriptions.
- For non-trivial changes, add a body explaining motivation, behavior changes, and non-obvious decisions. Separate it with a blank line.
- Use `BREAKING CHANGE:` footers when applicable.
- Never bypass Git hooks or Lefthook, including with `-n`. Wait for hooks to finish and fix failures.
- When mentioning a commit pushed to GitHub, link its hash: `[<short-hash>](https://github.com/<owner>/<repo>/commit/<full-hash>)`.
- Do not fabricate GitHub links for local commits.

## Implementation

- Follow the repository's architecture, naming, tooling, and supported language versions.
- Use idiomatic APIs supported by the installed dependencies. Do not upgrade dependencies merely to use a newer pattern.
- Fix the underlying problem. Remove temporary scaffolding and code made obsolete by the change.
- Keep necessary compatibility behavior. Do not leave dummy implementations or unused components.
- Keep changes focused. Avoid unrelated refactors and formatting changes.

## Verification

- Run checks appropriate to the change and the repository's requirements.
- For new behavior or bug fixes, add targeted unit or integration tests when practical.
- Test observable behavior and meaningful edge cases. Avoid tests that only mirror the implementation or assert source text.
- Assert strings when their content is part of the behavior or contract.
- Report what you verified and any checks you could not complete. Distinguish local changes, pushed code, and live behavior.

## Writing

- Write concise, natural prose with active voice and concrete wording.
- Put the main point first. Use enough detail to explain decisions without repeating the diff.
- Avoid stock phrases, inflated claims, and unnecessary jargon. Do not use em dashes.
- Apply this style to documentation, comments, UI text, commit messages, and explanations.
- For technical documentation, use `simple-english` in pragmatic mode. Use strict mode only for explicit ASD-STE100 requests.
- For substantial documentation structure or new guides, also use `documentation-writer`.
