---
description: Create a git commit with staged and unstaged changes
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git add:*), Bash(git commit:*)
---

Analyze staged and unstaged changes, draft a commit message with a brief description and bulleted list of changes, then ask for confirmation before committing. Follow the project's commit message preferences: brief description, optional explanation sentence for complex changes, followed by a concise bulleted list of changes.

Use the following steps:
1. Run git status to see untracked files
2. Run git diff to see both staged and unstaged changes
3. Run git log to see recent commit messages for style reference
4. Draft a commit message with a brief description and bullet points
5. Add relevant files to the staging area
6. Show the commit message and ask for confirmation
7. Create the commit if approved using the --signoff flag
