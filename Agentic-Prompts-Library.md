# Agentic Prompts Library

This document contains a curated collection of LLM prompts that guide agentic assistants (e.g., Cody, Copilot, TabNine) through common development steps. Each section corresponds to a phase of the workflow, providing clear, actionable prompts to automate tasks via Model Context Protocol (MCP) or equivalent integrations.

---

## 1. Backlog Fetching / Classification

1. **Fetch Next MVP Feature**  
   > “Using MCP, query GitHub Issues for the highest-priority open issue labeled `mvp-feature`. Annotate the response with:  
   > - Issue number  
   > - Title and description  
   > - Labels and estimate (if present)  
   > - ‘Ready’ status or blocker notes  
   > Provide a concise summary in JSON format.”

2. **Classify Unlabeled Issue**  
   > “Given a GitHub Issue description fetched by MCP (title, body, comments), determine whether it belongs to one of:  
   > - `mvp-feature`  
   > - `mvp-bug`  
   > - `mvp-chore`  
   > Explain your reasoning, assign the correct label, and output the updated issue metadata (including any recommended estimate).”

3. **Annotate “In Progress” on an Issue**  
   > “For GitHub Issue #\<number\>, post an MCP comment marking it as ‘in progress’. Include:  
   > - Current timestamp  
   > - Agent username (‘Cody’)  
   > - A brief note: ‘Starting TDD scaffolding for MVP feature.’  
   > Return the updated issue JSON with status changes.”

4. **Filter Issues by Effort**  
   > “Fetch all open GitHub Issues labeled `mvp-feature`, then filter by estimated T-shirt size (`S`, `M`, `L`). Sort results from smallest to largest. Present the first three as JSON objects (issue number, title, labels, estimate).”

---

## 2. Test Scaffolding

1. **Generate Failing Unit Test (Red Phase)**  
   > “Given the MVP feature specification:  
   > - Handler endpoint `/ping` should return HTTP 200 with `{ "status": "ok" }`  
   > Write a failing test in Jest (Node) or pytest (Python) that asserts this behavior. Include minimal imports and test structure. Return the full test file content.”

2. **Generate Failing Integration Test (Red Phase)**  
   > “The MVP function `uploadFile` is expected to write a file to HyperScaler Storage and return a JSON response with `fileUrl`. Create a failing integration test (using SuperTest for Node or requests + pytest for Python) that:  
   > - Sends a POST request with a sample payload (`filename`, `content`).  
   > - Expects status code 200 and a JSON body containing `fileUrl`.  
   > Provide the test code with placeholder environment variables for the function URL.”

3. **Convert Pseudocode to Test Case**  
   > “Convert the following pseudocode into a BDD-style unit test using Jest or pytest:  
   > 1. Call `calculateTotal([1,2,3])`  
   > 2. Expect return value to be `6`  
   > Ensure the test follows Red–Green–Refactor conventions and includes descriptive `describe`/`it` blocks.”

4. **Add Mocks/Stubs to Unit Test**  
   > “Given the existing unit test for `sendNotification` that calls an external messaging service, insert mocks for the messaging API client (e.g., using `jest.mock` or `unittest.mock.patch`). Ensure the test still fails initially (Red) and include comments indicating where to implement stubbing logic.”

---

## 3. Refactoring Suggestions

1. **Identify Duplication and Extract Function**  
   > “Analyze the following code snippet and detect any duplicated logic across multiple functions. Propose a refactored version that extracts the common logic into a helper function named `sanitizeInput`. Provide both the diff and the final code block. Use MCP to output the patch.”

2. **Convert Callback to Async/Await**  
   > “Refactor this callback-based function to use async/await syntax. Ensure proper error handling (try/catch) and remove any nested callbacks. Provide the updated function code in a single file.”

3. **Simplify Complex Conditional**  
   > “The method `processOrder()` contains deeply nested `if/else` statements. Rewrite the function to simplify conditionals (e.g., via guard clauses or early returns). Show only the refactored version and annotate lines you removed or changed.”

4. **Enforce Minimal Import Strategy**  
   > “Given a file with twenty imports, identify unused or heavyweight modules. Refactor so that only the minimal HyperScaler SDK imports remain. Present a cleaned-up import block and highlight removed lines.”

---

## 4. CI/CD YAML Generation

1. **Generate GitHub Actions Workflow (MVP Deploy)**  
   > “Produce a GitHub Actions YAML file named `mvp-deploy.yml` that triggers on `push` to the `mvp` branch. Steps should include:  
   > 1. Checkout code  
   > 2. Install dependencies (`npm install` or `pip install -r requirements.txt`)  
   > 3. Run unit tests (`npm test` or `pytest`)  
   > 4. Deploy to HyperScaler Function (AWS Lambda / Azure Function / GCP Cloud Function) using CLI commands.  
   > 5. Run a smoke test via `curl` against the deployed endpoint.  
   > Embed environment variable placeholders (e.g., `${{ secrets.HYPERSCALER_ACCESS_KEY }}`).  
   > Ensure `fail-fast: true` and output status in MCP-friendly JSON.”

2. **Generate Terraform Snippet for Function Provisioning**  
   > “Create a minimal Terraform configuration to define:  
   > - A serverless function (AWS Lambda) using `aws_lambda_function` resource  
   > - An associated IAM role with basic execution permissions  
   > - A storage bucket (S3) for file persistence  
   > Include only necessary attributes: `function_name`, `handler`, `runtime`, `role`, `source_code_hash`, and `environment` variables. Return the `.tf` file content.”

3. **CI Lint Configuration**  
   > “Generate a GitHub Actions workflow step that lints code using ESLint (for JS/TS) or flake8 (for Python). The step should:  
   > - Run `npm run lint` or `flake8 src/`  
   > - Fail the job if any lint errors are found  
   > - Annotate lint errors in the PR check panel. Provide YAML snippet.”

4. **Automate Canary Deployment**  
   > “Write a GitHub Actions job that:  
   > 1. Deploys a new version of `my-function` to HyperScaler (e.g., AWS Lambda alias `canary`)  
   > 2. Waits 30 seconds, then pings the endpoint 5 times to verify HTTP 200  
   > 3. If all checks pass, shifts 25% of traffic to `canary` alias; otherwise, roll back.  
   > Provide the full YAML job definition, using placeholders for secrets and function names.”

---

## 5. Rollback and Monitoring Scripts

1. **Generate Rollback Script for Function**  
   > “Produce a shell script named `rollback.sh` that:  
   > 1. Reads the last stable function version from a JSON file (e.g., `stable_version.json`).  
   > 2. Calls the HyperScaler CLI (`aws lambda update-alias --name prod --function-version`) to point the `prod` alias back to the stable version.  
   > 3. Posts an MCP message summarizing the rollback result (success or error).  
   > Include placeholder for region and function name environment variables.”

2. **Create Smoke-Monitoring Workflow**  
   > “Write a Python script `monitor.py` that:  
   > - Reads environment variables: `FUNCTION_URL`, `ALERT_WEBHOOK`  
   > - Performs an HTTP GET to `FUNCTION_URL` every 60 seconds  
   > - On non-200 response or timeout, sends a POST to `ALERT_WEBHOOK` with a JSON payload: `{ "status": "failure", "timestamp": "<ISO-8601>" }`  
   > - Logs successes and failures locally (e.g., append to `monitor.log`).  
   > Use standard libraries only (`requests` is allowed if installed).”

3. **Automated MCP Notification on Failure**  
   > “Within the `monitor.py` script, integrate an MCP HTTP call to `http://localhost:8000/mcp` whenever a failure alert is sent. The payload should include:  
   > - `action`: “function-failure”  
   > - `functionName`: from env var `FUNCTION_NAME`  
   > - `timestamp`: current UTC ISO-8601  
   > - `details`: HTTP status code or timeout message  
   > Provide the MCP JSON snippet and show where to insert it in the script.”

4. **Generate Metrics Dashboard Template**  
   > “Using Markdown and embedded shell blocks, create a dashboard README `metrics-dashboard.md` that:  
   > - Lists commands to fetch invocation count (`aws cloudwatch get-metric-statistics` or equivalent)  
   > - Includes example output parsing (e.g., using `jq`)  
   > - Shows how to calculate a simple error rate (failed_invocations / total_invocations * 100)  
   > - Provides instructions to schedule this script via cron or GitHub Actions.  
   > Ensure readability and include MCP annotations for when metrics cross thresholds.”

---

### End of Agentic Prompts Library
