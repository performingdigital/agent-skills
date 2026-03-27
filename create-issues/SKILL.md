---
name: create-issues
description: Analys the project, find all opportunities and create issues markdown files
author: giorgiopogliani
version: "1.0"
tags:
  - maintainability
---

## Steps

1. Create an issues folder if not already there
2. Analys current issues if any to understand the status of the project 
3. Analys the project code looking for dead code, duplication, bad practice, inconsistency, security issues, hacks, static methods, god classes, all possible code smells 
4. Update or create for each opportunity/issue the markdown file with a number and a name, also add metadata in the frontmatter like tags, severity, status
5. Present a summary of the most important issues and the best candidates to pick for the next session.

