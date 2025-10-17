# Getting Started - Palermo Cart Project

## Welcome

Welcome to the Palermo Cart project! This guide will help you get set up and familiar with our polyrepo architecture and development workflow.

## Project Overview

Palermo Cart is a shopping cart application built using a polyrepo architecture with the following repositories:

- **Palermo-cart-docs**: Documentation and diagrams
- **Palermo-cart-api**: Backend API (Golang)
- **Palermo-cart-portal**: Web frontend (React/Vue/Angular)
- **Palermo-cart-app**: Mobile application (Flutter/React Native)
- **Palermo-cart-db**: PostgreSQL database schema

## Prerequisites

Before you begin, ensure you have the following installed:

### Required Tools

- **Git**: Version control
  ```bash
  git --version
  # Should be 2.30.0 or higher
  ```

- **GitHub/GitLab Account**: For accessing repositories

### Backend Development (Palermo-cart-api)

- **Go**: Version 1.21 or higher
  ```bash
  go version
  ```

- **Docker**: For containerization
  ```bash
  docker --version
  ```

### Frontend Development (Palermo-cart-portal)

- **Node.js**: Version 18 or higher
  ```bash
  node --version
  ```

- **npm/yarn**: Package manager
  ```bash
  npm --version
  # or
  yarn --version
  ```

### Mobile Development (Palermo-cart-app)

#### Flutter
- **Flutter SDK**: Latest stable version
  ```bash
  flutter --version
  ```

- **Android Studio**: For Android development
- **Xcode**: For iOS development (macOS only)

#### React Native
- **Node.js**: Version 18 or higher
- **React Native CLI**:
  ```bash
  npm install -g react-native-cli
  ```

### Database (Palermo-cart-db)

- **PostgreSQL**: Version 14 or higher
  ```bash
  psql --version
  ```

## Initial Setup

### Step 1: Clone Repositories

Create a workspace directory and clone all repositories:

```bash
# Create workspace
mkdir palermo-cart-workspace
cd palermo-cart-workspace

# Clone all repositories
git clone <url-to-Palermo-cart-docs>
git clone <url-to-Palermo-cart-api>
git clone <url-to-Palermo-cart-portal>
git clone <url-to-Palermo-cart-app>
git clone <url-to-Palermo-cart-db>
```

Your directory structure should look like:
```
palermo-cart-workspace/
├── Palermo-cart-docs/
├── Palermo-cart-api/
├── Palermo-cart-portal/
├── Palermo-cart-app/
└── Palermo-cart-db/
```

### Step 2: Set Up Git Configuration

Configure your Git identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Set up useful aliases:

```bash
# Quick status
git config --global alias.st status

# Pretty log
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# Quick branch list
git config --global alias.br "branch -v"
```

### Step 3: Understand Branch Structure

Each repository has four main branches:

```bash
# List all branches
git branch -a

# You should see:
# * main
#   dev
#   qa
#   staging
```

### Step 4: Set Up Database

```bash
cd Palermo-cart-db

# Create database
createdb palermo_cart_dev

# Run initial migrations
psql -d palermo_cart_dev -f migrations/001_initial_schema.sql

# Load seed data
psql -d palermo_cart_dev -f seeds/dev_data.sql

# Verify setup
psql -d palermo_cart_dev -c "\dt"
```

### Step 5: Set Up API

```bash
cd ../Palermo-cart-api

# Install dependencies
go mod download

# Copy environment template
cp .env.example .env

# Edit .env with your settings
# Set database connection, API keys, etc.

# Run tests
go test ./...

# Start API server
go run main.go
```

Verify API is running:
```bash
curl http://localhost:8080/health
# Should return: {"status":"ok"}
```

### Step 6: Set Up Web Portal

```bash
cd ../Palermo-cart-portal

# Install dependencies
npm install
# or
yarn install

# Copy environment template
cp .env.example .env

# Edit .env with API URL
# REACT_APP_API_URL=http://localhost:8080

# Start development server
npm run dev
# or
yarn dev
```

Open browser to http://localhost:3000

### Step 7: Set Up Mobile App (Optional)

#### Flutter Setup

```bash
cd ../Palermo-cart-app

# Get dependencies
flutter pub get

# Check setup
flutter doctor

# Run on emulator/device
flutter run
```

#### React Native Setup

```bash
cd ../Palermo-cart-app

# Install dependencies
npm install
# or
yarn install

# Run on iOS (macOS only)
npm run ios

# Run on Android
npm run android
```

## Daily Development Workflow

### Starting Your Day

```bash
# 1. Navigate to your working repository
cd palermo-cart-workspace/Palermo-cart-api

# 2. Switch to dev branch
git switch dev

# 3. Pull latest changes
git pull origin dev

# 4. Check if there are updates in other repos
cd ../Palermo-cart-db
git switch dev
git pull origin dev

cd ../Palermo-cart-portal
git switch dev
git pull origin dev
```

### Working on a User Story

```bash
# 1. Create your feature branch from dev
git switch dev
git pull origin dev
git switch -c HU-15-dev

# 2. Make your changes
# ... code ...

# 3. Commit frequently with clear messages
git add .
git commit -m "feat(auth): add email validation

Implement email format validation for user registration.
Includes regex pattern and error messages.

Related to HU-15"

# 4. Push your branch
git push origin HU-15-dev

# 5. Create Merge Request on GitHub/GitLab
# Navigate to repository and create MR: HU-15-dev → dev
```

### Keeping Your Branch Up to Date

```bash
# Sync with latest dev changes
git switch HU-15-dev
git pull origin dev

# Resolve any conflicts
# ... fix conflicts ...

git add .
git commit -m "chore: resolve merge conflicts"
git push origin HU-15-dev
```

### Testing Your Changes

```bash
# Backend tests
cd Palermo-cart-api
go test ./...

# Frontend tests
cd Palermo-cart-portal
npm test
# or
yarn test

# Mobile tests
cd Palermo-cart-app
flutter test
# or
npm test
```

## Common Tasks

### Creating a New Feature

1. **Create branch from dev**:
   ```bash
   git switch dev
   git pull origin dev
   git switch -c HU-XX-dev
   ```

2. **Implement feature**

3. **Write tests**

4. **Update documentation** (in Palermo-cart-docs if needed)

5. **Commit and push**:
   ```bash
   git add .
   git commit -m "feat: description"
   git push origin HU-XX-dev
   ```

6. **Create Merge Request**

### Fixing a Bug

1. **Create branch from appropriate environment**:
   ```bash
   # For dev environment bug
   git switch dev
   git pull origin dev
   git switch -c HU-XX-dev
   ```

2. **Fix the bug**

3. **Add test to prevent regression**

4. **Commit and push**:
   ```bash
   git add .
   git commit -m "fix: description of bug fix"
   git push origin HU-XX-dev
   ```

5. **Create Merge Request**

### Updating Documentation

```bash
# 1. Go to docs repository
cd Palermo-cart-docs

# 2. Create branch
git switch dev
git pull origin dev
git switch -c HU-XX-dev

# 3. Update documentation
# ... edit markdown files ...

# 4. Commit and push
git add .
git commit -m "docs: update API authentication guide"
git push origin HU-XX-dev

# 5. Create Merge Request
```

### Database Changes

```bash
# 1. Go to database repository
cd Palermo-cart-db

# 2. Create branch
git switch dev
git pull origin dev
git switch -c HU-XX-dev

# 3. Create migration file
# Create: migrations/XXX_description.sql
# Create: rollbacks/XXX_rollback.sql

# 4. Test migration locally
psql -d palermo_cart_dev -f migrations/XXX_description.sql

# 5. Test rollback
psql -d palermo_cart_dev -f rollbacks/XXX_rollback.sql

# 6. Commit and push
git add .
git commit -m "feat(db): add user_preferences table"
git push origin HU-XX-dev

# 7. Create Merge Request
```

## Development Best Practices

### Code Quality

1. **Follow project coding standards**
2. **Write meaningful commit messages**
3. **Keep commits small and focused**
4. **Write tests for new code**
5. **Update documentation**

### Git Hygiene

1. **Never commit directly to main branches**
2. **Pull before you push**
3. **Keep branches short-lived** (2-3 days max)
4. **Delete branches after merging**
5. **Use descriptive branch names**

### Communication

1. **Update progress in daily standups**
2. **Ask for help when stuck**
3. **Review others' merge requests**
4. **Document decisions in ADRs** (Architecture Decision Records)

## Troubleshooting

### Common Issues

#### "fatal: remote origin already exists"

```bash
# Remove existing origin
git remote remove origin

# Add correct origin
git remote add origin <repository-url>
```

#### "Your branch is behind"

```bash
# Pull latest changes
git pull origin dev

# If you have local commits, rebase
git pull --rebase origin dev
```

#### "Merge conflict"

```bash
# 1. Open conflicted files
# 2. Look for conflict markers: <<<<<<<, =======, >>>>>>>
# 3. Resolve conflicts manually
# 4. Mark as resolved
git add <resolved-files>
git commit -m "chore: resolve merge conflicts"
```

#### Database Connection Issues

```bash
# Check PostgreSQL is running
pg_isready

# Check connection
psql -d palermo_cart_dev

# Reset database (WARNING: Deletes all data)
dropdb palermo_cart_dev
createdb palermo_cart_dev
psql -d palermo_cart_dev -f migrations/001_initial_schema.sql
```

#### API Won't Start

```bash
# Check port is not in use
lsof -i :8080

# Check environment variables
cat .env

# Check logs
go run main.go 2>&1 | tee api.log
```

## Getting Help

### Resources

- **Documentation**: [Palermo-cart-docs](../README.md)
- **Branching Policy**: [workflows/branching-policy.md](./branching-policy.md)
- **Release Process**: [workflows/release-process.md](./release-process.md)

### Contacts

- **Team Lead**: TBD
- **Tech Lead**: TBD
- **DevOps**: TBD

### Channels

- **Slack**: #palermo-cart-dev
- **Issues**: GitHub/GitLab Issues
- **Email**: team@example.com

## Next Steps

Now that you're set up:

1. **Read the branching policy**: [branching-policy.md](./branching-policy.md)
2. **Pick up your first User Story**: Check project board
3. **Join team standup**: Daily at 10:00 AM
4. **Set up IDE**: Install recommended extensions
5. **Review existing code**: Familiarize yourself with codebase

## Useful Commands Cheat Sheet

```bash
# Git shortcuts
git st                           # Status
git br                           # Branches
git lg                           # Pretty log
git switch <branch>              # Switch branch
git switch -c <new-branch>       # Create and switch

# Development
go test ./...                    # Run Go tests
npm test                         # Run JS tests
flutter test                     # Run Flutter tests
docker-compose up                # Start services

# Database
psql -d palermo_cart_dev         # Connect to DB
\dt                              # List tables
\q                               # Quit psql
```

Welcome aboard! Happy coding!
