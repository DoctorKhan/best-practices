## Best Practices & Workflow

Read this document often. It will be updated.

### Core Branching Philosophy: Protect the Source of Truth

The entire branching strategy is built on one core principle: **branches that are shared or deployed must always be stable and working.**

  * **Protecting `main` and `develop`:** Your `main` (production) and `develop` (integration) branches are the "sources of truth." Committing broken code to them blocks the entire team.
  * **Feature Branches as a "Sandbox":** Branches prefixed with `feature/` (e.g., `feature/login-page`) are your private "sandboxes." This is the *only* place where it is safe to have broken, half-finished code.
  * **The Rule:** The rule "only working code should be committed" applies *only* when merging into a shared branch (like `develop` or `main`). Your working branches are expected to be work-in-progress.

-----

### Priorities

1.  The production server should be up and running at all times (Protected `main (tagged)`).
2.  New versions are fully tested in the staging environment (`main` branch).
3.  The integration branch (`develop`) must remain Green (passing tests) so teammates can rely on it.
4.  You must push daily incremental commits to your local working branch (`feature/*`).
5.  Always write unit tests.

-----

### Branch Naming Conventions (Prefixes)

We use semantic prefixes to identify the purpose of a branch. Do not use random names.

| Prefix | Example | Type | Purpose |
| :--- | :--- | :--- | :--- |
| `feature/` | `feature/user-auth` | Red | A new functionality. Starts broken/WIP, must be Green before merging to `develop`. |
| `bugfix/` | `bugfix/header-logo` | Red | Fixing a non-critical bug found in `develop`. |
| `hotfix/` | `hotfix/db-crash` | Red | Urgent. Used to patch Production (`main`) immediately, skipping normal flow. |
| `users/` | `users/jdoe/sandbox` | Red | Optional. A pure experimental sandbox for a specific developer. |

-----

### Branch Structure

| Environment | Branch Name | Type | Purpose |
| :--- | :--- | :--- | :--- |
| **Work In Progress** | `feature/<name>` | Red | Develop specific tasks, tests, and fixes. This is your "sandbox." |
| **Development** | `develop` | Green | The shared integration branch. Accumulates features from all developers. |
| **Staging** | `main` | Red* | Live testing (Staging Environment). Red only during active deployment testing. |
| **Production** | `main (tagged)` | Green | Released to public. Tagged historical stable versions (e.g., `v1.0.0`). |

-----

### Steps for Deployment

1.  **Local Development:**
      * **Branch: `feature/<task-name>`**
      * **Purpose:** Individual development work.
      * **Action:**
          * Create a specific branch for your task from `develop`:
            ```shell
            git switch develop
            git pull
            git switch -c feature/my-new-task
            ```
          * Commit frequently and push to your feature branch.
          * **Syncing:** Merge `develop` into your branch frequently to resolve conflicts early:
            ```shell
            git merge develop
            ```
      * Review and test locally.
2.  **Feature Integration:**
      * **Branch: `develop`**
      * **Purpose:** Incorporate working features and perform local integration tests.
      * **Action:**
          * **Green Check:** Do not merge your branch into `develop` until you have ensured that all tests pass in your feature branch.
          * Create a Pull Request (PR) or merge `feature/my-new-task` into `develop`.
          * Once merged, the `develop` branch should auto-deploy to a Development/QA server (if available) or run CI tests.
3.  **Staging Deployment:**
      * **Branch: `main`**
      * **Purpose:** Live testing environment.
      * **Action:**
          * Create a PR from `develop` to `main`.
          * Merge the PR after approval.
          * Deploy `main` to the staging server.
          * Perform live testing.
4.  **Production Deployment:**
      * **Branch: `main (tagged)`**
      * **Purpose:** Release to the public.
      * **Action:**
          * Once `main` is stable in staging, create a tag (e.g., `v1.0.0`).
          * Save as a draft.
          * After approval, publish the release.
          * Deploy the released version to the production server.

-----

### General Rules

1.  Write clean, readable, well-commented code.
2.  Use version control systems (e.g., Git).
3.  Follow consistent coding styles.
4.  Write unit tests.
5.  Practice CI/CD.
6.  Communicate effectively.
7.  Seek help immediately when blocked.

-----

### CI/CD Pipeline Configuration Examples

#### GitHub Actions Example

**Workflow for `feature/*` branches (The Sandbox):**

```yaml
name: CI for Feature Branches
on:
  push:
    branches:
      - 'feature/**'  # Triggers on any branch starting with feature/
      - 'bugfix/**'

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Tests
        run: |
           npm install
           npm test
```

**Workflow for `develop` branch (Integration):**

```yaml
name: CI for Develop
on:
  push:
    branches:
      - develop

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Integration Tests
        run: |
           npm test
           # Optional: Deploy to internal Dev server
```

**Workflow for `main` branch (Staging):**

```yaml
name: Deploy to Staging
on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Deploy to Staging Server
        run: |
          ssh user@staging-server 'git pull origin main && docker-compose up -d --build'
```

-----

### Communication

  * **Red Branches (`feature/`):** It is okay if these fail in CI.
  * **Green Branches (`develop`, `main`):** These must never fail in CI. If `develop` breaks, no one can merge their work. Fixing `develop` becomes the top priority.
