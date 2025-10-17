# Polyrepo Architecture - Palermo Cart

## Overview

Palermo Cart uses a polyrepo architecture with five independent repositories, each with its own version control, CI/CD pipeline, and deployment cycle.

## Repository Structure

```
Palermo Cart Project
│
├── Palermo-cart-docs        (Documentation & Architecture)
│   ├── Branch: main
│   ├── Branch: staging
│   ├── Branch: qa
│   └── Branch: dev
│
├── Palermo-cart-api          (Backend REST API - Golang)
│   ├── Branch: main
│   ├── Branch: staging
│   ├── Branch: qa
│   └── Branch: dev
│
├── Palermo-cart-portal       (Web Frontend - React/Vue/Angular)
│   ├── Branch: main
│   ├── Branch: staging
│   ├── Branch: qa
│   └── Branch: dev
│
├── Palermo-cart-app          (Mobile App - Flutter/React Native)
│   ├── Branch: main
│   ├── Branch: staging
│   ├── Branch: qa
│   └── Branch: dev
│
└── Palermo-cart-db           (Database - PostgreSQL)
    ├── Branch: main
    ├── Branch: staging
    ├── Branch: qa
    └── Branch: dev
```

## Environment Flow

```
Developer Workflow:
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  ┌─────────┐    ┌─────────┐    ┌──────────┐    ┌────────┐ │
│  │   DEV   │───▶│   QA    │───▶│ STAGING  │───▶│  MAIN  │ │
│  │ (Local) │    │ (Test)  │    │  (Pre)   │    │ (Prod) │ │
│  └─────────┘    └─────────┘    └──────────┘    └────────┘ │
│       │              │               │              │       │
│       ▼              ▼               ▼              ▼       │
│  HU-XX-dev     HU-XX-qa     HU-XX-staging    release.X.Y  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

## Branch Strategy per Repository

### Development Branch (`dev`)
- **Purpose**: Active development
- **Deployment**: Auto-deploy to dev environment
- **Protection**: Requires 1 approval for MR
- **Testing**: Unit tests required

### QA Branch (`qa`)
- **Purpose**: Quality assurance testing
- **Deployment**: Auto-deploy to QA environment
- **Protection**: Requires 1 approval from QA team
- **Testing**: Integration and acceptance tests

### Staging Branch (`staging`)
- **Purpose**: Pre-production validation
- **Deployment**: Auto-deploy to staging environment
- **Protection**: Requires 2 approvals (dev + QA)
- **Testing**: Full regression and performance tests

### Main Branch (`main`)
- **Purpose**: Production environment
- **Deployment**: Manual deployment after approval
- **Protection**: Requires 3 approvals (dev lead + QA + DevOps)
- **Testing**: Smoke tests post-deployment

## Feature Development Flow

```
1. Create Feature Branch
   ┌─────────────────┐
   │   git switch dev│
   │   git pull      │
   │   git switch -c │
   │   HU-01-dev     │
   └────────┬────────┘
            │
            ▼
2. Develop & Test
   ┌─────────────────┐
   │ Write code      │
   │ Write tests     │
   │ Run locally     │
   └────────┬────────┘
            │
            ▼
3. Push & Create MR
   ┌─────────────────┐
   │ git push origin │
   │ HU-01-dev       │
   │ Create MR       │
   └────────┬────────┘
            │
            ▼
4. Code Review
   ┌─────────────────┐
   │ Review feedback │
   │ Make changes    │
   │ Get approval    │
   └────────┬────────┘
            │
            ▼
5. Merge to Dev
   ┌─────────────────┐
   │ Merge approved  │
   │ Auto-deploy dev │
   │ Delete branch   │
   └────────┬────────┘
            │
            ▼
6. Repeat for QA
   ┌─────────────────┐
   │ Create HU-01-qa │
   │ Test in QA env  │
   │ Merge to qa     │
   └────────┬────────┘
            │
            ▼
7. Repeat for Staging
   ┌─────────────────┐
   │ Create          │
   │ HU-01-staging   │
   │ Merge to staging│
   └────────┬────────┘
            │
            ▼
8. Include in Release
   ┌─────────────────┐
   │ Add to          │
   │ release.X.Y     │
   │ Deploy to main  │
   └─────────────────┘
```

## Release Process

```
Sprint Timeline (Weekly):
┌────────────────────────────────────────────────────────────┐
│                                                            │
│  Monday          Wednesday        Thursday         Friday │
│  ───────         ─────────        ────────        ─────── │
│  Sprint          Mid-Sprint       Code Freeze     Release │
│  Planning        Review           @ 5:00 PM       @ 2-4 PM│
│                                                            │
│  ▼               ▼                ▼                ▼       │
│  Create HUs      Review Progress  Final Testing   Deploy  │
│  Start Dev       QA Testing       Approve Release to Prod │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Release Branch Example

```bash
# On Friday, create release branch
git switch main
git pull origin main
git switch -c release.1.1

# Include approved HUs
# HU-01, HU-02, HU-03, HU-07, HU-09

# Update version
# Version: v1.1.0

# Tag release
git tag -a v1.1.0 -m "Release v1.1.0"

# Deploy to production
```

## Cross-Repository Dependencies

When changes span multiple repositories:

```
Deployment Order:
┌──────────────────────────────────────────────────────┐
│                                                      │
│  1. Database (Palermo-cart-db)                      │
│     └─▶ Run migrations first                        │
│                                                      │
│  2. Backend API (Palermo-cart-api)                  │
│     └─▶ Deploy API with new endpoints               │
│                                                      │
│  3. Frontend (Palermo-cart-portal)                  │
│     └─▶ Deploy web frontend                         │
│                                                      │
│  4. Mobile App (Palermo-cart-app)                   │
│     └─▶ Release to app stores                       │
│                                                      │
│  5. Documentation (Palermo-cart-docs)               │
│     └─▶ Update after all deployments               │
│                                                      │
└──────────────────────────────────────────────────────┘
```

## Branch Protection Rules

### Protected Branches
All main branches (`main`, `staging`, `qa`, `dev`) have the following protections:

✅ Require pull request reviews before merging
✅ Require status checks to pass before merging
✅ Require branches to be up to date before merging
✅ Include administrators in restrictions
✅ Restrict who can push to matching branches
✅ Require linear history
❌ Allow force pushes (disabled)
❌ Allow deletions (disabled)

## Repository Responsibilities

### Palermo-cart-docs
**Owner**: Documentation Team
**Purpose**: Centralized documentation
**Dependencies**: None (referenced by all)
**Deployment**: Static site generator

**Key Files**:
- README.md
- workflows/branching-policy.md
- workflows/release-process.md
- workflows/getting-started.md
- architecture/diagrams/
- architecture/adrs/

### Palermo-cart-api
**Owner**: Backend Team
**Purpose**: REST API and business logic
**Dependencies**: Palermo-cart-db
**Deployment**: Docker containers on Kubernetes

**Key Files**:
- main.go
- cmd/
- internal/
- pkg/
- api/

### Palermo-cart-portal
**Owner**: Frontend Team
**Purpose**: Web user interface
**Dependencies**: Palermo-cart-api
**Deployment**: Static files on CDN

**Key Files**:
- src/
- public/
- package.json

### Palermo-cart-app
**Owner**: Mobile Team
**Purpose**: iOS and Android apps
**Dependencies**: Palermo-cart-api
**Deployment**: App Store / Play Store

**Key Files**:
- lib/ (Flutter) or src/ (React Native)
- android/
- ios/
- pubspec.yaml or package.json

### Palermo-cart-db
**Owner**: Database Team / Backend Team
**Purpose**: Database schema and migrations
**Dependencies**: None (used by API)
**Deployment**: Migration scripts on PostgreSQL

**Key Files**:
- migrations/
- rollbacks/
- procedures/
- seeds/

## Benefits of Polyrepo Architecture

### ✅ Advantages

1. **Independent Deployment**
   - Each service can be deployed separately
   - Reduced risk of deployment failures
   - Faster deployment cycles

2. **Clear Boundaries**
   - Well-defined ownership per repository
   - Easier access control
   - Clearer responsibilities

3. **Flexible Technology Choices**
   - Each repo can use different tech stacks
   - Independent dependency management
   - No monolithic build system

4. **Isolated Version Control**
   - Independent versioning per service
   - Clearer commit history
   - Easier to track changes

5. **Reduced Blast Radius**
   - Issues in one repo don't affect others
   - Easier rollbacks
   - Better fault isolation

### ⚠️ Challenges

1. **Cross-Repo Coordination**
   - Need to coordinate changes across repos
   - Multiple MRs for related changes
   - Requires good communication

2. **Duplicate Configuration**
   - CI/CD setup per repository
   - Similar scripts across repos
   - More maintenance overhead

3. **Dependency Management**
   - Must manage cross-repo dependencies manually
   - Ensure compatible versions
   - Coordinate breaking changes

4. **Testing Integration**
   - End-to-end tests span multiple repos
   - Need integration test environment
   - More complex test setup

## Best Practices

### ✅ Do

- Keep branches short-lived (2-3 days max)
- Create feature branches for each HU
- Write descriptive commit messages
- Request code reviews
- Update documentation
- Test thoroughly before merging
- Delete branches after merging

### ❌ Don't

- Never commit directly to protected branches
- Don't keep branches open for weeks
- Don't merge without approval
- Don't skip testing
- Don't forget to update documentation
- Don't deploy without QA approval

## Monitoring and Metrics

Track these metrics per repository:

- **Lead Time**: Time from commit to production
- **Deployment Frequency**: How often we deploy
- **Change Failure Rate**: % of deployments causing failures
- **Mean Time to Recovery**: Time to fix production issues
- **Code Review Time**: Time for MR approval

## References

- [Branching Policy](../workflows/branching-policy.md)
- [Release Process](../workflows/release-process.md)
- [Getting Started](../workflows/getting-started.md)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
