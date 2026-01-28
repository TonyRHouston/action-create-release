# Package research and implementation plan

## Project goals alignment
This action focuses on reliably creating GitHub releases and auto-incrementing tags. Package upgrades should prioritize:

- Accurate tag discovery and version handling for auto-incrementing releases.
- Compatibility with current GitHub Actions runtime environments.
- Security and maintenance of dependencies used by the action and bundled build.

## Packages to research and why

### 1) `@actions/github`
- **Reason**: The code uses Octokit APIs for releases and tags; newer versions may improve pagination, typings, and API compatibility.
- **Research checklist**:
  - Compare current usage (`GitHub` constructor, `repos.createRelease`, `git.listMatchingRefs`) with the latest API surface.
  - Verify whether `context` and the GitHub client require new import paths or token configuration changes.
  - Confirm pagination helpers remain available or have replacement APIs.
- **Implementation plan**:
  1. Add compatibility layer if the new client uses `getOctokit` instead of `new GitHub()`.
  2. Update tag lookup to use the recommended pagination API.
  3. Validate release creation payloads against updated REST endpoints.
  4. Regenerate the bundled `dist/index.js` output and update tests.

### 2) `@actions/core`
- **Reason**: Inputs/outputs and error handling are critical; newer versions add features (like grouped logging).
- **Research checklist**:
  - Confirm input parsing behavior for booleans and empty strings.
  - Review output format changes or deprecations.
- **Implementation plan**:
  1. Update dependency and run unit tests.
  2. Validate output behavior with existing tests (e.g., `current_tag`, `previous_tag`).

### 3) `semver`
- **Reason**: Version parsing and comparison must be stable for tag auto-increment.
- **Research checklist**:
  - Confirm changes in coercion and prerelease parsing behavior.
  - Note any breaking changes in `semver.inc` behavior.
- **Implementation plan**:
  1. Update dependency and run tag-related tests.
  2. Add regression tests for prerelease handling if required.

### 4) Tooling updates (`eslint`, `jest`, `prettier`, `@zeit/ncc`)
- **Reason**: Tooling is outdated; modern versions improve reliability and compatibility with newer Node versions.
- **Research checklist**:
  - Determine minimum Node version required by the repo and Actions runtime.
  - Evaluate the migration path for `@zeit/ncc` to `@vercel/ncc`.
- **Implementation plan**:
  1. Upgrade tooling in a separate branch to reduce risk.
  2. Update config where required (ESLint config migration, Jest settings).
  3. Verify `npm test` and `npm run build` output.

## Proposed milestones

1. **Dependency audit**: Review current versions and document recommended upgrades.
2. **Octokit migration**: Update `@actions/github` usage to align with the latest API.
3. **Semver regression suite**: Add additional tests for tag sorting and prerelease bumps.
4. **Tooling refresh**: Modernize build and test tooling while keeping existing behavior.

## Implementation notes
- Keep changes grouped by dependency area to simplify review and rollback.
- Maintain compatibility with existing inputs and outputs as documented in `README.md`.
- Always rebuild `dist/index.js` after code changes affecting runtime behavior.
