## Best Practices

### Priorities
1. The production server should be up and running at all times
2. New versions are fully tested before deploying to production server
3. You must push daily incremental commits
4. Always write unit tests
5. Read rule #1

### Transparency

Engineers should commit frequently and push code daily, especially for teams in different time zones. Managers can track code direction through frequent commits in "red" branches (with broken code). When code passes unit tests, squash and merge commits into a "green" branch. Good commit messages help track changes and aid new team members.

1. Commit and push code every day.
2. Do incremental commits, and squash when merging.
3. Even if you don't write code, commit reports of activites every day.

### Branch Structure

Deploying a React project with a Python backend can be streamlined with a well-defined branching and deployment strategy.

| Environment     | Branch Name      | Type   | Purpose                             |
|-----------------|-----------|--------|----------------------------------|
| **Development** | `<your name>` | red | make tests, fixes, and new features |
|                 | feature   | green  | incorporate working features. integration test locally |
| **Production**  | main   | red    | live testing |
|                 | main (tagged) | green  | released to public live. tagged historical stable version releases of the main branch|

### Steps for Deployment

1. **Local Development:**
    - **Branch: developer-name**
    - **Purpose:** Individual development work, making tests, fixes, and new features.
    - **Action:** 
      - Each developer works on their own feature branch off `developer-name`.
      - Commit frequently and push to the `developer-name` branch.

2. **Feature Integration:**
    - **Branch: feature**
    - **Purpose:** Incorporate working features and perform local integration tests.
    - **Action:** 
      - Once features are complete, merge from `feature` branch to your branch.
      - Review and test integrated features locally. Then merge from your branch to the `feature` branch
      - Ensure both frontend and backend work together seamlessly by running integration tests locally.

3. **Staging Deployment:**
    - **Branch: main**
    - **Purpose:** Live testing environment.
    - **Action:** 
      - After successful local testing, create a PR from the `feature` branch to the `main` branch.
      - Merge the PR after approval.
      - Deploy the `main` branch to the staging server.
      - Perform live testing to ensure everything works as expected in a production-like environment.

4. **Production Deployment:**
    - **Branch: main (tagged)**
    - **Purpose:** Release the application to the public.
    - **Action:** 
      - Once a version is deemed stable and is running smoothly in staging, create a tag from the `main` branch.
      - The tag represents a specific release version (e.g., `v1.0.0`).
      - Save as a draft.
      - After approval, your manager will publish the release.
      - Deploy the released version to the production server.
      - Monitor the deployment for any issues.

### Communication

- Increase points of contact, checks, audits, milestones, and roadmaps. 
- Reduce unstructured meetings.

### General Rules

1. Write clean, readable, well-commented code.
2. Use version control systems (e.g., Git).
3. Follow consistent coding styles and naming conventions.
4. Break down complex problems.
5. Conduct code reviews.
6. Write unit tests.
7. Practice CI/CD.
8. Optimize performance.
9. Document code, APIs, and architectures.
10. Choose appropriate tools and frameworks.
11. Design for scalability, security, and maintainability.
12. Regularly refactor code.
13. Communicate effectively.
14. Stay updated with industry trends.
15. Follow agile methodologies.
16. Seek help immediately when blocked.

## Two Environments

### Development Environment

Each environment has a different configuration, build, and environment variables, typically stored in a `.env` file. They may also exist on different machines.

1. **Machines:**
   - **Development Machine:** Your local machine for running unit tests, local deployments, and integration tests.
   - **Staging Machine:** Can be same as production machine, just different ports.
   - **Production Machine:** 

3. **Best Practices:**
   - Commit frequently to the red branch, indicating work progress.
   - Squash and merge commits to the green branch once unit tests pass.

### Production Environment

2. **Machines:**
   - **Staging Machine:** Identical to the production server, used for testing before deploying to production. May go down periodically for testing.
   - **Production Machine:** Always up, running the main branch. Monitored for uptime, bandwidth, and resource usage.

3. **Deployment Process:**
   - **Main Branch:** Deployed to the staging machine. Once it passes all tests, merge and deploy to the production machine.
   - **Release Branch:** Use pull requests for deploying stable versions.

### Availability and Stability

1. **Production Server:** Must be stable and continuously monitored for high uptime.
2. **Staging Server:** Used for final testing, can experience downtime for testing purposes.

### Test Driven Development (TDD)

1. Write a failing test (red), commit to red branch.
2. Write code until the test passes (green), merge to green branch.
3. Commit and merge code to the next branch as appropriate.
4. Include UX paths (golden, happy, sad, bad) if time allows.

### Tools

- Use tools like SCP, rsync, Heroku, GitHub Actions, Puppet, or other DevOps systems for continuous deployment.
- Periodically commit to GitHub using Colab: [YouTube Guide](https://www.youtube.com/watch?v=uBY06NpnLcs).

By maintaining clear separation and management of the development and production environments, adhering to best practices, and utilizing effective tools, the team can ensure a stable, efficient, and transparent workflow.

### Deployment Tools and CI/CD Integration

1. **Continuous Integration (CI):**
    - Use tools like GitHub Actions, GitLab CI, CircleCI, or Jenkins to automate the build, test, and deployment process.
    - Set up CI pipelines to automatically run tests on every push to `developer` and `feature` branches.

2. **Continuous Deployment (CD):**
    - Use tools like Docker, Kubernetes, or Heroku to streamline the deployment process.
    - Configure your CI/CD pipelines to automatically deploy the `main` branch to the staging server and the `main` (tagged) branch to the production server.

### Example CI/CD Pipeline Configuration

#### GitHub Actions Example

**Workflow for `feature` branch:**
```yaml
name: CI for Feature Branch

on:
  push:
    branches:
      - feature

jobs:
  build:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:latest
        ports:
          - 5432:5432
        env:
          POSTGRES_DB: mydatabase
          POSTGRES_USER: myuser
          POSTGRES_PASSWORD: mypassword
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
    - uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.8

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'

    - name: Install backend dependencies
      run: |
        python -m venv venv
        source venv/bin/activate
        pip install -r requirements.txt

    - name: Install frontend dependencies
      run: |
        cd frontend
        npm install

    - name: Run backend tests
      run: |
        source venv/bin/activate
        pytest

    - name: Run frontend tests
      run: |
        cd frontend
        npm run test
```

**Workflow for `staging` branch:**
```yaml
name: Deploy to Staging

on:
  push:
    branches:
      - staging

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: Deploy to Staging Server
      run: |
        ssh user@staging-server 'cd /path/to/project && git pull origin staging && docker-compose down && docker-compose up -d --build'
      env:
        SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
```

**Workflow for `main` branch:**
```yaml
name: Deploy to Production

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: Deploy to Production Server
      run: |
        ssh user@production-server 'cd /path/to/project && git pull origin main && docker-compose down && docker-compose up -d --build'
      env:
        SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
```

By following this structure and utilizing CI/CD tools, you can ensure a smooth and reliable deployment process for your React frontend and Python backend applications.
