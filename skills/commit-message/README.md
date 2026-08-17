# Commit message

Use the `commit-message` skill to generate a WordPress Core Subversion commit message from a GitHub pull request and its linked Trac ticket.

## Prerequisites

- The [GitHub CLI (`gh`)](https://cli.github.com/) authenticated with access to the pull request's repository.
- A WordPress Trac MCP server registered under the name `wordpress-trac`, so its tools match the skill's `mcp__wordpress-trac__*` permission. The skill uses it to read the Trac ticket, its discussion, and any referenced changesets.

## Generate a commit message

In Claude Code, invoke the skill with a PR number:

```text
/commit-message 8016
```

Or run it with no arguments from a checked-out branch to use that branch's pull request:

```text
/commit-message
```

The skill reads the pull request, follows its `Trac ticket:` reference, reviews the ticket discussion and related changesets, and builds the props list from the PR's props bot comment (falling back to the PR author, reviewers, and ticket participants). It outputs only the formatted commit message, ready to copy.

## Use with Codex

Invoke the skill directly:

```text
Use $commit-message to generate a WordPress Core commit message for this pull request.
```

## What the output follows

The message is formatted according to the [commit messages handbook](https://make.wordpress.org/core/handbook/best-practices/commit-messages/):

- The summary line is prefixed with the ticket's component (omitted for "General") and written in imperative mood.
- The description stays brief: the what and why, not a restatement of the diff.
- `Developed in`, `Follow-up to`, `Props`, and `Fixes`/`See` lines follow WordPress Core conventions, in that order.
