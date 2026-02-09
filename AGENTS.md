# AGENTS.md — vllm

## Sync policy (MANDATORY)

- Keep shared baseline rules in this file synchronized with the ai-software-engineering counterpart at `~/[Gg]it[Rr]epos/ai-software-engineering/AGENTS.md`.
- Do not hardcode machine-specific absolute paths for ai-software-engineering; discover it under `~/[Gg]it[Rr]epos/ai-software-engineering`.
- When updating shared behavior rules in either file, update the other file in the same work session.
- Proactively remind the user to keep ai-software-engineering and local agent configuration (for example `~/.codex` and `~/.agents/skills`) loosely in sync when either side changes.
- Project-specific additions can stay only here, but they should be explicitly marked as project-specific.

## Continuity Ledger (compaction-safe)

- At the beginning of a new session, offer a continuity ledger file named `CONTINUITY_CODEX-<sessionid>.md` in this workspace.
- If ledger is active, refresh it at the start of each turn and whenever goal/constraints/decisions/state change.
- Keep ledger factual and compact; mark uncertainty as `UNCONFIRMED`.
- In replies while ledger is active, start with a short Ledger Snapshot (Goal + Now/Next + Open Questions).

## Execution reliability baseline

- Prefer `python3` (or an explicit venv interpreter); do not assume `python` exists.
- Assume macOS/BSD userland by default; avoid GNU-only commands/options unless explicitly available.
- Treat non-zero exits from process-probe commands (`ps -p`, `pgrep`, `lsof` no match) as expected checks, not hard failures.
- Before claiming a background run started, verify expected artifacts exist.
- For long runs (>10 minutes), use resilient orchestration (`tmux`, `launchd`, or equivalent) and monitor progress freshness.

## Session-derived guardrails (shared baseline)

- Use the `apply_patch` tool directly for patch-style edits; do not invoke `apply_patch` through `shell_command`.
- When sending shell commands that include backticks or markdown-like text, use single-quoted shell strings (or escaped backticks) to prevent unintended command substitution.
- Treat command timeouts as ambiguous completion states: always verify side effects immediately (`git status`, `git rev-parse`, remote branch/PR status) before retrying.
- Treat `gh pr checks` exit code `8` as “checks still pending”, not an execution failure; inspect individual check states before acting.
- Classify CI failures before changing code: if logs show external 5xx clone/fetch errors (GitHub, Read the Docs, dependency repos), mark as infra and avoid speculative code churn.
- Check permissions for CI reruns early; if rerun rights are missing (for example admin-only workflow reruns), report clearly and switch to maintainer-driven rerun/new-push strategy.
- Scope filesystem searches to known roots first; avoid broad recursive scans that can time out and slow incident response.

## Git hygiene baseline

- If the tree is dirty and a pull is needed, use `git pull --rebase --autostash` unless the user requests otherwise.
- Default assumption: for agent-authored changes in a git-tracked repo, commit and `git push` unless user opts out.
- If push target is unclear, create and use a `codex/<topic>` branch first.
- After push/cleanup requests, verify and report `git status --short`.

## Local project docs recommendation

- Maintain a project `RUNBOOK.md` for procedural operations and keep this `AGENTS.md` policy-focused.
- Read `RUNBOOK.md` before executing long-running or high-cost operations.

## Project-specific additions (vllm)

- When a PR check fails, pull failing logs first and classify as code issue vs infra issue before editing code.
- For upstream PR communication via GitHub CLI, prefer `gh pr comment --body '...'` single-quoted payloads.
- Keep bugfix stacks split into focused PRs when changes touch independent concerns (processor alignment, encoder robustness, loader helpers, etc.).

## Confusion log

- 2026-02-09: Using `apply_patch` via `shell_command` triggered tooling warning and avoidable churn. Prevention: direct `apply_patch` tool only.
- 2026-02-09: Unescaped backticks in a `gh pr comment` shell command caused `zsh` command substitution and malformed PR comment. Prevention: single-quote bodies.
- 2026-02-09: `gh pr checks` returned exit code `8` during pending checks and looked like a failure path. Prevention: parse check states, do not treat as hard failure.
- 2026-02-09: RTD and `bc_lint` failures were external GitHub/RTD 500 errors, not code regressions. Prevention: require failure classification before follow-up patches.
