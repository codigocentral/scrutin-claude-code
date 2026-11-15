# Scrutin for Claude Code

Type `/scrutin` in Claude Code and your local changes get reviewed before you open the PR.

- **No account.** Install the CLI and run it.
- **Runs on your machine.** The plugin calls `scrutin local --no-ai`: a static review that reads your files from disk and sends no code anywhere.
- **Claude explains, you decide.** Claude walks you through each finding (`path:line`, why it matters) and only changes code when you say so.

It looks at the files you changed (and their direct dependents) for security issues, leaked secrets, bugs and Dockerfile problems.

## Install

1. Install the Scrutin CLI (no account needed):

   ```bash
   # macOS / Linux
   curl -fsSL "https://get.scrutin.dev/agent?src=claude-code-plugin" | bash
   ```

   ```powershell
   # Windows (PowerShell)
   irm https://get.scrutin.dev/agent.ps1 | iex
   ```

2. Add the plugin in Claude Code:

   ```text
   /plugin marketplace add codigocentral/scrutin-claude-code
   /plugin install scrutin-local@scrutin-local
   ```

## Use

```text
/scrutin            # changed files and their direct dependents (or the whole repo if nothing changed)
/scrutin --staged   # only staged files, right before you commit
/scrutin --all      # the whole repository
/scrutin src/api    # a specific path
```

Example:

```text
> /scrutin
Reviewed 2 changed files: 3 critical, 2 high, 1 medium.

app.py
  app.py:2  Stripe Secret Key Detected: a live key is committed in source. Roll it and load it from the environment.
  app.py:5  SQL built with string formatting: user input reaches the query. Use a parameterized query.
  app.py:7  Command injection: subprocess with shell=True runs whatever reaches `cmd`. Pass a list with shell=False.
Dockerfile
  Dockerfile:1  Base image uses :latest: builds are not reproducible. Pin a version.
  ...
Want me to go through them one by one?
```

## Want reviews on the PR too?

On the Pro plan, the same CLI also comments on pull requests in GitHub, GitLab and Azure DevOps, with AI review using your own provider key. See [scrutin.dev](https://scrutin.dev/en) and the [docs](https://docs.scrutin.dev).

## Feedback

This is new and we want blunt feedback: open an issue here or write to [support@scrutin.dev](mailto:support@scrutin.dev).

## License

The plugin (manifests and command) is released under the [MIT License](LICENSE). The Scrutin CLI has its own terms: [scrutin.dev/terms](https://scrutin.dev/terms) and [privacy](https://scrutin.dev/privacy).
