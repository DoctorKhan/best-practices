# Best Practices

## Transparency

Engineers should commit frequently and push code every day. In the short-term, this kind of communication is critical when team members are in different time zones. On a day-to-day basis it enables one to share progress more effectively, requiring fewer meetings. For managers, it is important to track the direction of the code, or the activities of engineers, even before major commits to working branches. These may be done in branches which contain consistently broken code. Hereafter referred to as "red" branches. The red branch should contain many commits per day indicating what the engineer is working on.

When the code can pass the unit tests, the commits may be squashed and merged onto a working branch (green branch). In the long run, good git commits should tell a story. Step-by-step how was the project built? These changes enable one to identify and track potentially breaking changes, as well as enable new team members to learn the code faster. 

## Branch Structure

| Environment  | Branch Name | Branch Type | Purpose
|--------------|-------------|--------------|-------------|
| **Development**	| test		| red | make tests, fixes, and new features
|              		| feature	| green | incorporate working features
| **Production**	| alpha		| red | internal user testing live
|              		| main		| green | released to public live
|              		| release	| tagged | historical stable version releases

## Communication

Try to increase points of contact, checks, audits, milestones, and roadmaps. Reduce meetings, especially unstructued ones.




## General rules

Software engineering best practices are a set of guidelines and principles that help developers create high-quality, maintainable, and efficient software. Some key best practices include:

1. Writing clean, readable, and well-commented code
2. Using version control systems (e.g., Git) to track changes and collaborate with others
3. Following a consistent coding style and naming conventions
4. Breaking down complex problems into smaller, manageable tasks
5. Conducting code reviews to maintain quality and share knowledge
6. Writing unit tests to ensure code correctness and prevent regressions
7. Practicing continuous integration and continuous deployment (CI/CD) to automate build, test, and deployment processes
8. Optimizing performance by identifying and fixing bottlenecks
9. Documenting code, APIs, and architectures to facilitate understanding and maintenance
10. Choosing the right tools and frameworks for the job
11. Designing software with scalability, security, and maintainability in mind
12. Regularly refactoring code to improve its structure and reduce technical debt
13. Communicating effectively with team members, stakeholders, and clients
14. Staying up-to-date with the latest technologies and industry trends
15. Following agile development methodologies (e.g., Scrum, Kanban) to adapt to changing requirements and deliver value incrementally
16. Assess and seek help immediately when encountering a blocker.

By adhering to these best practices, software engineers can create software that is more reliable, easier to maintain, and better aligned with user needs and business goals. However, it's important to note that best practices may vary depending on the specific context, technology stack, and project requirements, so it's crucial to adapt and apply them accordingly.

## Machines

There are three different environments, typically existing on three different machines that will be deployed to periodically.

1. **Three Machines**
   - Production: This is the one that is available to your users. It should always stay up and be continually monitored. It should always stay on the main branch.
   - Staging: This runs in an environment identical to the production server, but is used to test functionality and integration tests before deploying to production.
   - Development: This can be your local machine. Run unit tests here and local deployments. Optionally, integration tests.
2. **Availability and Stability**
   - The production server must be available and stable at all times without interruption.
   - Monitor the server for bandwidth, connections, and resource usage to ensure high uptime.
   - Use a second machine to continuously monitor the server's availability and responsiveness.
   - The staging server may go down periodically if there are bugs, etc.
   - The staging machine should go down every time there are code changes.

## Test Driven Development (TDD)
1. Write the test first that fails (red), then commit code to red branch.
2. Write code until the test passes (green), then merge code into the green branch.
3. Commit code and merge it into the next branch as appropriate.
4. If you have time, test coverage should include these UX paths: golden, happy, sad, and bad paths

## Separation of Environments
1. **Environments**
   - There are two environments: the production environment and the development environment.
   - Use the development environment while you are developing the code. The production environment is what runs the final application which the users use.
   - The production environment will be set up on the production machine and the staging machine. They must be identical to ensure there are no changes between the staging and production.
   - The main and release branches are in the production environment.
   - The red and green branches are in the development environment.

## Branch Management
Maintain four branches: red, green, main, release. Red and green are deployed to the development machine, main is deployed to staging, and release to production machine.

1. **Deployment**
   - The test branch
      - should be deployed to the development machine (your local machine).
      - It should always fail some unit tests.
      - Commit broken code to the red branch liberally, each day. This ensures transparency.
      - Once all unit tests pass, merge it into the next branch (feature) squashing all commits.
   - The feature branch
      - should be deployed to the development machine (your local machine).
      - It should always pass all unit tests.
      - Once it passes all integration tests, merge it into the alpha branch squashing all commits.
   - The main branch
      - should be deployed to the staging machine.
      - Once it passes all alpha and beta tests, it should be merged into a main branch.
      - The main branch should be deployed to the production machine.
   - Do not push to release branch. Always do a pull request for the release.
   - Perhaps do a pull request to main as well, depending on your manager.
2. **Tools**
   - Use tools like SCP, rsync, Heroku, GitHub Actions, Puppet, or other DevOps systems for continuous deployment.

