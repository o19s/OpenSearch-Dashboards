# Running CI on a Fork

This document explains how to run the OpenSearch-Dashboards GitHub Actions workflows on your fork, so you can verify tests pass before opening PRs to the upstream repository.

## Enabling GitHub Actions on Your Fork

1. **Go to your fork** on GitHub (e.g., `your-org/OpenSearch-Dashboards`).

2. **Enable Actions** (if not already enabled):
   - Settings → Actions → General
   - Under "Actions permissions", select **"Allow all actions and reusable workflows"**
   - Save

3. **Workflows run automatically** when you:
   - Push to any branch
   - Open a pull request (to any branch, including your fork's `main`)

## Workflows That Run on Forks

The following workflows run on forks without any additional configuration:

| Workflow | Trigger | Purpose |
|----------|----------|---------|
| **build_and_test_workflow** | Push to main/version branches, all PRs | Unit tests, lint, typecheck, functional tests, build artifacts |
| **cypress_workflow** | All PRs | Cypress E2E tests |
| **osart_ci** | PRs touching `plugins/osart/**` | OSART plugin Cypress tests |
| **dependency_review** | All PRs | Dependency vulnerability scan |
| **links_checker** | Push/PR to main | Link validation |
| **lighthouse_testing** | PRs to main | Performance testing |
| **add-untriaged** | Issue opened | Label management |
| **delete_backport_branch** | Backport PR closed | Cleanup |
| **github-workflow-badger** | PR opened | Contributor badges |

## Workflows That Skip on Forks

These workflows require upstream-only secrets or infrastructure. They automatically skip when `github.repository != 'opensearch-project/OpenSearch-Dashboards'`:

| Workflow | Reason |
|----------|--------|
| **pr_review** | Requires BEDROCK_ACCESS_ROLE (AI code review) |
| **version-increment** | Upstream release automation |
| **cypress_workflow_with_s3** | Requires S3 connection secrets |
| **opensearch_changelog_workflow** | Requires changelog bridge API secrets |
| **backport** | Requires opensearch-trigger-bot GitHub App |
| **create_doc_issue** | Requires opensearch-trigger-bot GitHub App |

## Optional: Code Coverage Upload

The build workflow uploads coverage to Codecov when `CODECOV_TOKEN` is set. To enable:

1. Sign up at [codecov.io](https://codecov.io) and add your fork
2. Copy your repo token
3. In your fork: Settings → Secrets and variables → Actions
4. Add secret: `CODECOV_TOKEN` = your token

If the secret is not set, the upload step is skipped and the build still passes.

## Testing Before Upstream PR

1. Push your branch to your fork
2. Open a PR from your branch → your fork's `main` (or create a `test` branch)
3. GitHub Actions will run the same build_and_test_workflow, cypress_workflow, etc. that upstream uses
4. Fix any failures before opening a PR to `opensearch-project/OpenSearch-Dashboards`

This avoids waiting for upstream maintainer approval of first-time contributor workflows, since your fork's workflows run immediately.
