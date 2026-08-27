# Regression analysis

Use the `regression-analysis` skill for report-only, evidence-based regression analysis of a WordPress Core local Git checkout, GitHub pull request, or Core Trac changeset. It checks compatibility risk without changing reviewed code or tests.

## Prerequisites

- Git.
- The [GitHub CLI (`gh`)](https://cli.github.com/), authenticated for the WordPress repositories.
- Network access for GitHub and WordPress Core Trac evidence.
- The [WordPress Trac MCP server](https://github.com/WordPress/trac-mcp), configured under the exact name `wordpress-trac`.
- A local `wordpress-develop` Git checkout for local mode.

The production Core Trac MCP endpoint is:

```text
https://wordpress-trac-mcp-server-prod.a8c-aiops.workers.dev/mcp
```

Clients that need a local bridge can use `mcp-remote`:

```json
{
  "mcpServers": {
    "wordpress-trac": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://wordpress-trac-mcp-server-prod.a8c-aiops.workers.dev/mcp"
      ]
    }
  }
}
```

## Run an analysis

In Claude Code, use one of these forms:

```text
/regression-analysis
/regression-analysis local /path/to/wordpress-develop
/regression-analysis pr 11865
/regression-analysis changeset 62408
```

No argument uses the current checkout. Local mode includes committed, staged, unstaged, and untracked changes. By default, it compares them with the upstream WordPress `trunk` merge-base. Inspection is read-only and does not switch branches or alter the checkout.

PR mode gets the pull request and its GitHub evidence through `gh`. Changeset mode gets Core Trac evidence through `wordpress-trac`, then maps the changeset to the GitHub mirror through `gh`. Remote modes may create an isolated temporary checkout and remove it after analysis.

Bare numbers are ambiguous. Always select `pr` or `changeset`; the skill does not guess.

## Evidence boundaries

All GitHub access uses `gh`. All WordPress Core Trac access uses the configured MCP server. The skill does not replace either source with scraping or another client.

Missing mandatory evidence blocks analysis. This includes evidence needed to establish the target identity, exact range, or complete source-backed diff.

## Report and safety

The normal report contains the target, range, intent, verdict, findings, compatibility coverage, verification, and residual risks. Each finding has a severity and one of three confidence levels:

- `Confirmed`: differential or equivalent direct proof shows the change introduced the failure.
- `Probable`: direct code or data flow and consumer or platform evidence support the impact.
- `Possible`: a concrete failure path exists, but a required precondition remains unverified.

If mandatory evidence is unavailable, the report explains why analysis is blocked. A report with no concrete regressions is limited to its stated scope and evidence. It is not a universal safety claim.

The skill reports only. It does not modify code or tests. It does not execute contributor-controlled code, scripts, tests, or reproducers without explicit approval. Approved execution must use an isolated, secret-free environment.

## Use with Codex

Invoke the skill directly:

```text
Use $regression-analysis to analyze the current WordPress Core change for regressions.
```
