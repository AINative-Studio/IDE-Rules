## **Introduction**

These coding standards guide our XP-oriented development team at **AINative Studio** in rapidly building MVP prototypes with minimal code. We leverage HyperScaler APIs for Compute, Storage, and Functions to achieve the fastest, most stable proof of concept. All workflows are orchestrated by **Cody**, our AI Agent embedded within the AINative Studio IDE. Emphasis is on **speed**, **stability**, and **Test-Driven/Behavior-Driven XP programming** methods.

---

## **📋 Backlog Management (MVP Mode)**

Cody uses **GitHub Issues** as the single source of truth and leverages **MCP (Model Context Protocol)** to manage backlog items in real time. For MVP mode, prioritization focuses on “just enough” scope to validate core functionality.

### **LLM Prompts for Rapid Backlog Handling**

1. **Prompt:**

   > *“Fetch the top open GitHub issue labeled `mvp-feature`. Use MCP to annotate its MVP-ready acceptance criteria and generate a plan to prototype it using HyperScaler Function and Storage APIs.”*

2. **Prompt:**

   > *“Create a WIP comment via MCP that outlines how to scaffold a failing test for this MVP feature using minimal HyperScaler SDK calls. Explain why a WIP commit enhances traceability in rapid prototyping.”*

3. **Prompt:**

   > *“Given an array of GitHub issues fetched by MCP, filter for `mvp-feature`, `mvp-bug`, or `mvp-chore` and assign or update labels. Outline how Cody should process each category under an MVP-first workflow.”*

### **MVP Workflow Steps**

1. **Issue Selection & MCP Annotation**

   * Cody filters GitHub Issues by `state: open`, `label: mvp-ready`, sorted by lowest effort.
   * Cody annotates each issue with minimal acceptance criteria (e.g., “Echo input via serverless function” or “Store simple JSON in managed table”).

2. **Branch Naming**

   * **feature/mvp-{issue-number}-{short-slug}**
   * **bugfix/mvp-{issue-number}-{short-slug}**
   * **chore/mvp-{issue-number}-{short-slug}**

3. **Minimal TDD Workflow**

   1. **WIP: Red Tests**

      * Cody auto-generates a single failing test targeting HyperScaler Function or Storage SDK (e.g., “expect HTTP GET `/ping` to return 200”).
   2. **WIP: Green Tests**

      * Cody writes the minimal implementation using HyperScaler Function (e.g., AWS Lambda or Azure Function) until the test passes.
   3. **Refactor & Commit**

      * Cody removes obvious duplication or unnecessary glue code, leaving only the smallest runnable prototype. Commit under `refactor/mvp-{issue}` if needed.

4. **Pull Request (PR) Process**

   1. Cody marks the GitHub Issue **“in progress”** via MCP.
   2. Cody opens a PR targeting the `mvp` branch once tests pass.
   3. Cody triggers a lightweight CI run:

      * **HyperScaler SDK linter** (if available)
      * **Basic unit test suite** (only the newly added test)
   4. Cody adds MCP comments summarizing pass/fail status and any notable logs.
   5. Upon PR approval and merge into `mvp`, Cody transitions the issue to **“deliver-ready”** and labels `mvp-done`.

5. **Issue Closure & Smoke Verification**

   * After merging, Cody deploys to a HyperScaler Function or Container.
   * Cody runs a single smoke test (e.g., HTTP 200 check).
   * If successful, Cody marks the issue **“closed”** and logs the deployment URL in issue comments via MCP.

---

## **📖 Story Types & Estimation (MVP Context)**

Cody classifies work into **mvp-feature**, **mvp-bug**, and **mvp-chore**. Estimation uses a simplified scale (T-shirt sizes: S, M, L) to keep velocity high.

### **LLM Prompts for MVP Estimation**

1. **Prompt:**

   > *“Analyze this GitHub Issue labeled `mvp-feature` and its MCP annotations. Assign a T-shirt size estimate (S, M, L) based on effort to integrate a single HyperScaler Function/Storage call.”*

2. **Prompt:**

   > *“Using the T-shirt scale, justify why this is “S” (e.g., < 1 hour of work), “M” (\~1–3 hours), or “L” (>3 hours) for an MVP implementation. Update estimate in MCP metadata.”*

### **Story Estimation Guidelines**

* **S (Small):**

  * One or two HyperScaler SDK calls.
  * Unit test + minimal configuration (e.g., “Create `/status` function returning hardcoded JSON”).
* **M (Medium):**

  * Composing two or three services (e.g., Function + Storage write/read).
  * Requires simple orchestration (e.g., “Upload file to managed bucket, return presigned URL”).
* **L (Large):**

  * Prototype of a core workflow needing multiple services (e.g., Function, Queue, Storage).
  * May require initial configuration scripts or Terraform snippet.

---

## **🎨 Coding Style Guidelines (Minimal Code Focus)**

Cody enforces concise, self-contained code that calls HyperScaler-managed services directly. Avoid boilerplate: rely on managed defaults.

### **LLM Prompts for Minimal-Code Consistency**

1. **Prompt:**

   > *“Given this code snippet, remove all unnecessary layers and rewrite to directly use the HyperScaler Function SDK in the language runtime (Node/Python). Ensure it fits within 80 characters per line.”*

2. **Prompt:**

   > *“Reformat the provided block to use a single handler function file (e.g., `index.js` or `main.py`) that utilizes environment variables for all configuration. Output a diff via MCP.”*

### **Key Standards**

* **Single-File Functions**

  * Each Function/Handler should live in one file (`index.js`, `main.py`, etc.).
  * Use default entrypoints and minimal dependencies.

* **Environment Variables & Secrets**

  * Store all credentials/config in managed **Secrets Manager** or **Key Vault**.
  * Refer to them via `process.env` (Node) or `os.getenv` (Python).

* **Compact Imports**

  * Only import the HyperScaler SDK modules you need (e.g., `from aws_lambda_powertools import Logger` or `from azure.storage.blob import BlobServiceClient`).
  * Avoid full-fledged frameworks—use built-in HTTP handlers (e.g., AWS API Gateway proxy integration).

* **Inline Documentation**

  * Use **very brief** docstrings/JSDoc headers explaining the function purpose and environment variables.
  * Skip lengthy comments—rely on code clarity.

* **Error Handling**

  * Catch only expected errors (e.g., missing environment var).
  * Fail fast: log and rethrow or return HTTP 500.

* **Security Practices**

  * Rely on HyperScaler-managed IAM roles and scopes.
  * Do not write custom encryption; use managed KMS or Vault.
  * Validate inputs at a minimal level (e.g., type checks) before passing to SDK.

---

## **🧪 Testing Strategy (MVP TDD/BDD)**

Cody drives **lean TDD** and **BDD** to validate MVP code. Tests remain minimal yet effective.

### **LLM Prompts for MVP Testing**

1. **Prompt:**

   > *“Generate a minimal unit test for this function handler (e.g., `handler(event, context)`) that expects HTTP 200 and a JSON body. Use Jest (Node) or pytest (Python). Link the test file in the GitHub Issue via MCP.”*

2. **Prompt:**

   > *“Write a BDD-style smoke test that invokes the deployed HyperScaler Function endpoint and verifies the response schema. Store test results in CI logs and report status via MCP.”*

### **Test Structure**

1. **Unit Tests (Red–Green–Refactor)**

   * One or two simple test cases per function.
   * Use small mocks or stubs for SDK calls (e.g., mocking `BlobServiceClient.upload_blob`).
   * Example (Node/Jest):

     ```javascript
     // tests/unit/handler.spec.js
     const { handler } = require('../../src/index.js');

     test('handler returns 200 with JSON payload', async () => {
       const event = { httpMethod: 'GET' };
       const result = await handler(event);
       expect(result.statusCode).toBe(200);
       expect(JSON.parse(result.body)).toHaveProperty('message');
     });
     ```

2. **Integration/Smoke Tests**

   * Use actual deployed function URL.
   * One end-to-end check per feature (e.g., upload → list).
   * Example (Python/pytest):

     ```python
     # tests/integration/test_upload_and_list.py
     import requests

     def test_upload_and_list(mvp_function_url):
         data = {"filename": "test.txt", "content": "hello"}
         r1 = requests.post(f"{mvp_function_url}/upload", json=data)
         assert r1.status_code == 200

         r2 = requests.get(f"{mvp_function_url}/list")
         assert r2.status_code == 200
         items = r2.json()
         assert "test.txt" in [item["name"] for item in items]
     ```

3. **CI Integration**

   * CI only runs newly added tests.
   * On every PR to `mvp`, Cody runs:

     1. **Unit tests** (local mocks)
     2. **Smoke test** (if feature deploy exists)
   * Failures block merge.

---

## **🔄 Continuous Integration & Deployment (MVP Focus)**

Cody configures **GitHub Actions** for rapid deployments to HyperScaler-managed services. Deployment is simplified to “push code → run Function deploy.”

### **LLM Prompts for MVP CI/CD**

1. **Prompt:**

   > *“Generate a GitHub Actions workflow that, on pushes to `mvp`, installs dependencies, runs unit tests, and deploys a serverless function to the HyperScaler (AWS Lambda, Azure Function, or GCP Cloud Function). Use MCP to output YAML.”*

2. **Prompt:**

   > *“Identify common MVP pipeline failures (e.g., missing env var) and generate an automated check that fails early with an explanatory log. Use MCP to embed this check into CI.”*

### **Key CI/CD Practices**

* **Branch Protection (MVP)**

  * Protect `mvp` branch; require passing CI to merge PRs.

* **Automated Steps**

  1. **Setup**

     * Install HyperScaler CLI/SDK (e.g., `awscli`, `azure-functions-core-tools`).
     * Install project dependencies (e.g., `npm install` or `pip install -r requirements.txt`).
  2. **Unit Test**

     * Run only unit tests (no full integration).
  3. **Deploy**

     * **Deploy to “mvp” environment:**

       * AWS: `aws lambda update-function-code` with inline ZIP.
       * Azure: `az functionapp deployment source config-zip`.
       * GCP: `gcloud functions deploy`.
     * If deployment fails, pipeline stops.
  4. **Smoke Test (Optional)**

     * If the function existed previously, run a single smoke test against endpoint.

* **Rollback Strategy**

  * For MVP, no automated rollback; rely on last successful deployment as alias/version.
  * Cody comments the previous function ARN or URL on failure for manual revert.

* **Infra as Code (Minimal)**

  * Keep Terraform/ARM/Deployment Manager templates simple (e.g., only function definition, storage bucket).
  * Version these in `infra/mvp/` folder; updated manually when needed.

---

## **🔧 Version Control (GitHub)**

Cody enforces **lean GitHub workflows** suitable for MVP: frequent, small commits; minimal PR reviews; automatic MCP updates.

### **LLM Prompts for MVP GitHub Workflows**

1. **Prompt:**

   > *“Summarize the latest four commits into a brief PR description that highlights changes to serverless function code. Use MCP to auto-generate the summary.”*

2. **Prompt:**

   > *“Explain why making small, frequent WIP commits (every 30–60 minutes) aids rapid prototype development. Post this rationale as a PR comment via MCP.”*

### **Key GitHub Practices**

* **Branching Strategy (MVP)**

  * All feature branches prefix with `feature/mvp-`.
  * Keep branch lifetime < 48 hours; merge quickly into `mvp`.
  * Regularly rebase onto `mvp` to minimize drift.

* **Pull Requests**

  * **Requirements:**

    1. PR title: `MVP: short description`
    2. Description must include:

       * Linked GitHub Issue number.
       * Steps to test locally (e.g., “npm run test” or `pytest`).
       * Deployed Function URL if existing.
  * **Cody’s Automated Checks:**

    * Lint only HyperScaler SDK usage (e.g., checking required env vars).
    * Unit test pass/fail.
    * Attach MCP comments summarizing test coverage and deployment status.

* **WIP Commits**

  * Use `WIP:` prefix.
  * Commit as soon as a test scaffolding changes or minimal functional code passes.

* **End-of-Day Commits**

  * Ensure no uncommitted changes remain.
  * Cody posts a “MVP snapshot” message via MCP summarizing changes (function URL, passing tests).

---

## **📌 Conclusion**

These MVP-focused rules enable **Cody**, the AINative Studio Agent, to rapidly prototype using HyperScaler APIs for Compute, Storage, and Functions. By enforcing minimal code, leveraging managed services, and applying **Test-Driven/Behavior-Driven XP methods**, our team achieves the fastest, most stable MVPs with continuous feedback and real-time synchronization via MCP.
