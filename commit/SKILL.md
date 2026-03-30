---
name: commit
description: Stage and commit changes with a concise, descriptive commit message
author: giorgiopogliani
version: "1.0"
tags:
  - git
  - workflow
---

## Steps

1. Run `git status` to see all modified, added, and untracked files
2. Run `git diff` (staged and unstaged) to understand what changed
3. Run `git log --oneline -5` to match the existing commit message style
4. Present a summary of all changes to the user, grouped by file, with a short explanation of what each change does
5. Stage the relevant files (prefer `git add <file>` over `git add -A` to avoid committing secrets or unwanted files)
6. Write a short, concise commit message (one line, under 72 characters) that explains **why** the change was made, not just what changed. Follow conventional commits style if the repo already uses it.
7. Create the commit. Do NOT add any `Co-Authored-By` trailer.
8. Show the final commit hash and message to confirm success
