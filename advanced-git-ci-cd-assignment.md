# Advanced Git & CI/CD Technical Guide

## 1. Workflow Architecture & Protection

### Explanation

For our team, I recommend using **Trunk-Based Development** because we are a fast-moving startup.  
This strategy keeps the workflow simple: developers create short-lived feature branches, open pull requests, and merge quickly into `main` after review and testing.

This is better for a startup because:
- It reduces long merge conflicts.
- It helps us release faster.
- It keeps the codebase always close to production-ready.

### Branch Protection Rules for `main`

We should enforce these rules on the `main` branch:

1. **Require pull request before merging**
   - No one can push directly to `main`.
   - Every change must go through a Pull Request.

2. **Require status checks to pass**
   - CI/CD tests must pass before merging.
   - If tests fail, the PR cannot be merged.

3. **Require at least one approval**
   - Another developer must review the code before merging.

4. **Restrict force pushes**
   - Developers cannot rewrite the history of `main`.

---

## 2. History Cleanup - Interactive Rebase

### Explanation

Interactive rebase helps developers clean messy commits before opening a Pull Request.

- `pick`: keeps the commit as it is.
- `squash`: combines the commit with the commit before it.

For example, if a developer has commits like:

- `wip1`
- `wip2`
- `wip3`
- `typo`
- `done`

They can squash them into one clean commit before code review.

### Command

To open an interactive rebase for the last 5 commits:

```bash
git rebase -i HEAD~5