# Branching Policy - Palermo Cart Project

## Overview

This document defines the branching strategy and workflow for all repositories in the Palermo Cart polyrepo architecture.

## Core Principles

1. **Never commit directly** to protected branches (`dev`, `qa`, `staging`, `main`)
2. **Each User Story (HU)** must have its own branch per environment
3. **Only `main`** uses release branches for production deployments
4. **Weekly sprint releases** are deployed to production
5. **Code review is mandatory** before any merge

## Environment Branches

### 1. Development (`dev`)

- **Purpose**: Active development environment
- **Deployment**: Automatic on merge
- **Testing**: Unit tests required
- **Branch from**: `dev`
- **Merge to**: `dev`

### 2. Quality Assurance (`qa`)

- **Purpose**: QA testing environment
- **Deployment**: Automatic on merge
- **Testing**: Integration and acceptance tests
- **Branch from**: `qa`
- **Merge to**: `qa`

### 3. Staging (`staging`)

- **Purpose**: Pre-production validation
- **Deployment**: Automatic on merge
- **Testing**: Full regression and performance tests
- **Branch from**: `staging`
- **Merge to**: `staging`

### 4. Production (`main`)

- **Purpose**: Production environment
- **Deployment**: Manual, after approval
- **Testing**: Smoke tests post-deployment
- **Branch from**: `main`
- **Merge to**: `main`

## Branch Naming Conventions

### User Story Branches

Format: `HU-{NUMBER}-{ENVIRONMENT}`

Examples:
```
HU-01-dev
HU-01-qa
HU-01-staging
```

### Release Branches (Main Only)

Format: `release.{SPRINT}.{VERSION}`

Examples:
```
release.1.1
release.1.2
release.2.1
```

### Hotfix Branches (Emergency Fixes)

Format: `hotfix-{DESCRIPTION}`

Example:
```
hotfix-payment-gateway
```

## Workflow by Environment

### Development Workflow

```bash
# 1. Update dev branch
git pull origin dev
git switch dev

# 2. Create feature branch
git switch -c HU-01-dev

# 3. Develop feature
# ... make changes ...

# 4. Commit changes
git add .
git commit -m "feat: implement user authentication"

# 5. Push branch
git push origin HU-01-dev

# 6. Create Merge Request
# HU-01-dev → dev

# 7. After approval and merge, delete branch
git branch -d HU-01-dev
```

### QA Workflow

```bash
# 1. Update qa branch
git pull origin qa
git switch qa

# 2. Create QA branch
git switch -c HU-01-qa

# 3. Perform QA validations
# ... run tests, fix issues ...

# 4. Commit changes
git add .
git commit -m "test: add integration tests for authentication"

# 5. Push branch
git push origin HU-01-qa

# 6. Create Merge Request
# HU-01-qa → qa

# 7. After approval and merge, delete branch
git branch -d HU-01-qa
```

### Staging Workflow

```bash
# 1. Update staging branch
git pull origin staging
git switch staging

# 2. Create staging branch
git switch -c HU-01-staging

# 3. Perform staging validations
# ... final testing ...

# 4. Commit changes (if needed)
git add .
git commit -m "chore: update staging configuration"

# 5. Push branch
git push origin HU-01-staging

# 6. Create Merge Request
# HU-01-staging → staging

# 7. After approval and merge, delete branch
git branch -d HU-01-staging
```

### Production Release Workflow

```bash
# 1. Update main branch
git pull origin main
git switch main

# 2. Create release branch
git switch -c release.1.1

# 3. Cherry-pick or merge approved HUs
# Example: Consolidate multiple HUs
git merge --no-ff HU-01-staging
git merge --no-ff HU-02-staging
git merge --no-ff HU-03-staging

# 4. Update version and changelog
# ... update version files ...

# 5. Commit release preparation
git add .
git commit -m "chore: prepare release 1.1"

# 6. Push release branch
git push origin release.1.1

# 7. Create Merge Request
# release.1.1 → main

# 8. After approval, tag release
git tag -a v1.1.0 -m "Release v1.1.0"
git push origin v1.1.0

# 9. Deploy to production

# 10. Delete release branch
git branch -d release.1.1
```

## Merge Request Process

### Requirements Before Creating MR

1. All tests must pass
2. Code follows project standards
3. Documentation is updated
4. No merge conflicts

### MR Review Checklist

- [ ] Code quality and readability
- [ ] Test coverage
- [ ] Documentation completeness
- [ ] No security vulnerabilities
- [ ] Performance considerations
- [ ] Breaking changes documented

### Approval Requirements

- **dev**: 1 approval minimum
- **qa**: 1 approval from QA team
- **staging**: 2 approvals (1 dev + 1 QA)
- **main**: 3 approvals (1 dev lead + 1 QA + 1 DevOps)

## Commit Message Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Test additions or modifications
- `chore`: Maintenance tasks
- `perf`: Performance improvements

### Examples

```bash
feat(auth): add JWT token validation

Implement JWT token validation middleware for API authentication.
Includes token expiration check and user role verification.

Closes #123

---

fix(cart): prevent duplicate items in shopping cart

Fixed race condition causing duplicate items when adding products
quickly to the cart.

Fixes #456

---

docs(api): update authentication endpoint documentation

Added examples and error response codes for /api/auth endpoints.
```

## Branch Protection Rules

### Protected Branches

All main branches are protected:
- `main`
- `staging`
- `qa`
- `dev`

### Protection Settings

1. Require pull request reviews before merging
2. Require status checks to pass before merging
3. Require branches to be up to date before merging
4. Include administrators in restrictions
5. Restrict who can push to matching branches

## Emergency Hotfix Process

For critical production issues:

```bash
# 1. Create hotfix branch from main
git switch main
git pull origin main
git switch -c hotfix-critical-bug

# 2. Fix the issue
# ... make changes ...

# 3. Commit fix
git add .
git commit -m "fix: resolve critical payment processing issue"

# 4. Push hotfix
git push origin hotfix-critical-bug

# 5. Create MR to main (expedited review)
# hotfix-critical-bug → main

# 6. After merge, backport to other branches
git switch staging
git cherry-pick <commit-hash>
git push origin staging

git switch qa
git cherry-pick <commit-hash>
git push origin qa

git switch dev
git cherry-pick <commit-hash>
git push origin dev
```

## Multi-Repository Coordination

When changes span multiple repositories:

1. Create branches with the same HU number in all affected repos
2. Document cross-repo dependencies in commit messages
3. Merge in dependency order:
   - Database first (`Palermo-cart-db`)
   - API second (`Palermo-cart-api`)
   - Frontend/App last (`Palermo-cart-portal`, `Palermo-cart-app`)
4. Update documentation (`Palermo-cart-docs`) after all merges

## Best Practices

1. **Keep branches short-lived**: Merge within 2-3 days
2. **Sync frequently**: Pull from base branch daily
3. **Commit often**: Small, focused commits
4. **Test locally**: Run tests before pushing
5. **Clear descriptions**: Write descriptive MR descriptions
6. **Clean history**: Squash commits if necessary before merging
7. **Tag releases**: Always tag production releases

## Troubleshooting

### Merge Conflicts

```bash
# 1. Update your branch with latest from base
git switch HU-01-dev
git pull origin dev

# 2. Resolve conflicts in your editor

# 3. Mark as resolved
git add .
git commit -m "chore: resolve merge conflicts"

# 4. Push updated branch
git push origin HU-01-dev
```

### Accidental Commit to Protected Branch

```bash
# 1. Create a new branch from current state
git switch -c HU-XX-dev

# 2. Push the new branch
git push origin HU-XX-dev

# 3. Reset protected branch to remote
git switch dev
git reset --hard origin/dev
```

## Release Schedule

- **Weekly sprints**: Monday to Friday
- **Release day**: Every Friday
- **Release window**: 2:00 PM - 4:00 PM
- **Rollback plan**: Always prepared before release

## Support

For questions about the branching policy:
- Contact the DevOps team
- Check the #git-workflow Slack channel
- Refer to this documentation
