---
description: Review your local changes with Scrutin before you open the PR (no account, runs on your machine)
argument-hint: "[--all | --staged | path]"
allowed-tools: Bash(command -v scrutin), Bash(scrutin --version), Bash(scrutin local:*), Bash(git status:*), Bash(git rev-parse:*), Read
---

You are running Scrutin, a local code review CLI, on the repository the user has open, and then walking them through what it found. Arguments from the user: `$ARGUMENTS`

## 1. Check that the CLI is installed

Run `command -v scrutin`. If it is missing, stop and show the user how to install it. Do not run the installer yourself:

- macOS / Linux: `curl -fsSL "https://get.scrutin.dev/agent?src=claude-code-plugin" | bash`
- Windows (PowerShell): `irm https://get.scrutin.dev/agent.ps1 | iex`

No account is needed. After installing, they can run `/scrutin` again.

## 2. Pick the scope

- `--staged`: only staged files → `scrutin local . --staged --json --no-ai`
- `--all`: the whole repository → `scrutin local . --json --no-ai`
- a path: only that path → `scrutin local <path> --json --no-ai`
- no argument (default): if `git status --porcelain` shows changes, review only the changed files and their direct dependents → `scrutin local . --incremental --json --no-ai`; if there are no changes, or this is not a git repository, review the whole directory → `scrutin local . --json --no-ai`

Run each command on its own: no `;`, `&&`, pipes or `echo`, so it matches the allowed tools and the user is not asked for extra permissions. Always pass `--no-ai`, so the review is static and runs entirely on the user's machine. Progress lines go to stderr; the JSON report goes to stdout. An exit code of 1 means issues were found, not that the command failed.

## 3. Report

Parse the JSON (`issues`, `issues_by_severity`, `files_analyzed`, `total_issues`). Then:

1. One line with the scope and the count per severity.
2. The issues, most severe first, grouped by file, each as `path:line` — title — one sentence on why it matters, using the issue's `description` and `suggestion`. Secrets are already masked in `code_snippet`; never print the full value, even if you can read it in the file.
3. If there are no issues, say what was reviewed and that Scrutin reported nothing at the selected severity. Do not claim the code is safe or bug-free.

Do not edit any file on your own. Offer to work through the findings one by one, and change code only after the user agrees to each change.

If the output says a finding needs a paid plan or an AI provider, mention it in one line and point to https://scrutin.dev/en. Do not run AI modes without the user asking for them.
