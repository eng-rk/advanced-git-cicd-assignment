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

## 3. The Emergency Surgery - Cherry-Picking

### Explanation

git cherry-pick copies a specific commit from one branch and applies it to another branch.

This is useful in emergencies because we can move only the bug fix commit to the main branch without merging the entire develop branch, which may contain unstable code.

### Commands

```bash
git checkout main
```

```bash
git pull origin main
```

```bash
git cherry-pick 8f3a9b2
```

```bash
git push origin main
```

---

## 4. Integration Testing with Docker Compose (GitHub Actions)

### Explanation

Integration tests should run in CI/CD pipelines because they provide a clean and consistent environment for every developer.

Running tests locally may produce different results depending on operating systems, installed packages, or database states.

Using Docker Compose in GitHub Actions ensures the full multi-container application works correctly before merging into the main branch.

### GitHub Actions Workflow

```yaml
name: Docker Compose Integration Test

on:
  pull_request:
    branches:
      - main

jobs:
  compose-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository code
        uses: actions/checkout@v4

      - name: Start Docker Compose stack
        run: docker-compose up -d

      - name: Wait for services to initialize
        run: sleep 15

      - name: Test web service
        run: curl -f http://localhost:8080 || exit 1

      - name: Show Docker Compose logs
        if: always()
        run: docker-compose logs

      - name: Stop Docker Compose stack
        if: always()
        run: docker-compose down
```
