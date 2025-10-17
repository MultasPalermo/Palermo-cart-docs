# Release Process - Palermo Cart Project

## Overview

This document outlines the complete release process for deploying changes to production in the Palermo Cart project.

## Release Cycle

- **Sprint Duration**: 1 week (Monday to Friday)
- **Release Frequency**: Weekly
- **Release Day**: Every Friday
- **Release Window**: 2:00 PM - 4:00 PM
- **Code Freeze**: Thursday 5:00 PM

## Release Types

### 1. Regular Sprint Release

Weekly releases containing approved User Stories.

**Version Format**: `v{MAJOR}.{MINOR}.{PATCH}`

Example: `v1.2.0`

### 2. Hotfix Release

Emergency fixes for critical production issues.

**Version Format**: `v{MAJOR}.{MINOR}.{PATCH+1}`

Example: `v1.2.1` (hotfix after v1.2.0)

### 3. Major Release

Significant changes with breaking changes or major features.

**Version Format**: `v{MAJOR+1}.0.0`

Example: `v2.0.0`

## Pre-Release Checklist

### Week Beginning (Monday)

- [ ] Review sprint goals and HUs
- [ ] Confirm all HUs have acceptance criteria
- [ ] Verify CI/CD pipelines are operational
- [ ] Check environment health (dev, qa, staging)

### Mid-Sprint (Wednesday)

- [ ] Review HU progress
- [ ] Identify at-risk HUs
- [ ] Begin QA testing of completed HUs
- [ ] Update release notes draft

### Code Freeze (Thursday 5:00 PM)

- [ ] All HUs must be merged to staging
- [ ] Final QA sign-off on staging environment
- [ ] Performance testing completed
- [ ] Security scan passed
- [ ] Documentation updated
- [ ] Release notes finalized
- [ ] Rollback plan prepared

### Release Day (Friday)

- [ ] Final smoke tests on staging
- [ ] Stakeholder approval obtained
- [ ] Database backup completed
- [ ] Deployment team notified
- [ ] On-call team briefed

## Release Process Steps

### Step 1: Prepare Release Branch

```bash
# In each repository

# 1. Ensure you're on main and up to date
git switch main
git pull origin main

# 2. Create release branch
git switch -c release.1.1

# 3. Verify branch is clean
git status
```

### Step 2: Consolidate Approved HUs

```bash
# Example: Merging multiple HUs into release branch

# Merge HU-01
git merge --no-ff HU-01-staging -m "feat: add user authentication (HU-01)"

# Merge HU-02
git merge --no-ff HU-02-staging -m "feat: implement shopping cart (HU-02)"

# Merge HU-03
git merge --no-ff HU-03-staging -m "fix: resolve payment gateway timeout (HU-03)"

# Verify no conflicts
git status
```

### Step 3: Update Version and Changelog

#### Update Version Files

**For API (Golang):**
```go
// version.go
package main

const (
    Version = "1.1.0"
    BuildDate = "2025-01-17"
)
```

**For Frontend (package.json):**
```json
{
  "name": "palermo-cart-portal",
  "version": "1.1.0"
}
```

**For Mobile App:**
```yaml
# pubspec.yaml (Flutter)
version: 1.1.0+11

# package.json (React Native)
{
  "version": "1.1.0"
}
```

#### Update CHANGELOG.md

```markdown
# Changelog

## [1.1.0] - 2025-01-17

### Added
- User authentication with JWT tokens (HU-01)
- Shopping cart functionality (HU-02)
- Payment gateway integration (HU-07)

### Fixed
- Payment gateway timeout issues (HU-03)
- Cart item duplication bug (HU-09)

### Changed
- Updated user profile UI (HU-05)

### Security
- Implemented rate limiting on API endpoints
```

### Step 4: Commit Release Preparation

```bash
git add .
git commit -m "chore: prepare release v1.1.0

Release includes:
- HU-01: User authentication
- HU-02: Shopping cart
- HU-03: Payment gateway fixes
- HU-07: Payment integration
- HU-09: Cart bug fixes

Release date: 2025-01-17"

git push origin release.1.1
```

### Step 5: Create Merge Request

1. Go to repository on GitHub/GitLab
2. Create MR: `release.1.1` → `main`
3. Use release template (see below)
4. Assign reviewers (Dev Lead, QA Lead, DevOps)

#### MR Template for Releases

```markdown
## Release v1.1.0

### Release Information
- **Sprint**: Sprint 1.1
- **Release Date**: 2025-01-17
- **Release Window**: 2:00 PM - 4:00 PM

### Included User Stories
- HU-01: User authentication with JWT tokens
- HU-02: Shopping cart functionality
- HU-03: Payment gateway timeout fix
- HU-07: Payment gateway integration
- HU-09: Cart duplication bug fix

### Testing Summary
- ✅ All unit tests passing
- ✅ Integration tests passing
- ✅ QA sign-off received
- ✅ Performance tests passed
- ✅ Security scan passed

### Deployment Plan
1. Database migrations (5 min)
2. API deployment (10 min)
3. Frontend deployment (5 min)
4. Mobile app release (handled separately)
5. Smoke tests (10 min)

### Rollback Plan
- Database rollback scripts prepared
- Previous version artifacts available
- Rollback window: 30 minutes

### Impact Analysis
- **Downtime**: None expected (rolling deployment)
- **Database changes**: Yes (new tables for authentication)
- **API changes**: Yes (new endpoints, backward compatible)
- **Breaking changes**: None

### Post-Release Verification
- [ ] User login functionality
- [ ] Shopping cart operations
- [ ] Payment processing
- [ ] API response times
- [ ] Error rates

### Approvals Required
- [ ] Dev Lead
- [ ] QA Lead
- [ ] DevOps Lead

### Additional Notes
- Mobile app will be released to stores on Monday
- Documentation updated in Palermo-cart-docs
```

### Step 6: Review and Approval

Required approvals:
1. **Dev Lead**: Code quality and technical review
2. **QA Lead**: Testing and quality assurance
3. **DevOps Lead**: Deployment and infrastructure review

### Step 7: Tag Release

After MR is approved and merged:

```bash
# Switch to main and pull latest
git switch main
git pull origin main

# Create annotated tag
git tag -a v1.1.0 -m "Release v1.1.0

Features:
- User authentication with JWT
- Shopping cart functionality
- Payment gateway integration

Fixes:
- Payment gateway timeout
- Cart item duplication

Release Date: 2025-01-17"

# Push tag
git push origin v1.1.0

# Verify tag
git tag -l -n9 v1.1.0
```

### Step 8: Deploy to Production

#### Deployment Order

1. **Database (Palermo-cart-db)**
   ```bash
   # Run migrations
   psql -d palermo_cart_prod -f migrations/xxx_migration.sql

   # Verify
   psql -d palermo_cart_prod -c "SELECT version FROM schema_migrations ORDER BY version DESC LIMIT 1;"
   ```

2. **API (Palermo-cart-api)**
   ```bash
   # Deploy API (using your deployment tool)
   # Example with Docker:
   docker pull palermo-cart-api:v1.1.0
   docker service update --image palermo-cart-api:v1.1.0 api-service

   # Verify
   curl https://api.palermo-cart.com/health
   curl https://api.palermo-cart.com/version
   ```

3. **Frontend (Palermo-cart-portal)**
   ```bash
   # Deploy frontend
   # Example with CDN:
   aws s3 sync ./build s3://palermo-cart-portal/v1.1.0
   aws cloudfront create-invalidation --distribution-id XXX --paths "/*"

   # Verify
   curl https://palermo-cart.com/version.json
   ```

4. **Mobile App (Palermo-cart-app)**
   - iOS: Submit to App Store (Review takes 1-3 days)
   - Android: Deploy to Play Store (Review takes 1-2 hours)

### Step 9: Post-Deployment Verification

#### Smoke Tests

```bash
# API Health Check
curl https://api.palermo-cart.com/health

# User Authentication
curl -X POST https://api.palermo-cart.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"test","password":"test"}'

# Shopping Cart
curl https://api.palermo-cart.com/api/cart \
  -H "Authorization: Bearer <token>"

# Payment Gateway
curl https://api.palermo-cart.com/api/payments/health
```

#### Monitoring Checks

- [ ] Error rates within normal range
- [ ] Response times acceptable
- [ ] Database connections stable
- [ ] No critical alerts
- [ ] User traffic normal

### Step 10: Communication

1. **Internal Notification**
   - Post in #releases Slack channel
   - Email to stakeholders
   - Update status page

2. **External Communication**
   - Release notes published
   - Customer notification (if needed)
   - Social media announcement (for major releases)

### Step 11: Cleanup

```bash
# Delete release branch (after successful deployment)
git branch -d release.1.1
git push origin --delete release.1.1

# Keep tag for reference
# DO NOT delete tags
```

## Rollback Procedure

If critical issues are discovered post-deployment:

### Step 1: Decision to Rollback

Triggers:
- Critical functionality broken
- Data corruption detected
- Security vulnerability exposed
- Error rate > 5%
- Response time > 3x normal

### Step 2: Execute Rollback

```bash
# 1. Revert to previous tag
git switch main
git reset --hard v1.0.0

# 2. Deploy previous version
# Follow deployment steps with previous version

# 3. Rollback database if needed
psql -d palermo_cart_prod -f rollbacks/xxx_rollback.sql

# 4. Verify rollback
curl https://api.palermo-cart.com/version
# Should show v1.0.0
```

### Step 3: Post-Rollback

1. Create incident report
2. Analyze root cause
3. Fix issues in development
4. Plan hotfix or next release

## Hotfix Process

For critical production issues between regular releases:

### Step 1: Create Hotfix

```bash
# Create hotfix branch from main
git switch main
git pull origin main
git switch -c hotfix-payment-critical

# Fix the issue
# ... make changes ...

# Commit
git add .
git commit -m "fix: resolve critical payment processing issue"

# Push
git push origin hotfix-payment-critical
```

### Step 2: Expedited Review

- Immediate review by available team members
- Minimum 2 approvals
- Skip non-critical checks if necessary

### Step 3: Deploy Hotfix

```bash
# Tag with patch version
git tag -a v1.1.1 -m "Hotfix: Payment processing critical fix"
git push origin v1.1.1

# Deploy immediately
# Follow standard deployment process
```

### Step 4: Backport to Other Branches

```bash
# Backport to staging
git switch staging
git cherry-pick <hotfix-commit-hash>
git push origin staging

# Backport to qa
git switch qa
git cherry-pick <hotfix-commit-hash>
git push origin qa

# Backport to dev
git switch dev
git cherry-pick <hotfix-commit-hash>
git push origin dev
```

## Release Calendar

### Weekly Schedule

- **Monday**: Sprint planning, begin development
- **Tuesday**: Development continues
- **Wednesday**: Mid-sprint review, begin QA
- **Thursday**: Code freeze 5:00 PM, final QA
- **Friday**: Release deployment 2:00-4:00 PM

### Release Windows

- **Production Release**: Fridays 2:00-4:00 PM
- **Hotfix Deployment**: Anytime (with approval)
- **No-Deploy Periods**: Weekends, holidays

## Version Numbering

### Semantic Versioning

Format: `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes, major features
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, minor changes

Examples:
- `1.0.0` → Initial release
- `1.1.0` → New features added
- `1.1.1` → Bug fixes
- `2.0.0` → Breaking changes

## Documentation Requirements

After each release:

1. Update API documentation
2. Update user guides
3. Update architecture diagrams (if needed)
4. Create/update ADRs for major decisions
5. Update this release process (if changed)

## Metrics and Reporting

Track for each release:
- Number of HUs included
- Deployment time
- Number of issues found post-release
- Rollback occurrences
- Time to fix critical issues

## Contact Information

- **Release Manager**: TBD
- **DevOps Lead**: TBD
- **On-Call Engineer**: Check rotation schedule
- **Emergency Contact**: TBD

## References

- [Branching Policy](./branching-policy.md)
- [CI/CD Pipeline](./ci-cd-pipeline.md)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
