---
name: finish-coding-checklist
description: Git protocol for the end of an implementation step. Use when the user says "done", "ship it", "commit this", or at the natural end of a planned step. Stages only the actual changes, gates on tests and quality, drafts a commit message that names the why, and stops before any push. Does not push or open PRs without explicit confirmation.
---

# Process

Run each check in order. Do not skip ahead. Any failure halts the checklist until resolved or the user explicitly waives it.

## 1. Survey the working tree

Run `git status` and `git diff` (staged and unstaged). Read both. Identify:
- Files changed for the current task.
- Files changed incidentally (editor swap files, test artifacts, debug prints, `.env` overrides, virtualenv directories).
- Untracked files that should be added vs. ones that should be ignored.

If anything sensitive appears (credentials, API keys, personal data, machine-local paths), stop and ask before going further.

## 2. Run test-completion-checklist

Invoke the testing protocol. The commit does not happen until the suite is green and the change has tests covering it. If tests are red, fix the cause; do not commit "WIP" to silence the gate unless the user explicitly asks.

## 3. Run the language quality gate

For Python work, run python-quality-gate. For other languages, run the project's lint / format / type-check commands (`ruff check`, `mypy`, `eslint`, `tsc --noEmit`, etc.). Address findings. Mechanical fixes (formatting, unused imports) can be applied without asking; design-level findings (function shape, missing types in unrelated code) get surfaced but not auto-patched.

## 4. Stage deliberately

Use `git add <path>` for each file that belongs to this commit. Do not use `git add -A` or `git add .` — those sweep in noise. Re-run `git diff --staged` and read it. The staged diff is what the commit will contain; if it includes anything you cannot justify, unstage it.

## 5. Draft the commit message

Format: Conventional Commits.

```
<type>(<scope>): <subject>

<body>
```

- **type**: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `style`. Pick one; if the change spans two, the commit spans two and should be split.
- **scope** (optional): the module or area (`api`, `ingest`, `pricing`).
- **subject**: imperative, lower-case, no trailing period, ≤ 72 chars. Names the change ("add retry on 5xx") not the activity ("worked on retries").
- **body**: explains the **why**. The diff already shows the what. Mention the constraint, bug, or decision that motivated the change. Wrap at ~72 cols. Skip the body only for genuinely trivial changes (typo, formatting).

Surface the drafted message to the user and wait for confirmation or edits before committing. "Looks fine" counts as confirmation; silence does not.

## 6. Commit

Run `git commit` with the confirmed message. Do not pass `--no-verify`. Do not amend a previously pushed commit. If a pre-commit hook fails, fix the underlying issue and create a new commit; do not bypass.

## 7. Stop

Do not `git push`. Do not open a pull request. Do not merge. Report:
- The commit hash and one-line subject.
- The branch you are on.
- Whether the branch is ahead of its upstream and by how many commits.
- A one-line suggestion for the next action ("ready to push to `origin/dev_matthieu`?", "ready to open a PR into `main`?").

Wait for the user to authorize the push or PR explicitly. Pushing is a real-world action; it is not part of "finishing the code."

# Branch flow (default for this project)

- `main` is protected. No direct commits.
- Each collaborator works on `dev_<name>` (e.g., `dev_Matthieu`, `dev_Anthony`, `dev_Vincent`).
- Integration into `main` happens through a pull request reviewed by at least one other collaborator.
- Rebase your dev branch onto `main` before opening a PR; resolve conflicts locally rather than in the GitHub UI.

If the user is on `main` when this checklist runs, surface that as a finding and ask whether to move the change to a dev branch before committing.

# Anti-patterns

Do not commit and push in one motion. Do not write commit messages that paraphrase the diff ("update file.py"). Do not `git add -A` to save time. Do not stack five logical changes into one commit so the message has to use "and" three times — split them. Do not use `--amend` after the commit has been pushed; create a follow-up commit instead. Do not skip hooks; they are the gate, not the obstacle.
