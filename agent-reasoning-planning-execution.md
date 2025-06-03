## Agent Rules: Planning, Reasoning, and Execution

These rules define how the agent should behave at each stage—planning, reasoning, coding, and monitoring—transforming the previously defined prompts into prescriptive guidelines. Follow these rules to ensure consistent, transparent, and effective agentic assistance.

---

### 1. Planning & Roadmapping Rules

1. **High-Level MVP Roadmap Creation**

   * **Rule:** When the user introduces a new product concept or feature request, the agent must generate a 4-week MVP roadmap.
   * **Structure:** Present as a markdown table with columns:

     * **Week** (1–4)
     * **Goal** (concise statement of weekly objective)
     * **Deliverables** (specific artifacts or functionality)
     * **Dependencies** (external APIs, data, or other stories)

2. **Sprint Breakdown from Roadmap**

   * **Rule:** Given an existing 4-week roadmap, the agent must break a specified week into two one-week sprints.
   * **Output:** For each sprint, list:

     * **Epics** (broad functional areas)
     * **User stories** (bulleted, plain English descriptions)
     * **Estimate** (T-shirt size: S/M/L)
     * **External Services Required** (if any, e.g., specific HyperScaler APIs)

3. **Dependency Graph Generation**

   * **Rule:** When given a set of user stories, the agent must analyze dependencies and output a Graphviz DOT representation.
   * **Format:** Only the DOT code (no prose), where:

     * Each node is a story (with a unique identifier).
     * Directed edges point from prerequisite to dependent story.

4. **Milestone & Release Checklist**

   * **Rule:** Before a major version release, the agent must compile a release checklist containing:

     * Completed features (listed by name)
     * Confirmation that all unit/integration tests pass
     * Approval status of security scans
     * CI/CD configuration readiness (e.g., canary deployment step exists)
     * Updated documentation (README, API specs)
   * **Format:** Markdown checklist with unchecked boxes (`- [ ]`).

5. **Risk Assessment & Mitigation Plan**

   * **Rule:** For any multi-week plan, the agent must identify at least three technical risks. For each risk, supply:

     * **Description** (concise explanation)
     * **Likelihood** (High/Medium/Low)
     * **Mitigation Steps** (specific actions or fallback strategies)
   * **Format:** Bullet list, with each risk as a sub-bullet containing its three attributes.

---

### 2. Reasoning & Chain-of-Thought Rules

1. **Explain Coding Decision (Chain-of-Thought)**

   * **Rule:** Before producing any nontrivial code snippet, the agent must emit a chain-of-thought narrative that:

     1. Clarifies ambiguous requirements.
     2. Weighs alternative approaches (e.g., library versus custom logic).
     3. Considers edge cases, performance, and security.
     4. Concludes with a chosen implementation plan.
   * **Format:** Numbered steps, each briefly describing rationale, ending with a summary that states the chosen approach.

2. **Design Data Model with Reasoning**

   * **Rule:** When asked to define or modify a data model, the agent must first reason about:

     1. Field types (e.g., integer, string, timestamp)
     2. Indexing and query patterns
     3. Relationships between entities
   * **Output:**

     * A brief numbered reasoning narrative.
     * The final schema in SQL (PostgreSQL) or the relevant database definition language.

3. **Select Between Two API Designs**

   * **Rule:** If two implementation options are presented, the agent must compare them using a chain-of-thought that addresses:

     * Security implications
     * Ease of integration
     * Compatibility with HyperScaler or other existing services
   * **Outcome:** A final recommendation statement (e.g., “Recommend Option B because …”).

4. **Optimize Performance with Reasoning**

   * **Rule:** When asked to optimize a function (e.g., repeated storage calls), the agent must produce a bulletized reasoning that:

     1. Identifies the performance bottleneck
     2. Suggests caching or batching strategies
     3. Evaluates trade-offs (cost vs. complexity)
   * **Final Output:** A concise code snippet implementing the chosen optimization (e.g., in-memory cache with eviction policy).

5. **Error Handling Strategy Explanation**

   * **Rule:** Upon implementing any external API call (e.g., HyperScaler Function invocation), the agent must:

     1. List potential error types (timeouts, permission errors, service unavailability).
     2. Propose a retry/backoff strategy (e.g., exponential backoff with jitter).
     3. Output a utility function or code pattern demonstrating the strategy.

---

### 3. Coding & Test-Scaffolding Rules

1. **Generate Failing Unit Test (Red Phase)**

   * **Rule:** For each new function or endpoint, the agent must create a minimal failing unit test that:

     * Uses the appropriate framework (Jest for Node, pytest for Python).
     * Asserts the core behavior or return value.
     * Includes only necessary imports and boilerplate.
   * **Format:** Full test file content. No commentary beyond inline test comments.

2. **Generate Failing Integration Test**

   * **Rule:** When functionality spans multiple services (e.g., Function + Storage), the agent generates an integration test that:

     * Sends an HTTP request to the deployed endpoint.
     * Verifies both status code and expected JSON schema.
     * Uses placeholders for environment variables (e.g., `FUNCTION_URL`).
   * **Format:** Full test code (SuperTest or requests+pytest).

3. **Convert Pseudocode to Test Case**

   * **Rule:** Given a pseudocode description, the agent must translate it into a BDD-style test with:

     * Clear `describe`/`it` or `scenario` blocks.
     * Red–Green–Refactor placeholders.

4. **Add Mocks/Stubs to Unit Test**

   * **Rule:** When an external dependency is involved, the agent must insert the minimal mocking setup (e.g., `jest.mock` or `unittest.mock`) and leave comments indicating where to implement stubs. The initial test must still fail.

---

### 4. Refactoring Rules

1. **Identify Duplication and Extract Function**

   * **Rule:** On any code snippet containing repeated logic, the agent must:

     1. Detect duplicated sections.
     2. Extract them into a new helper function named according to context (e.g., `sanitizeInput`).
     3. Provide a unified diff: removed duplication and new helper function.

2. **Convert Callback to Async/Await**

   * **Rule:** For callback-based functions, the agent refactors into `async/await` style:

     * Wrap asynchronous calls in `try/catch`.
     * Remove nested callbacks.
     * Ensure the function returns a `Promise`.
   * **Format:** Only the refactored function code in a single file.

3. **Simplify Complex Conditional**

   * **Rule:** When encountering nested `if/else` logic, the agent must:

     * Use guard clauses or early returns to flatten structure.
     * Document which lines were removed or simplified in comments.
   * **Output:** Final refactored function with inline annotations.

4. **Enforce Minimal Import Strategy**

   * **Rule:** For any file, the agent should:

     * Identify unused or heavy imports.
     * Remove those imports, leaving only essential HyperScaler SDK or built-in modules.
   * **Output:** Cleaned import block followed by refactored code.

---

### 5. CI/CD YAML Generation Rules

1. **Generate GitHub Actions Workflow for MVP Deploy**

   * **Rule:** For an MVP branch (`mvp`), the agent must produce a YAML file named `mvp-deploy.yml` with steps:

     1. `checkout` code
     2. Install dependencies (`npm install` or `pip install`)
     3. Run unit tests (`npm test` or `pytest`)
     4. Deploy to HyperScaler Function (use appropriate CLI commands)
     5. Run a smoke test (`curl` or `requests`)
   * **Details:** Include placeholders for secrets:

     * `${{ secrets.HYPERSCALER_ACCESS_KEY }}`, `${{ secrets.FUNCTION_NAME }}`.

2. **Generate Terraform Snippet for Function Provisioning**

   * **Rule:** When requested to provision infrastructure, the agent outputs a `.tf` file containing:

     * `aws_lambda_function` (or equivalent resource) definition.
     * Minimal IAM role with execution permissions.
     * Storage bucket (e.g., S3 resource).
   * **Include Only:** Required attributes: `function_name`, `handler`, `runtime`, `role`, `source_code_hash`, `environment`.

3. **CI Lint Configuration**

   * **Rule:** Agent must generate a YAML step that:

     * Runs `npm run lint` (JS/TS) or `flake8 src/` (Python).
     * Fails the job on lint errors.
     * Annotates errors in PR checks.

4. **Automate Canary Deployment**

   * **Rule:** Provide a GitHub Actions job that:

     1. Deploys new function version with alias `canary`.
     2. Waits 30 seconds.
     3. Hits the endpoint 5 times, expecting HTTP 200.
     4. If successful, shifts 25% traffic to `canary` alias; otherwise, reverts.
   * **Format:** Full YAML with placeholders for `FUNCTION_NAME` and region.

---

### 6. Rollback & Monitoring Rules

1. **Generate Rollback Script for Function**

   * **Rule:** Agent creates a shell script `rollback.sh` that:

     1. Reads `stable_version.json` (JSON with `{ "version": "<x>" }`).
     2. Calls `aws lambda update-alias --function-name $FUNCTION_NAME --name prod --function-version $VERSION`.
     3. Posts an MCP message summarizing rollback success or failure.
   * **Placeholders:** Environment variables for `FUNCTION_NAME` and AWS region.

2. **Create Smoke-Monitoring Script**

   * **Rule:** Provide a Python script `monitor.py` that:

     * Reads env vars: `FUNCTION_URL`, `ALERT_WEBHOOK`.
     * Every 60 seconds, issues an HTTP GET to `FUNCTION_URL`.
     * On non-200 or timeout, sends a POST to `ALERT_WEBHOOK` with JSON `{ "status": "failure", "timestamp": "<ISO-8601>" }`.
     * Logs all results to `monitor.log`.
   * **Library Use:** Standard library plus `requests` if available.

3. **Automated MCP Notification on Failure**

   * **Rule:** Within `monitor.py`, integrate an HTTP POST to MCP endpoint (`http://localhost:8000/mcp`) on failure, with payload fields:

     * `action`: `"function-failure"`
     * `functionName`: from `FUNCTION_NAME` env var
     * `timestamp`: current UTC ISO-8601
     * `details`: error message or status code
   * **Format:** Show JSON snippet to send via `requests.post`.

4. **Generate Metrics Dashboard Template**

   * **Rule:** Produce `metrics-dashboard.md` containing:

     1. Commands to fetch invocation metrics (e.g., `aws cloudwatch get-metric-statistics`).
     2. Example `jq` parsing to calculate error rate: `(failed_invocations / total_invocations) * 100`.
     3. Instructions to schedule via `cron` or GitHub Actions.
   * **Include:** MCP annotations for alert thresholds (e.g., “If error rate > 5%, send MCP alert”).

---

### 7. Reasoning & Execution Sample Behavior

* **When implementing any new function:**

  1. **Apply Chain-of-Thought Rules**: The agent must produce a numbered reasoning narrative before generating code.
  2. **Convert reasoning to Code**: After reasoning, generate the final implementation in the requested language.
  3. **Link to Tests**: Ensure at least one minimal test exists (unit or integration) that aligns with reasoning.
  4. **Document Edge Cases**: Inline comments must identify at least one edge case considered.

* **Example for `generatePresignedUrl` Function:**

  1. **Chain-of-Thought Steps (Agent must create this):**

     * Clarify that the function needs a PUT presigned URL valid for 15 minutes.
     * Identify AWS SDK v3’s `getSignedUrl` with `PutObjectCommand`.
     * Validate `bucketName` and `objectKey` inputs.
     * Conclude with an implementation plan using environment variables for `AWS_REGION`.
  2. **Final Code Generation:**

     * Provide TypeScript code that matches the reasoning steps.
  3. **Unit Test Linkage:**

     * Generate a Jest test that initially fails, asserting status code and presence of `PresignedUrl` property.

---

## Enforcement & Usage

* **Automatic Invocation:**

  * Whenever the agent is asked to “plan,” “reason,” “generate tests,” or “deploy,” it must reference the corresponding rule above.
  * The agent must embed MCP annotations when interacting with GitHub Issues or CI/CD events.

* **Self-Validation:**

  * After completing each step, the agent should self-audit:

    1. Did I follow the specified output format?
    2. Did I provide chain-of-thought where required?
    3. Did I attach the correct file names and placeholders?

* **Fail-Safes:**

  * If any rule cannot be executed (e.g., missing context or credentials), the agent must explicitly state which rule failed and why, requesting additional information.

---

**End of Agent Rules Document**
