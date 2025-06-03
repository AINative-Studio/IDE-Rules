## **Introduction**

These coding standards guide our XP-oriented development team at **AINative Studio** in building secure applications for banking and compliance-based clients. The principles focus on **code quality, security, collaboration, and Behavior-Driven Development (BDD)**. All development workflows are orchestrated by **Cody**, our AI Agent embedded within the AINative Studio IDE.

---

## **📋 Backlog Management**

The agent uses **GitHub Issues** as the source of truth for backlog management and leverages **MCP (Model Context Protocol)** to create, update, and track issues in real time during the development cycle.

### **LLM Prompts for Backlog Management**

1. **Prompt:**

   > *“Fetch the highest-priority open GitHub issue labeled “feature.” Use MCP to annotate its current state and generate a plan to start, work on, and complete it. Include explicit steps for AINative-driven test generation (TDD) and the branching strategy.”*

2. **Prompt:**

   > *“Before implementation begins, create a work-in-progress (WIP) GitHub issue comment via MCP. Explain why this enforces best practices in traceability and fosters continuous synchronization with the agent.”*

3. **Prompt:**

   > *“Given a list of GitHub issues fetched by MCP, classify each as a bug, chore, or feature. Apply labels accordingly, and outline how the agent should process each category within the structured workflow.”*

### **Standard Workflow**

1. **Issue Selection & MCP Annotation**

   * Cody queries GitHub Issues:

     * Filter by `state: open`, `label: ready`, sorted by priority.
   * Cody uses MCP messages to annotate issue context and backlog metadata.

2. **Branch Naming Convention**

   * **feature/{issue-number}-{short-title-slug}**
   * **bugfix/{issue-number}-{short-title-slug}**
   * **chore/{issue-number}-{short-title-slug}**

3. **TDD Workflow (with AINative Agents)**

   1. **WIP: Red Tests**

      * Cody issues a failing-test scaffold via MCP, posted as a comment on the issue.
   2. **WIP: Green Tests**

      * Cody implements code until tests pass; commits under branch `feature/{issue}`.
   3. **Refactor & Commit**

      * Cody refactors code to remove duplication, improve readability, and commits under `refactor/{issue}` if necessary.

4. **Pull Request Process**

   1. Cody marks the GitHub Issue as **“in progress”** via MCP.
   2. Cody opens a PR against `develop` once tests pass.
   3. AINative Agent runs automated checks (linting, security scans, unit/integration tests).
   4. Cody adds MCP comments summarizing code coverage and any security warnings.
   5. Upon PR approval and merge, Cody transitions the issue to **“deliver-ready”** via MCP.
   6. Cody updates issue labels to `delivered`.

5. **Issue Closure & Verification**

   * After merging, Cody runs a final integration test suite.
   * If successful, Cody marks the GitHub Issue **“closed”** and logs the merge commit hash in issue comments via MCP.

---

## **📖 Story Types & Estimation**

Cody classifies work into **Features, Bugs, and Chores** and leverages MCP to maintain metadata (labels, estimates) on GitHub Issues.

### **LLM Prompts for Story Estimation**

1. **Prompt:**

   > *“Analyze the GitHub Issue description and existing MCP annotations to determine whether it’s a feature, bug, or chore. Assign appropriate labels and update its estimate field in MCP metadata.”*

2. **Prompt:**

   > *“Using the Fibonacci scale (0, 1, 2, 3, 5, 8), estimate the complexity of this GitHub Issue. Justify by referencing required API integrations, agent orchestration, and test coverage. Update the estimate in issue comments via MCP.”*

### **Story Estimation Guidelines**

* **0️⃣ (Zero Points):** Trivial changes (typos, minor settings).
* **1️⃣ (One Point):** Clear-cut tasks solvable by minimal code (e.g., small config update).
* **2️⃣ (Two Points):** Tasks requiring simple API calls or minor agent modifications.
* **3️⃣, 5️⃣, 8️⃣ (Higher Points):** Complex features (e.g., new AINative agent flow, quantum integration) that should be split if > 5 points.

---

## **🎨 Coding Style Guidelines**

Cody enforces consistent style conventions across all AINative codebases for readability, maintainability, and seamless collaboration.

### **LLM Prompts for Code Consistency**

1. **Prompt:**

   > *“Given a code snippet with inconsistent naming conventions, rewrite it to follow AINative best practices: `camelCase` for functions, `PascalCase` for classes, and fully annotate types.”*

2. **Prompt:**

   > *“Reformat the provided block to enforce 4-space indentation, 80-character line limits, and include language-appropriate docstrings or JSDoc. Use MCP to output a diff that can be applied automatically.”*

### **Key Standards**

* **Naming Conventions**

  * **Functions/Methods:** `camelCase` (JS/TS, Python)
  * **Classes/Constructors:** `PascalCase`
  * **Constants/Env Vars:** `UPPER_SNAKE_CASE`
  * **Database Models/Entities:** `PascalCase` (e.g., `UserAccount`)

* **Code Formatting**

  * **Indentation:** 4 spaces
  * **Line Length:** ≤ 80 characters
  * **Braces & Semicolons:** Follow idiomatic guidelines (e.g., semicolons in TS; no semicolons in Python)

* **Comments & Docstrings**

  * Use **meaningful**, **concise** comments
  * Employ **docstrings** (Python) or **JSDoc/TSDoc** (JS/TS) for all public interfaces
  * Remove outdated comments; rely on code clarity

* **Type Annotations**

  * **TypeScript:** Fully annotate every exported function, interface, and model
  * **Python:** Use **PEP 484** type hints for all public functions and classes

* **Imports / Dependencies**

  * Group external packages before internal modules
  * Prefer absolute paths (e.g., `@ainative/services`) over deep relative imports

* **Security Practices**

  * Sanitize and validate all user inputs in AINative API endpoints
  * Use AINative-provided libraries for encryption, hashing, and token management
  * Escape dynamic parameters in queries/templates to prevent injection

---

## **🧪 Testing Strategy (TDD/BDD)**

Cody drives **Test-Driven Development** (TDD) and **Behavior-Driven Development** (BDD), auto-generating tests and running them under MCP orchestration.

### **LLM Prompts for Testing**

1. **Prompt:**

   > *“Generate a BDD-style unit test suite for function `createAgentWorkflow(config)` using Jest (for JS/TS) or pytest (for Python). Publish the test file and reference it in the GitHub Issue via MCP.”*

2. **Prompt:**

   > *“Write an integration test that validates the interaction between `agent-coordination-service` and `memory-service`. Ensure to cover edge cases, and store the results in the CI artifacts. Document the test run status via MCP.”*

### **Test Structure**

1. **Unit Tests**

   * **Frameworks:**

     * JS/TS: **Jest** (with `@jest/globals`)
     * Python: **pytest** (+ `pytest-bdd` for BDD scenarios)
   * **Conventions:**

     * Test file name mirrors module name (e.g., `agentService.spec.ts`)
     * Tests created before implementation (Red–Green–Refactor)

2. **Integration Tests**

   * **Scope:** Interactions between multiple microservices (e.g., Agent Controller + Memory Service)
   * **Isolation:** In-memory databases (e.g., SQLite or `mongodb-memory-server`)

3. **Functional/API Tests**

   * **Tools:**

     * JS/TS: **SuperTest** for HTTP endpoints
     * Python: **requests** + **pytest**
   * **Coverage:** Validate all public API routes (status codes, response schemas, error handling)

4. **Test Suites & CI Integration**

   * **Directory Structure:**

     ```
     /tests
       /unit
       /integration
       /functional
     ```
   * **Naming:** Prefix files with module/service name (e.g., `userModel.test.py`)
   * **CI Configuration:**

     * AINative CI runs all tests on every push/PR
     * Coverage ≥ 90% required for new code
   * **Example (Jest):**

     ```javascript
     // tests/unit/calculator.spec.ts
     import { add } from '../../src/utils/calculator';

     describe('Calculator Utility', () => {
       describe('addition()', () => {
         it('adds two positive integers correctly', () => {
           expect(add(3, 4)).toBe(7);
         });
       });
     });
     ```

---

## **🔄 Continuous Integration & Deployment (CI/CD)**

Cody orchestrates CI/CD using **GitHub Actions** and **AINative Build Agents**, ensuring rapid, automated testing and deployment.

### **LLM Prompts for CI/CD**

1. **Prompt:**

   > *“Define a CI workflow that builds Docker images, runs linting, unit/integration tests, security scans, and deploys to staging for a microservices app. Use MCP to output YAML configuration suitable for GitHub Actions.”*

2. **Prompt:**

   > *“Identify common pipeline failures—e.g., flaky tests or missing dependencies—and generate automated MCP checks or remedial scripts to resolve them.”*

### **Key CI/CD Practices**

* **Continuous Integration**

  1. **Branch Protection:**

     * `main` and `develop` are protected; PRs require passing CI and one approving review.
  2. **Automated Steps:**

     * **Lint & Static Analysis:** ESLint (JS/TS), flake8/mypy (Python)
     * **Unit & Integration Tests**
     * **Security Scans:** Snyk, Bandit, or equivalent
     * **Code Coverage Reports**
  3. **Notifications:**

     * Failures trigger MCP notifications to Slack/Teams

* **Continuous Deployment**

  1. **Staging:**

     * Merges to `develop` trigger deployment to staging (e.g., Kubernetes)
  2. **Production:**

     * Tagging a release on `main` triggers production deployment
     * **Canary Releases:** Gradually shift traffic; rollback automatically if health checks fail
  3. **Rollback Strategy:**

     * If post-deploy health check fails, Cody reverts to previous stable image
  4. **Infra as Code:**

     * Managed by Terraform or Pulumi; versioned alongside application code

---

## **🔧 Version Control (GitHub)**

Cody enforces **GitHub workflows** to guarantee collaboration, traceability, and high code quality—complemented by MCP-driven automation.

### **LLM Prompts for GitHub Workflows**

1. **Prompt:**

   > *“Summarize these Git commit messages into a detailed PR description. Follow AINative’s Conventional Commit style and embed the summary in the PR description via MCP.”*

2. **Prompt:**

   > *“Explain why frequent WIP commits improve context sharing with AINative Agents and ensure no local work is lost. Use MCP to post this rationale as a PR comment.”*

### **Key GitHub Practices**

* **Branching Strategy:**

  * Branch from `develop` following naming conventions
  * Keep branches short-lived (< 2 weeks)
  * Regularly rebase onto `develop` to stay up-to-date

* **Pull Requests:**

  * **Requirements:**

    1. PR description includes purpose, linked GitHub Issue, and testing steps.
    2. Checklist in description:

       * All tests pass locally
       * Linting and static analysis completed
       * Security scan results attached
       * Documentation updated (e.g., OpenAPI spec)
  * **Agent Comments:**

    * Cody automatically reviews code style, suggests missing tests, and flags security concerns via MCP comments.

* **WIP Commits:**

  * Prefix commit messages with `WIP:` for ongoing work
  * Cody captures these commits to update MCP memory snapshots

* **End-of-Day Commits:**

  * Ensure no changes remain uncommitted
  * Cody posts a summary of daily progress in the team channel via MCP

---

## **📌 Conclusion**

These rules enable **Cody**, the AINative Studio Agent, to manage the entire development cycle—backlog, estimation, coding style, testing, CI/CD, and version control—using GitHub Issues and MCP for real-time synchronization. Continuous feedback between human developers and Cody ensures code remains secure, maintainable, and aligned with enterprise standards.
