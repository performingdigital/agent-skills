---
name: pr
description: Create a pull request for the current branch against develop and open it in the browser
author: giorgiopogliani
version: "1.0"
tags:
  - git
  - workflow
  - github
---

## Steps

1. Run `git status` to check for uncommitted changes. If there are any, warn the user before proceeding.
2. Run `git log --oneline develop..HEAD` and `git diff develop...HEAD` to understand all changes included in the PR
3. Ensure the current branch is pushed to the remote (`git push -u origin <branch>` if needed)
4. Write a short PR title (under 70 characters) and a body with a `## Summary` section listing the key changes as bullet points
5. Create the PR against `develop` using `gh pr create --base develop --title "..." --body "..."`
6. Open the PR in the browser using `gh pr view --web`
7. Return the PR URL to the user
