# Sentry Development Guide for AI Agents

> **IMPORTANT**: AGENTS.md files are the source of truth for AI agent instructions. Always update the relevant AGENTS.md file when adding or modifying agent guidance. Do not add to CLAUDE.md or Cursor rules.

## Overview

Sentry is a developer-first error tracking and performance monitoring platform. This repository contains the main Sentry application, which is a large-scale Django application with a React frontend.

## Project Structure

```
sentry/
├── src/
│   ├── sentry/           # Main Django application
│   │   ├── api/          # REST API endpoints
│   │   ├── models/       # Django models
│   │   ├── tasks/        # Celery tasks
│   │   ├── integrations/ # Third-party integrations
│   │   ├── issues/       # Issue tracking logic
│   │   └── web/          # Web views and middleware
│   ├── sentry_plugins/   # Plugin system
│   └── social_auth/      # Social authentication
├── static/               # Frontend application
├── tests/                # Backend test suite
├── fixtures/             # Test fixtures
├── devenv/               # Development environment config
├── migrations/           # Database migrations
└── config/               # Configuration files
```

> For detailed development patterns, see nested AGENTS.md files:
>
> - **Backend patterns**: `src/AGENTS.md`
> - **Backend testing patterns**: `tests/AGENTS.md`
> - **Frontend patterns**: `static/AGENTS.md`

### Context-Aware Loading

Cursor is configured to automatically load relevant AGENTS.md files based on the file being edited (via `.cursor/rules/*.mdc`). This provides context-specific guidance without token bloat:

- Editing `src/**/*.py` → Loads `src/AGENTS.md` (backend patterns)
- Editing `tests/**/*.py` → Loads `tests/AGENTS.md` (testing patterns)
- Editing `static/**/*.{ts,tsx,js,jsx}` → Loads `static/AGENTS.md` (frontend patterns)
- Always loads this file (`AGENTS.md`) for general Sentry context

**Note**: These `.mdc` files only _reference_ AGENTS.md files—they don't duplicate content. All actual guidance should be added to the appropriate AGENTS.md file, not to Cursor rules.

## Backend

For backend development patterns, commands, security guidelines, and architecture, see `src/AGENTS.md`.
For backend testing patterns and best practices, see `tests/AGENTS.md`.

## Frontend

For frontend development patterns, commands, design system guidelines, and React testing best practices, see `static/AGENTS.md`.

## Kencove Fork

This is **kencove/sentry** - a fork of getsentry/sentry with custom modifications for our self-hosted deployment.

### Key Modifications

1. **GitLab Autofix Support** (`static/app/components/events/autofix/utils.tsx`)
   - Added `'gitlab'` and `'integrations:gitlab'` to `supportedProviders` array
   - Enables GitLab repositories for Seer Autofix feature

### Building Custom Image

Build and push to Google Artifact Registry:

```bash
# Using Cloud Build (recommended)
./build-and-push.sh v26.1.0-gitlab

# Local build only (no push)
./build-and-push.sh --local

# Using gcloud directly
gcloud builds submit --config=cloudbuild.yaml .
```

Image location: `us-central1-docker.pkg.dev/kencove-prod/kencove-docker-repo/sentry`

### Syncing with Upstream

```bash
# Add upstream remote
git remote add upstream https://github.com/getsentry/sentry.git

# Fetch and merge specific release
git fetch upstream
git checkout master
git merge upstream/releases/26.1.0

# Re-apply Kencove changes if needed
# (GitLab support in static/app/components/events/autofix/utils.tsx)
```

### Related Repositories

- **Seer AI Service**: [kencove/seer](https://github.com/kencove/seer) - GitLab repository client
- **Helm Charts**: [kencove/charts](https://github.com/kencove/charts) - Deployment configuration
- **Infra Clusters**: `~/projects/infra/clusters/helm/sentry/` - Deployment values
