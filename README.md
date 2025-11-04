# Renovate Test Repository

This repository is used to test Renovate bot configuration before deploying to production repositories.

**IMPORTANT**: This uses the exact same dependencies as `@bucketeer/js-client-sdk` but with intentionally older versions.

## Setup

1. Push this repository to GitHub
2. Install the Renovate app: https://github.com/apps/renovate
3. Grant access to this repository
4. Renovate will create an onboarding PR

## What to expect

Renovate should create:
- An onboarding PR explaining what it will do
- A Dependency Dashboard issue listing all available updates
- Grouped PRs for updates based on the configuration

## GitHub Actions Testing

Workflows with intentionally old action versions:
- `actions/checkout@v3` (current: v4)
- `actions/setup-node@v3` (current: v4)
- `pnpm/action-setup@v3.0.0` (current: v4.x)
- `googleapis/release-please-action@v3` (current: v4.x)

**Expected:** One grouped PR: `ci(deps): bump ci-dependencies group`

## Expected Behavior

### Blocked Updates (should NOT create PRs):
- **typescript**: 5.3.0 → 5.9.x (minor/major blocked, only patch allowed)
- **eslint**: 9.0.0 → 9.32.x (minor/major blocked, only patch allowed)
- **prettier**: 3.0.0 → 3.6.x (minor/major blocked, only patch allowed)
- **@types/node**: 20.0.0 → 22.x (minor/major blocked, only patch allowed)
- **async-mutex**: 0.4.0 → 0.5.x (minor/major blocked, only patch allowed)
- **unbuild**: 2.0.0 → 3.x (minor/major blocked, only patch allowed)
- **Test deps major updates**: vitest 2.x → 3.x (major blocked)
- **GitHub Actions major updates**: actions/checkout v3 → v4 (major blocked)

### Grouped PRs Expected:

1. **ci-dependencies group** (GitHub Actions patch + minor)
   - actions/checkout: v3 → v4 (minor)
   - actions/setup-node: v3 → v4 (minor)
   - pnpm/action-setup: v3.0.0 → v4.x (major blocked, will only do patch/minor within v3)
   - googleapis/release-please-action: v3 → v4 (major blocked, will only do patch/minor within v3)

2. **test-dependencies group** (patch + minor)
   - vitest: 2.0.0 → 2.x.x (patch updates within 2.x)
   - @vitest/browser: 2.0.0 → 2.x.x
   - @vitest/utils: 2.0.0 → 2.x.x
   - webdriverio: 8.0.0 → 9.x (minor)
   - happy-dom: 14.0.0 → 20.x (minor)
   - msw: 2.0.0 → 2.x.x (patch/minor within 2.x)
   - @types/jsdom: 21.0.0 → 27.x (minor)

3. **build-minor group** (minor updates for build deps)
   - @typescript-eslint/eslint-plugin: 7.0.0 → 8.x
   - @typescript-eslint/parser: 7.0.0 → 8.x
   - typescript-eslint: 7.0.0 → 8.x
   - eslint-config-prettier: 9.0.0 → 10.x
   - eslint-plugin-prettier: 5.0.0 → 5.x
   - eslint-plugin-import: 2.29.0 → 2.32.x
   - jiti: 1.21.0 → 2.x

4. **build-patch group** (patch updates for build deps)
   - Any patch-level updates for packages not in other groups

## Testing checklist

- [ ] Onboarding PR created
- [ ] Dependency Dashboard issue created
- [ ] Test dependencies grouped in ONE PR (not separate PRs)
- [ ] TypeScript ESLint packages grouped in build-minor (not separate!)
- [ ] Major updates correctly blocked for critical packages
- [ ] No individual PRs for typescript, eslint, prettier (updates blocked)
- [ ] Auto-merge works for grouped PRs
- [ ] No conflicts between grouped PRs
- [ ] Auto-rebase works when PRs are merged

## Key Test: TypeScript ESLint Grouping

**This is the critical test**: 
- `@typescript-eslint/eslint-plugin` 7.0.0 → 8.46.3
- `@typescript-eslint/parser` 7.0.0 → 8.46.3  
- `typescript-eslint` 7.0.0 → 8.41.0

These should all be in **ONE grouped PR** called "build-minor", NOT three separate PRs!

If Renovate creates separate PRs for these, it has the same grouping issue as Dependabot.

## Renovate config highlights

- **Automerge**: Enabled for patch and minor updates
- **Grouping**: Dependencies grouped by type (test deps, build deps, etc.)
- **Ignored updates**: Major/minor updates blocked for critical packages
- **Labels**: All PRs labeled with "dependencies"
- **Same config as javascript-client-sdk**: Direct comparison to Dependabot
