## Engineering Practices

This repository documents practical engineering conventions for day-to-day development work. It should stay short, current, and easy to apply.

### Core Principles

1. `main` should always be in a deployable state.
2. Changes should be small, reviewable, and easy to roll back.
3. CI is required for shared branches and pull requests.
4. Test-driven development is preferred especially when behavior is unclear, risky, or being implemented with AI assistance.
5. Incomplete work should be hidden behind feature flags, not long-lived branches.
6. Automated tests should exist at the right level for the risk: unit, integration, and end-to-end where appropriate.

-----

### Preferred Branching Model

Use a trunk-based workflow by default.

| Branch | Purpose | Rules |
| :--- | :--- | :--- |
| `main` | Source of truth. Always production-ready. | Protected. Requires passing CI and review before merge. |
| `release/<version>` | Optional short-lived stabilization branch for a scheduled release. | Created only when needed. Deleted after release. |
| `feat/<name>` | New user-facing functionality. | Short-lived. Rebased or merged frequently from `main`. |
| `fix/<name>` | Bug fix that is not an emergency. | Short-lived. Must include tests when practical. |
| `hotfix/<name>` | Urgent production repair. | Branch from `main`, fast review, merge back quickly. |
| `docs/<name>` | Documentation-only changes. | Keep small and easy to review. |
| `chore/<name>` | Tooling, maintenance, dependency, or config changes. | Prefer isolated, low-risk changes. |
| `refactor/<name>` | Structural code improvement with no intended behavior change. | Should be supported by tests. |

Do not keep a permanent `develop` branch unless the team has a concrete release-management reason for it. For most teams, a permanently deployable `main` branch is simpler and more reliable.

-----

### Branch Naming

Prefer concise, descriptive branch names:

- `feat/user-auth`
- `fix/header-overflow`
- `chore/update-eslint`
- `docs/api-pagination`
- `refactor/payment-service`
- `hotfix/login-outage`

Avoid generic names such as `test`, `my-branch`, `updates`, or user-specific sandbox branches unless there is a clear temporary need.

-----

### Daily Workflow

1. Start from the latest `main`.
   ```shell
   git switch main
   git pull
   git switch -c feat/my-change
   ```
2. Make small commits with clear intent.
3. Rebase or merge from `main` regularly if the branch lives longer than a day or two.
4. Run relevant tests locally before opening a pull request.
5. Open a PR early if feedback is useful, but do not merge until required checks pass.
6. Merge to `main` only when the change is reviewable, tested, and safe to deploy.

Prefer short-lived branches. If work is not ready for users but needs to merge, use feature flags, configuration gates, or dark launches.

-----

### Test-Driven Development

TDD is not mandatory for every change, but it is the preferred default when:

1. The expected behavior can be stated clearly before implementation.
2. The change affects business rules, parsing, state transitions, or bug fixes.
3. AI is being used to generate or refactor code and you want fast validation of correctness.

Recommended loop:

1. Write or update a failing test that captures the intended behavior.
2. Implement the smallest change needed to make the test pass.
3. Refactor while keeping the test suite green.

Why this matters for AI-assisted coding:

1. Tests constrain the solution space and reduce vague prompts.
2. Failing tests make regressions visible immediately.
3. Passing tests provide a concrete acceptance check for generated code.
4. Small test-first steps reduce the risk of large, plausible-looking but incorrect changes.

Do not force TDD where it adds little value, such as trivial presentation changes or exploratory prototyping. But when the cost of wrong behavior is meaningful, a test-first workflow is usually the safest and fastest path.

-----

### Pull Request Standards

Every pull request should:

1. Be scoped to one logical change.
2. Explain the user or system impact.
3. Include tests or explain why tests were not added.
4. Pass all required CI checks.
5. Be small enough for a reviewer to understand without excessive context switching.

Reviewers should focus on correctness, risk, maintainability, and test coverage, not only style.

-----

### Deployment Model

Branch names and environments should not be conflated.

Use this separation instead:

| Concept | Example |
| :--- | :--- |
| Branch | `main` |
| Release branch | `release/2026-03` or `release/1.4.0` |
| Tag | `v1.4.0` |
| Environment | `dev`, `staging`, `prod` |

Recommended flow:

1. Merge reviewed changes into `main`.
2. Deploy `main` automatically to a non-production environment if useful.
3. Validate in `staging`.
4. Tag the release commit as `vX.Y.Z`.
5. Deploy that tagged commit to production.

This keeps release history immutable and makes rollback much clearer.

-----

### CI/CD Expectations

CI should be mandatory for pull requests and protected branches.

Minimum expectations:

1. Install dependencies in a reproducible way.
2. Run linting, type checks, and automated tests.
3. Build the application if it produces deployable artifacts.
4. Block merges when required checks fail.
5. Deploy from a known commit, ideally the same one that was validated in CI.

Example GitHub Actions workflow:

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm run lint --if-present
      - run: npm run typecheck --if-present
      - run: npm test --if-present
      - run: npm run build --if-present
```

Example release workflow:

```yaml
name: Deploy

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy tagged release
        run: ./scripts/deploy-production.sh
```

-----

### Testing Guidance

Testing strategy should match risk and feedback speed.

| Test Type | Best For |
| :--- | :--- |
| Unit tests | Pure logic, small components, edge cases |
| Integration tests | Database, API, queue, filesystem, and service boundaries |
| End-to-end tests | Critical user journeys and release confidence |

Do not optimize for unit-test count alone. Optimize for confidence per minute of maintenance cost.

When practical, start with the test before the implementation. This is especially useful for AI-assisted code generation, where tests act as an executable specification and a regression boundary.

-----

### Communication Rules

1. Shared branches must stay healthy. If `main` breaks, fixing it becomes the top priority.
2. Raise blockers early instead of waiting until a deadline.
3. Prefer written decisions in PRs, issues, or design notes so tradeoffs are visible later.
4. When behavior changes, document the operational impact, migration needs, and rollback path.

-----

### General Engineering Standards

1. Write code that is easy to read, change, and delete.
2. Prefer explicit names over clever abstractions.
3. Keep dependencies current, but upgrade deliberately.
4. Use formatting and linting tools to automate consistency.
5. Treat observability as part of the feature: logs, metrics, traces, alerts.
6. Design for failure: retries, timeouts, idempotency, and rollback should be intentional.
7. Default to secure choices for secrets, permissions, and data handling.
