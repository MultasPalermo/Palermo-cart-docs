# Palermo Cart Documentation

Project documentation, architecture diagrams, ADRs (Architecture Decision Records), CI/CD workflows, and branching policies.

## Repository Information

- **Category**: Documentation
- **Purpose**: Centralized documentation for all Palermo Cart repositories
- **Contents**: Architecture diagrams, decision records, workflows, policies

## Branching Policy

### Main Branches

This repository follows a **4-environment branching strategy**:

- `dev` - Development environment documentation
- `qa` - Quality Assurance environment documentation
- `staging` - Pre-production environment documentation
- `main` - Production environment documentation

### Branch Naming Convention

Each User Story (HU) documentation must have its own branch per environment:

```
HU-{NUMBER}-{ENVIRONMENT}
```

Examples:
- `HU-01-dev`
- `HU-01-qa`
- `HU-01-staging`

### Release Branches (Main Only)

Documentation releases use sprint-based release branches:

```
release.{SPRINT}.{VERSION}
```

Example: `release.1.1`, `release.1.2`

## Workflow by Environment

### 1. Development (dev)

```bash
git pull origin dev
git switch dev
git switch -c HU-01-dev

# Update documentation for feature/fix
# Create diagrams, update ADRs

# Create MR: HU-01-dev → dev
```

**Closure**: Development is closed when branch `dev` is updated.

### 2. Quality Assurance (qa)

```bash
git pull origin qa
git switch qa
git switch -c HU-01-qa

# Update QA-specific documentation
# Validate documentation completeness

# Create MR: HU-01-qa → qa
```

**Closure**: QA development is closed when branch `qa` is updated.

### 3. Staging

```bash
git pull origin staging
git switch staging
git switch -c HU-01-staging

# Update staging documentation
# Integrate approved documentation from QA

# Create MR: HU-01-staging → staging
```

**Closure**: Staging is closed when branch `staging` is updated.

### 4. Main (Production)

```bash
git pull origin main
git switch main
git switch -c release.1.1

# Consolidate all documentation for production release
# Update release notes and changelogs

# Create MR: release.1.1 → main
```

**Note**: One release per sprint (weekly).

## Important Rules

- **Never commit directly** to `dev`, `qa`, `staging`, or `main`
- Document all changes after merges to `staging` or `main` in other repositories
- Keep documentation synchronized with code changes
- Follow [Conventional Commits](https://www.conventionalcommits.org/) format:
  - `docs:` - Documentation updates
  - `feat:` - New documentation sections
  - `fix:` - Documentation corrections
  - `chore:` - Maintenance tasks

## Documentation Structure

```
Palermo-cart-docs/
├── architecture/          # Architecture diagrams and decisions
│   ├── diagrams/         # System architecture diagrams
│   └── adrs/             # Architecture Decision Records
├── workflows/            # CI/CD and development workflows
│   ├── branching-policy.md
│   ├── ci-cd-pipeline.md
│   └── release-process.md
├── api/                  # API documentation
├── database/             # Database schema documentation
├── deployment/           # Deployment guides
└── README.md             # This file
```

## Code Review Process

1. Always request review before merging documentation
2. Ensure technical accuracy
3. Maintain consistency across all documentation

## Release Management

- Each release must be tagged: `v1.x.x`
- Weekly sprint releases
- Tag format: `v{MAJOR}.{MINOR}.{PATCH}`

## Related Repositories

- [Palermo-cart-api](../Palermo-cart-api) - Backend API (Golang)
- [Palermo-cart-portal](../Palermo-cart-portal) - Web frontend
- [Palermo-cart-app](../Palermo-cart-app) - Mobile application
- [Palermo-cart-db](../Palermo-cart-db) - Database schema

## Contributing

When making changes to code in other repositories:

1. Update relevant documentation in this repository
2. Keep architecture diagrams current
3. Document major decisions in ADRs
4. Update API documentation when endpoints change
