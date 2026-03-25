---
description: Create a GitHub PR with proper title and description
allowed-tools: Bash(git log:*), Bash(git diff:*), Bash(git status:*), Bash(git rev-parse:*), Bash(git merge-base:*), Bash(gh pr create:*), Bash(gh pr list:*)
---

Create a GitHub pull request for the current branch.

Use the following steps:

1. Determine the target branch: check if there's an upstream tracking branch, otherwise fall back to `master` then `main`.
2. Find the merge-base between the current branch and the target: `git merge-base <target> HEAD`
3. Get all commits since the merge-base: `git log <merge-base>..HEAD --oneline` for a summary, and `git log <merge-base>..HEAD --format="%s%n%b"` for full messages.
4. Get the diff stat: `git diff <merge-base>...HEAD --stat`
5. Analyze the commits to understand the scope and nature of all changes.
6. Draft a concise, technical PR title (under 70 chars) that reflects the overall change.
7. Draft a PR body following this style:
   - `## Summary` section with a tight bulleted list — technical, specific, no fluff
   - No explicit test list unless something unusual is worth calling out
   - Focus on what changed and why, not how tests were written
   - Keep it short enough to skim in 30 seconds
8. Show the draft title and body, then ask for confirmation before creating the PR.
9. Create the PR with `gh pr create` using a HEREDOC for the body.
