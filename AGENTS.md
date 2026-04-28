# AGENTS.md

Single source of truth for both Claude Code and Codex (and any other agent that respects the AGENTS.md convention). `CLAUDE.md` imports this file; do not duplicate content there.

## What this repo is

Team project for the **Alberthon G20** student hackathon. Three collaborators: Matthieu, Anthony, Vincent.

## Stack

- **Backend: Python.** Confirmed. All backend code lives under `backend/`.
- **Frontend: undecided.** Do not assume a framework. When the choice is made, record it here.

## Package management (backend)

- **`uv` is the primary tool.** Use `uv` for environment creation, dependency resolution, and running scripts (`uv sync`, `uv add <pkg>`, `uv run <cmd>`).
- **`pip` is allowed as a fallback** when `uv` cannot do something (e.g. installing from a local wheel an internal index does not mirror). Prefer `uv pip` over bare `pip` so the active environment is the `uv`-managed one.
- The lockfile (`uv.lock`) is committed. Do not edit it by hand.
- Do not introduce a second package manager (no Poetry, no Pipenv, no Conda) without team agreement recorded here.

## Layout

```
.
├── AGENTS.md                # this file — read first
├── CLAUDE.md                # one-line import of AGENTS.md
├── backend/                 # Python service (pyproject.toml lives here)
└── .claude/
    └── skills/              # portable SKILL.md files used by both agents
        ├── test-completion-checklist/
        └── finish-coding-checklist/
```

The frontend directory will be added once the framework is chosen.

## Branch flow

- `main` is the integration branch and is **protected**. No direct commits or pushes.
- Each collaborator works on their own dev branch: `dev_Matthieu`, `dev_Anthony`, `dev_Vincent`.
- Changes reach `main` via pull request, reviewed by at least one other collaborator.
- Rebase your dev branch onto `main` before opening a PR; resolve conflicts locally, not in the GitHub UI.

## End-of-task protocols

When an implementation step is finished, run these in order. They are skills, so invoke them by name in either Claude Code or Codex:

1. **`test-completion-checklist`** — verifies new behavior has tests, that the relevant tests actually ran, and that nothing was silently skipped. Path: `.claude/skills/test-completion-checklist/SKILL.md`.
2. **`finish-coding-checklist`** — gates the commit on tests + quality, stages deliberately, drafts a why-focused Conventional Commit message, and stops before any push. Path: `.claude/skills/finish-coding-checklist/SKILL.md`.

Pushing and opening PRs are real-world actions and require explicit user authorization; do not chain them onto a commit.

## Code conventions (backend, Python)

- Type hints on every function argument and return.
- `pathlib.Path` for paths (no `os.path.join` in new code).
- `dataclass` (or `TypedDict` for JSON-shaped payloads) for structured data passed between functions.
- Structured logging with key-value fields. No `print` in library code.
- No bare `except:` or `except Exception:`. Raise typed exceptions with context.
- File names describe what they contain. No `utils.py`, `helpers.py`, `misc.py`, `common.py`.

Frontend conventions will be added with the framework choice.

## Things not to do

- Do not run `/init` and ship the result.
- Do not let this file grow past ~300 lines. Every line earns its place.
- Do not commit secrets. `.env` is gitignored; commit `.env.example` with dummy values instead.
- Do not push to `main` directly, even with a "small" change.
- Do not let the agent that wrote the code be the only reviewer. Cross-review with the other agent (Claude reviews Codex output and vice versa) for anything non-trivial.
