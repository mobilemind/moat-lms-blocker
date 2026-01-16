# Branch Protection and GitHub Actions Configuration

## Overview

This document outlines the recommended branch protection rules and GitHub Actions configuration for the moat-lms-blocker repository to ensure code quality and security.

## Branch Protection Rules for `main` branch

### Recommended Settings:

#### 1. Require Pull Request Reviews
- **Require approvals**: 1 approval minimum
- **Dismiss stale reviews**: Yes (when new commits are pushed)
- **Require review from Code Owners**: Optional (if CODEOWNERS is used)

#### 2. Require Status Checks to Pass
**Enable**: Require status checks to pass before merging

**Required status checks** (all must pass):
- `Lint filter list files` (from lint-filters.yml)
- `Lint JSON files` (from lint-json.yml)
- `Lint Markdown files` (from lint-markdown.yml)
- `Lint YAML files` (from lint-yaml.yml)

**Additional settings**:
- ✅ Require branches to be up to date before merging
- ✅ Do NOT require approval to run workflows on PRs from contributors
  - This allows checks to run automatically without maintainer approval
  - Workflows already have minimal permissions (`contents: read`)
  - No write access or secrets are exposed

#### 3. Require Conversation Resolution
- **Enabled**: Require all conversations to be resolved before merging

#### 4. Require Signed Commits (Optional but Recommended)
- **Enabled**: Require commits to be signed
- Enhances security by verifying commit authenticity

#### 5. Require Linear History
- **Enabled**: Prevent merge commits
- Keeps history clean with rebase/squash merges

#### 6. Include Administrators
- **Enabled**: Apply rules to administrators
- Ensures consistency and prevents accidental bypasses

#### 7. Restrict Pushes
- **Allow force pushes**: No
- **Allow deletions**: No

## GitHub Actions Security Configuration

### Current Security Measures

All workflows already implement security best practices:

1. **Minimal Permissions**: All jobs use `permissions: contents: read`
2. **Pinned Actions**: Using specific versions (v6) instead of mutable tags
3. **Trigger Configuration**: 
   - Runs on `push` to `main` branch
   - Runs on `pull_request` to `main` branch
   - Uses `pull_request` (not `pull_request_target`) to avoid exposing secrets

### Why `pull_request` is Safe

The workflows use `pull_request` trigger which:
- ✅ Runs in the context of the PR branch (not main)
- ✅ Does not have access to repository secrets
- ✅ Only has `contents: read` permission
- ✅ Cannot modify the repository
- ✅ Safe to run without approval for external contributors

Using `pull_request` instead of `pull_request_target` means:
- Checks run automatically on all PRs
- No maintainer approval needed to run checks
- Malicious PRs cannot access secrets or write to the repo
- Results are still reported back to the PR

## npm Security Configuration

### .npmrc Settings

The repository uses a `.npmrc` file with security-focused settings:

- `engine-strict=true` - Enforces Node.js/npm version requirements
- `save-exact=true` - Saves exact dependency versions
- `package-lock=true` - Requires package-lock.json
- `audit=true` - Automatically audits dependencies
- `audit-level=moderate` - Blocks moderate+ vulnerabilities
- `legacy-peer-deps=false` - Strict dependency resolution

### package.json Settings

- `"private": true` - Prevents accidental publishing to npm
- Strict engine requirements: Node.js >=24.12.0, npm >=11.6.0
- All dependencies are devDependencies (no runtime dependencies)

## Dependabot Configuration

Current configuration in `.github/dependabot.yml`:
- Weekly updates for both GitHub Actions and npm
- Limited to 5 open PRs per ecosystem
- Helps keep dependencies secure and up-to-date

## Implementation Steps

### Step 1: Configure Branch Protection (GitHub UI)

1. Go to repository Settings → Branches
2. Add branch protection rule for `main`
3. Configure settings as outlined above
4. Pay special attention to:
   - ✅ Enable "Require status checks to pass before merging"
   - ✅ Select all 4 lint workflow checks as required
   - ❌ Do NOT enable "Require approval to run workflows"

### Step 2: Verify Workflow Security

All workflows already have proper security:
- ✅ Minimal permissions set
- ✅ Using `pull_request` trigger (safe for external contributors)
- ✅ No secrets exposed
- ✅ Pinned action versions

### Step 3: Test Configuration

1. Create a test PR with changes
2. Verify all 4 lint checks run automatically (no approval needed)
3. Verify PR cannot be merged until checks pass
4. Verify PR cannot be merged without approval (if required)

## Why This Configuration is Secure

1. **Automatic Checks**: All PRs are automatically checked without requiring maintainer approval
2. **No Secret Exposure**: Workflows use `pull_request` with read-only permissions
3. **Mandatory Reviews**: At least one approval required before merge
4. **Status Check Gates**: Code must pass all lints before merge
5. **Locked Dependencies**: Exact versions with regular Dependabot updates
6. **Engine Enforcement**: Ensures consistent Node.js/npm environment
7. **Private Package**: Cannot accidentally publish to npm registry

## Additional Recommendations

### Consider Adding:

1. **CodeQL Analysis** (GitHub Advanced Security)
   - Automated security scanning
   - Detects common vulnerabilities
   - Free for public repositories

2. **Dependency Review Action**
   - Reviews dependency changes in PRs
   - Alerts on new vulnerabilities
   - Example: `actions/dependency-review-action`

3. **Renovate or Mend Bolt**
   - More advanced dependency management
   - Grouped updates for related dependencies
   - Custom scheduling and automerge rules

4. **Commit Signing Enforcement**
   - Requires GPG/SSH signed commits
   - Verifies commit authenticity
   - Prevents commit impersonation

## Maintenance

- Review branch protection rules quarterly
- Update action versions when security updates are released
- Monitor Dependabot PRs and merge promptly
- Audit dependencies regularly with `npm audit`

## Resources

- [GitHub Branch Protection Rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
- [GitHub Actions Security Hardening](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
- [npm Security Best Practices](https://docs.npmjs.com/security-best-practices)
- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
