---
name: git-work-tree
description: Use when the user or orchestrating/planning agents ask to create a new work tree for a large chunk of work that can be parallelized in the current code base
---

# Git work tree
Create a git work tree by following these conventions
- branching out from the `dev` branch from the remote repository `origin` unless the user requires specific base branch
- do not track the dev branch
- new branch name should have a prefix that indicates the type of the work and it starts with
  - `feat/` for new feature
  - or `fix/` for bug fix
  - or `refactor/` for refactoring
  - or `chore/` for miscellaneous
  - or `prototype/` for fast prototyping
and followed by a descriptive name
- the worktree name will be the same as the branch name and the work tree should be created in the bare repository root directory instead of in the current working directory
- let the user to confirm the git command to be executed before you execute it
- if there are long running operations hooked to git operations, wait until they are completed and only then report to the user that creation of the work tree is completed
