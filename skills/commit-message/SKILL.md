---
name: commit-message
description: Generate a WordPress Core Subversion commit message from a GitHub pull request and its linked Trac ticket.
disable-model-invocation: true
user-invocable: true
allowed-tools:
  - Bash(gh pr view:*)
  - mcp__wordpress-trac__*
argument-hint: "[pr-number]"
---

# WordPress Commit Message Generator

Generate a WordPress core commit message from a GitHub PR and its linked Trac ticket.

Follow the "WordPress Core Commit Message Format" section below when generating the commit message.

## Context

- If a PR number is provided as `$1`, use that. Otherwise omit it from `gh` commands to use the current branch's PR.

## Instructions

1. **Get PR info and extract Trac ticket:**

   - Fetch PR details using:
     ```sh
     gh pr view [pr-number] --json title,body,number,url --template '{{.title}}
     ---
     PR Number: {{.number}}
     PR URL: {{.url}}
     ---
     {{.body}}'
     ```
   - The PR information should help inform the commit message.
   - Look in the PR description for a line starting with `Trac ticket: `
   - The ticket URL may be a markdown link `[text](url)` or plain text URL
   - Extract the ticket number from the URL (e.g., `https://core.trac.wordpress.org/ticket/64419` → `64419`)
   - If multiple Trac tickets are referenced, identify the primary ticket (the one being fixed). Additional tickets will become `See #...` references.
   - If no Trac ticket is found, ask the user for one
   - Note any changeset references (`r12345` or `[12345]`) in the PR description for step 3.

2. **Fetch Trac ticket details:**

Always use the WordPress Trac MCP tools to fetch ticket details, including enough comments to review the full discussion when possible.

- Fetch the main ticket.
- Use `component` for the commit message prefix, but NOT if it's "General" (omit the prefix in that case)
- Use the ticket summary and description to help form the commit message
- Look for related ticket references (#12345) in the description
- Fetch related tickets to understand the relationship
- Include related tickets as `See #...` references in the commit message
- Reference related tickets in the description if appropriate to explain context
- Collect any changeset references (`r12345` or `[12345]`, e.g., "reverts r58123", "follow-up to [58123]") from the ticket and its discussion, combining them with any found in the PR description from step 1.

3. **Explore discovered changesets:**

Always use the WordPress Trac MCP tools to fetch changeset details, including the diff when it helps explain the relationship.

Use changeset information to understand relationships:

- What the original change did (for reverts or follow-ups)
- Related tickets that may need `See #...` references
- Context that should be mentioned in the commit message description
- Add a "Follow-up to [nnnnn]" line when this commit directly continues, reverts, or fixes a previous changeset.

4. **Build the props list:**

   - Fetch the PR's props comment:
     ```sh
     gh pr view [pr-number] --json comments --jq '.comments[] | select(.body | test("Use this line as a base for the props")) | .body'
     ```
   - Extract the props list from the line starting with `Props `
   - If no props comment is found (new PRs or bot failure), build the props list from the PR author, reviewers, and Trac ticket participants instead.
   - Review the Trac ticket discussion. Add the profile name of any participant who contributed. Skip trivial contributions or obvious spam, but include folks when in doubt.
   - Merge all sources, deduplicating usernames. The PR bot already uses WordPress.org usernames. For Trac participants, use their WordPress.org profile name as shown on Trac.

5. **Generate the commit message:**

   - Follow the WordPress commit message format guidelines below

## Output

Output ONLY the commit message text, properly formatted and ready to copy. Do not include any other commentary or explanation. Use a markdown code block so it's easy to copy.

# WordPress Core Commit Message Format

This skill documents the official formatting guidelines for WordPress core commit messages.

## Message Structure

```
Component: Brief summary.

Longer description with more details, such as a `new_hook` being introduced with the context of a `$post` and a `$screen`.

More paragraphs can be added as needed.

Developed in {GitHub PR URL}.

Follow-up to [12345], [67890].

Props person, another.
Fixes #12345. See #67890.
```

## Brief Summary (First Line)

- Must be one line, no line breaks
- Aim for ~50 characters, max 70
- Prefix with component/focus of the change (from Trac ticket component, unless it's "General")
- The list of valid components is at the end of this document
- Use imperative mood: "Add feature" not "Adds feature" or "Added feature"
- Must end with a period

## Description

- **Be brief. Default to the shortest description that conveys the _what_ and _why_ — usually two to four sentences.** When in doubt, cut. A short, correct description is better than a thorough one.
- Do NOT restate the diff or explain implementation mechanics (which methods, modes, or algorithms changed). That's the _how_; the diff already shows it.
- One short paragraph is typical. A second paragraph is the exception, not the norm — add one only when context genuinely requires it (e.g. explaining a related ticket or a non-obvious motivation).
- If the summary line fully captures the change, omit the description entirely (keep only the Developed in, Props, and Fixes lines).
- Separated from summary by a blank line
- Informed by both the PR and the Trac ticket, but summarize — do not absorb their length. The PR description's detail, test output, and links are context for _you_, not content to copy.
- Do NOT manually wrap lines
- Don't include time estimates or scheduling language
- Code/hooks in backticks: `function_name()`, `hook_name`
- Each sentence should begin with a capital letter and end with a period

## Developed In Line

- Add `Developed in {PR URL}.` at the end of the description
- Must end with a period
- Must be preceded by a blank line
- Comes BEFORE Follow-up to line (if present)
- Must be followed by a blank line

## Follow-up To Line (Optional)

- Add `Follow-up to [12345], [67890].` when this commit directly continues, reverts, or fixes a previous changeset
- Format changeset numbers as `[123]` (square brackets) — these reference changesets, not tickets
- Comes AFTER Developed in line
- Must be preceded by a blank line
- Must be followed by a blank line before Props

## Props Line

- Give props to all contributors: patches, code suggestions, design, testing, reporting
- Format: `Props username1, username2.`
- No `@` before usernames
- No colon after "Props"
- Separate usernames with comma + space
- Must end with a period
- Use WordPress.org usernames (check Trac for correct usernames)
- Don't use `props` anywhere except the Props line

## Ticket References

- On their own line below Props
- `Fixes #12345.` - closes the ticket
- `See #12345.` - references without closing
- Multiple tickets: `Fixes #123, #456. See #789.`

## Valid Components

The following components are the **only** valid component prefixes for WordPress core commit messages.

- Administration
- AI
- Bootstrap/Load
- Build/Test Tools
- Bundled Theme
- Cache API
- Comments
- Cron API
- Customize
- Database
- Date/Time
- Editor
- Export
- External Libraries
- Feeds
- Filesystem API
- Formatting
- General
- Help/About
- HTML API
- HTTP API
- I18N
- Import
- Interactivity API
- Mail
- Media
- Networks and Sites
- Options, Meta APIs
- Permalinks
- Plugins
- Posts, Post Types
- Privacy
- Query
- REST API
- Script Loader
- Security
- Site Health
- Sitemaps
- Taxonomy
- Themes
- Toolbar
- Upgrade/Install
- Users
- XML-RPC
