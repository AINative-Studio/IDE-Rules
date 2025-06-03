```markdown
# Agentic Development Rules Repository

This repository hosts a collection of coding‐standards and workflow “rules” documents designed to guide agent‐driven, XP‐oriented development workflows. While originally tailored for **AINative Studio** and its embedded agent **Cody**, these rules can be reused or adapted in any popular IDE that supports agentic assistants (e.g., VS Code with AI extensions, IntelliJ IDEA with “IntelliCode,” or other environments that support Model Context Protocol–driven agents).

---

## Table of Contents

1. [Purpose & Overview](#purpose--overview)  
2. [Repository Structure](#repository-structure)  
3. [Getting Started](#getting-started)  
   - [Cloning the Repo](#cloning-the-repo)  
   - [Rule Document Formats](#rule-document-formats)  
4. [Usage in AINative Studio](#usage-in-ainative-studio)  
   - [Configuring Cody](#configuring-cody)  
   - [MCP Integration](#mcp-integration)  
5. [Usage in Other IDEs](#usage-in-other-ides)  
   - [VS Code (with AI Extensions)](#vs-code-with-ai-extensions)  
   - [IntelliJ IDEA / WebStorm](#intellij-idea--webstorm)  
   - [Other Agentic‐Enabled IDEs](#other-agentic-enabled-ides)  
6. [Documentation & Rule Sets](#documentation--rule-sets)  
   - [Core “MVP‐Focused” Rules](#core-mvp-focused-rules)  
   - [Prototype Mode vs. Full Standards](#prototype-mode-vs-full-standards)  
7. [Contributing Guidelines](#contributing-guidelines)  
8. [Versioning & Releases](#versioning--releases)  
9. [License](#license)

---

## Purpose & Overview

When building software in an XP (Extreme Programming) style with heavy reliance on agentic AI (Model Context Protocol–driven assistants), developers need **machine‐readable, prescriptive guidelines** that codify:

- **Backlog Management** (using GitHub Issues + MCP)  
- **Branching & Naming Conventions**  
- **Minimal‐Code Prototyping** (leveraging HyperScaler Functions, Storage, Compute)  
- **Test-Driven & Behavior-Driven Development** (TDD/BDD)  
- **CI/CD Pipelines** (lightweight, rapid MVP deployments)  
- **Version Control Best Practices**

These rule sets empower any agentic assistant—whether “Cody” in AINative Studio or an AI plugin in VS Code—to automatically:

1. **Fetch, classify, and update backlog items** in real time via MCP.  
2. **Generate failing tests (Red)**, implement minimal code (Green), and refactor.  
3. **Open and update pull requests** with automated comments, coverage reports, and deployment URLs.  
4. **Enforce naming, formatting, and minimal‐dependency guidelines** for rapid prototypes.  
5. **Trigger HyperScaler‐based deployments** (e.g., AWS Lambda, Azure Functions, GCP Functions) from a single code push.  

Even if your organization does **not** use AINative Studio, adopting these rule sets in any IDE that supports Model Context Protocol or similar agentic protocols will standardize your team’s rapid MVP workflows.

---

## Repository Structure

```

/agentic-rules
├── README.md
├── docs/
│   ├── MVP-Focused-Rules.md
│   ├── Full-Coding-Standards.md
│   ├── HyperScaler-Integration-Guide.md
│   ├── Example-MCP-Scripts.md
│   └── Agentic-Prompts-Library.md
├── examples/
│   ├── sample-workflow\.vscode.json
│   ├── sample-workflow\.intellij.xml
│   ├── mcp-snippet.yaml
│   └── deploy-function-template.tf
└── CONTRIBUTING.md

````

- **`docs/`**: Contains all rule documents in Markdown.  
  - **`MVP-Focused-Rules.md`**: “Minimal Code, Rapid Prototyping” rule set.  
  - **`Full-Coding-Standards.md`**: Comprehensive XP‐oriented coding standards.  
  - **`HyperScaler-Integration-Guide.md`**: Detailed instructions for using Compute/Storage/Function APIs from major cloud providers.  
  - **`Example-MCP-Scripts.md`**: Sample YAML/JSON snippets demonstrating how to send MCP messages for backlog updates, PR comments, and test scaffolding.  
  - **`Agentic-Prompts-Library.md`**: A catalog of LLM prompts designed to drive the agent through each stage of the workflow (backlog, TDD, CI/CD, release).  

- **`examples/`**: Contains IDE‐specific configuration or sample files.  
  - **`sample-workflow.vscode.json`**: A sample VS Code tasks and settings file showing how to call MCP scripts.  
  - **`sample-workflow.intellij.xml`**: Equivalent for IntelliJ IDEA or WebStorm.  
  - **`mcp-snippet.yaml`**: A reusable MCP message template (e.g., for “Mark issue as in progress”).  
  - **`deploy-function-template.tf`**: A minimal Terraform snippet for deploying a serverless function in an MVP style.

- **`CONTRIBUTING.md`**: Guidelines for submitting improvements, new examples, or updated rules.

---

## Getting Started

### Cloning the Repo

```bash
git clone https://github.com/YourOrg/agentic-rules.git
cd agentic-rules
````

### Rule Document Formats

All documents live under `docs/` in Markdown format. You can open them directly in any text editor or IDE. If your agentic plugin supports “live rule loading,” point the plugin’s “rules directory” to `/agentic-rules/docs/` to enable automated enforcement.

---

## Usage in AINative Studio

If you are using **AINative Studio** (which includes the **Cody** Agent), follow these steps:

1. **Open the Rules Directory**

   * In AINative Studio, go to **File → Open Folder** and select `/agentic-rules/docs/`.
   * Cody will automatically ingest all `.md` files as “guidelines” for each development phase.

2. **Configure MCP Endpoint**

   * Open `settings.json` (AINative Studio’s IDE settings) and add:

     ```jsonc
     {
       "cody.mcpEndpoint": "http://localhost:8000/mcp",
       "cody.rulesDir": "/absolute/path/to/agentic-rules/docs"
     }
     ```
   * Adjust `mcpEndpoint` to point to your local or hosted MCP service.

3. **Backlog Integration**

   * In AINative Studio’s “Backlog” panel, configure GitHub credentials and set “Rules Source” to use `docs/MVP-Focused-Rules.md` or `docs/Full-Coding-Standards.md`.
   * Cody will automatically read “LLM Prompts” from those docs to fetch and classify issues.

4. **Agentic Workflow**

   * When you create or update a GitHub Issue labeled `mvp-feature`, Cody will invoke the MCP snippet defined in `docs/Example-MCP-Scripts.md` to mark it “in progress,” generate a failing test, and open a new branch.
   * As you type code, Cody will reference the “Coding Style Guidelines” in `docs/Full-Coding-Standards.md` or “Minimal Code Guidelines” in `docs/MVP-Focused-Rules.md` to auto-suggest best practices.
   * On `git push`, Cody triggers the CI/CD pipeline (via `sample-workflow.vscode.json` tasks) and uses HyperScaler integration scripts from `docs/HyperScaler-Integration-Guide.md` to deploy the function.

---

## Usage in Other IDEs

These rules are IDE-agnostic as long as the IDE supports an agentic assistant or can run MCP scripts. Below are integration notes for popular environments:

### VS Code (with AI Extensions)

1. **Install an MCP-Capable Extension**

   * Examples:

     * “AI Codex” (supports MCP)
     * “GitHub Copilot Labs” (can be configured to forward prompts)
   * Configure extension to call your MCP endpoint (e.g., via `settings.json`):

     ```jsonc
     {
       "aiAssistant.mcpEndpoint": "http://localhost:8000/mcp",
       "aiAssistant.rulesDir": "${workspaceFolder}/agentic-rules/docs"
     }
     ```

2. **Task & Launch Configuration**

   * Copy `examples/sample-workflow.vscode.json` into your project’s `.vscode/` folder.
   * It defines tasks for:

     * Running a local MCP server.
     * Linting/Formatting according to rule docs.
     * Deploying to HyperScaler using CLI commands.

3. **Agentic Prompts**

   * Whenever you open an issue or create a PR in VS Code’s GitHub integration panel, your AI extension can fetch “LLM Prompts” directly from `docs/Agentic-Prompts-Library.md`.
   * Example: invoke a command “Fetch Next MVP Issue” to let the agent highlight the top GitHub Issue.

### IntelliJ IDEA / WebStorm

1. **Install an AI Assistant Plugin**

   * Examples:

     * “Tabnine Enterprise” (supports custom rule integration)
     * “CodeWhisperer” (with prompt customization)
   * In **Settings → Tools → AI Assistant**, set:

     ```
     MCP Endpoint: http://localhost:8000/mcp  
     Rules Directory: <path>/agentic-rules/docs
     ```

2. **File Watchers & Live Templates**

   * Use **Live Templates** to insert standardized MCP snippets (e.g., marking an issue “in progress”).
   * Copy `examples/mcp-snippet.yaml` into your project and reference it in **File Watchers** so that certain keywords (like `@MCP:StartIssue`) auto-expand to the full YAML.

3. **CI/CD Integration**

   * Use **Run Configurations** for “Deploy MVP Function.”
   * Follow instructions in `docs/HyperScaler-Integration-Guide.md` to set up Environment variables and service endpoints.

### Other Agentic-Enabled IDEs

* **Cursor, Windsurf, Augment, Tessl** (with community LLM/agent plugins):

  1. Configure your plugin’s “prompt library path” to `/agentic-rules/docs/`.
  2. Provide MCP endpoint in plugin settings.
* **Cloud-Based Editors** (GitHub Codespaces, Gitpod):

  1. Mount this repository into your codespace (e.g., via a `git submodule`).
  2. Set environment variable `MCP_RULES_PATH=/workspaces/agentic-rules/docs`.
  3. Agent processes and enforces rules in real time.

---

## Documentation & Rule Sets

### Core “MVP-Focused” Rules

File: `docs/MVP-Focused-Rules.md`

* **Backlog Management** (GitHub Issues + MCP)
* **Minimal TDD/BDD** (single-file functions, HyperScaler SDK calls)
* **MVP CI/CD** (GitHub Actions for functions)
* **Branch & Naming Conventions** (`feature/mvp-*`)

### Full “XP-Oriented” Coding Standards

File: `docs/Full-Coding-Standards.md`

* **Comprehensive Backlog Workflows** (labels: `feature`, `bug`, `chore`)
* **Detailed TDD/BDD Practices** (unit, integration, functional tests)
* **CI/CD Pipelines** (multi-stage, staging→production)
* **Code Style & Security** (type annotations, static analysis tools)

### HyperScaler Integration Guide

File: `docs/HyperScaler-Integration-Guide.md`

* Walkthroughs for AWS, Azure, GCP:

  1. **Compute** (EC2, App Service, Cloud Run)
  2. **Functions** (AWS Lambda, Azure Functions, GCP Cloud Functions)
  3. **Storage** (S3, Blob Storage, Cloud Storage)
* **Terraform templates** (`deploy-function-template.tf`) for quick provisioning.
* **Example CLI commands** for deployment, environment management, and secret injection.

### Example MCP Scripts

File: `docs/Example-MCP-Scripts.md`

* YAML/JSON templates for:

  * Mark issue “in progress”
  * Post a failing-test comment
  * Annotate code coverage results
  * Close or transition issues upon merge
* Sample binds for both AINative “Cody” and generic MCP clients.

### Agentic Prompts Library

File: `docs/Agentic-Prompts-Library.md`

* Curated LLM prompts for each step:

  * **Backlog fetching / classification**
  * **Test scaffolding**
  * **Refactoring suggestions**
  * **CI/CD YAML generation**
  * **Rollback and monitoring scripts**

---

## Contributing Guidelines

If you’d like to suggest a new rule, update an existing one, or add an example for a new IDE:

1. **Fork the repository** and create a feature branch (`git checkout -b update-rule-xyz`).
2. **Modify or create** Markdown files under `docs/`.
3. **Add examples** under `examples/` (if applicable).
4. **Update** `CONTRIBUTING.md` with any new instructions for your change.
5. **Submit a Pull Request**, describing:

   * What was changed or added.
   * How it integrates with MCP or agentic workflows.
   * Any usage examples.

Reviewers (Cody or human maintainers) will verify the new rules, run through sample workflows, and merge.

---

## Versioning & Releases

This repository follows **Semantic Versioning** (MAJOR.MINOR.PATCH). Release tags appear as `v1.0.0`, `v1.1.0`, etc. Each release includes:

* Updated rule documents in `docs/`.
* New examples or templates in `examples/`.
* Changelog entries summarizing additions/fixes.

Use GitHub Releases to track major changes, new IDE integrations, or protocol updates.

---

## License

This repository is licensed under the **MIT License**. See [LICENSE](LICENSE) for full details.

---

**Happy Agentic Development!**
Empower your AI assistants (Cody, Copilot, TabNine, etc.) to enforce these rules automatically and accelerate your MVP and full-scale software delivery.
