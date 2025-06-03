# Enhanced Agentic Prompts Library

This document extends the existing Agentic Prompts Library by adding sections specifically focused on **Planning & Roadmapping** and **Reasoning & Chain-of-Thought**. These prompts guide agentic assistants to not only fetch and classify tasks but also plan multi-step workflows and demonstrate explicit reasoning when coding. A sample reasoning narrative is included to illustrate how an agent might think through a coding problem.

---

## 1. Planning & Roadmapping

1. **High-Level MVP Roadmap Creation**  
   > *“Given a new product concept (e.g., “real-time chat widget using serverless functions”), generate a 4-week MVP roadmap. Outline weekly goals, key deliverables, and dependencies. Present the plan as a markdown table with columns: Week, Goal, Deliverables, Dependencies.”*

2. **Sprint Breakdown from Roadmap**  
   > *“Take the 4-week roadmap you just created and break Week 2 into two 1-week sprints. For each sprint, list epics and associated user stories (in plain English). Label each story with a T-shirt size (S/M/L) and note any external HyperScaler APIs required.”*

3. **Dependency Graph Generation**  
   > *“From the user stories in a given sprint (provided as a list of bullet points), analyze and generate a dependency graph in DOT (Graphviz) format. Each node should represent a story, and directed edges indicate prerequisites. Return the DOT code only.”*

4. **Milestone & Release Checklist**  
   > *“Create a release checklist for Version 1.0.0 that includes:  
   > - Feature completion (list features)  
   > - All unit/integration tests passing  
   > - Security scan results approved  
   > - CI/CD pipeline configured for canary release  
   > - Documentation updated (README, API specs)  
   > Output as a markdown checklist with checkboxes.”*

5. **Risk Assessment & Mitigation Plan**  
   > *“Given the proposed plan, identify three major technical risks (e.g., latency when using serverless functions, storage quota limits) and propose mitigation strategies. Present each risk with: Description, Likelihood (High/Medium/Low), Mitigation Steps.”*

---

## 2. Reasoning & Chain-of-Thought

### 2.1 Prompts for Explicit Reasoning

1. **Explain Coding Decision (Chain-of-Thought)**  
   > *“You need to implement a function `sanitizeInput(input: string)` that removes any non-alphanumeric characters and trims whitespace. Describe your reasoning step-by-step (chain-of-thought) before writing the final JavaScript code. Include considerations for edge cases and performance.”*

2. **Design Data Model with Reasoning**  
   > *“Construct a simple data model for a “Task” entity (fields: `id`, `title`, `description`, `status`, `assignedTo`). Reason through field types, indexing strategy, and relations. Provide your reasoning narrative, then output the final model schema in SQL (PostgreSQL) syntax.”*

3. **Select Between Two API Designs**  
   > *“You have two options for implementing authentication:  
   > A) JWT tokens stored in localStorage  
   > B) Http-only cookies with session tokens  
   > Provide a chain-of-thought comparing both approaches in terms of security, ease of use, and HyperScaler compatibility. Then state a final recommendation.”*

4. **Optimize Performance with Reasoning**  
   > *“Given a function that reads from HyperScaler Storage on every HTTP request, reason step-by-step about caching strategies to improve performance and reduce costs. After reasoning, provide a code snippet implementing in-memory caching with eviction policy.”*

5. **Error Handling Strategy Explanation**  
   > *“When calling a HyperScaler Function from client code, what errors must you anticipate (e.g., timeouts, 5xx errors)? Reason through types of failures and describe a retry/backoff strategy. Then, write a small TypeScript utility function `retryWithBackoff(fn, attempts)` based on your reasoning.”*

---

### 2.2 Sample Reasoning Narrative

> **Task:** Implement `generatePresignedUrl(bucketName: string, objectKey: string): Promise<string>` using AWS S3 SDK (Node.js). The function should return a URL valid for 15 minutes that allows uploading to a specified bucket/object path.

**Chain-of-Thought Reasoning:**
1. **Clarify Requirements:**  
   - The function must generate a presigned URL specifically for uploads (PUT operation).  
   - The URL validity is 15 minutes (900 seconds).  
   - Input parameters: `bucketName` (string) and `objectKey` (string).  
   - Return type: `Promise<string>` that resolves to the URL.

2. **Choose SDK Method:**  
   - AWS SDK v3 provides `GetObjectCommand` for GET, but for uploading, we need `PutObjectCommand`.  
   - The presigner utility in v3 is `S3RequestPresigner` or using `@aws-sdk/s3-request-presigner`.  
   - The typical flow:  
     1. Create an `S3Client` instance (with region, credentials).  
     2. Create a `PutObjectCommand` with bucket and key.  
     3. Use `getSignedUrl(client, command, { expiresIn: 900 })` to generate URL.

3. **Initialize AWS Credentials & Config:**  
   - The client needs correct AWS credentials. In an MVP, rely on environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION`).  
   - Assume those are set in the deployment environment.

4. **Edge Cases & Validation:**  
   - If `bucketName` or `objectKey` is empty or contains illegal characters, throw early.  
   - Validate type: both inputs must be nonempty strings.  
   - If AWS credentials are missing, the SDK will throw; we can let that bubble up, or we can catch and wrap in a custom error.

5. **Performance & Security Considerations:**  
   - Generating a presigned URL is fast (no network call except to fetch region config), so no caching needed.  
   - Ensure the URL scope is minimal: only allow a PUT for exactly that key (no wildcard).  
   - Do not embed any secret in the URL beyond the presigned signature.

6. **Implementation Plan:**  
   - Write a TypeScript function with signature:  
     ```ts
     async function generatePresignedUrl(bucketName: string, objectKey: string): Promise<string> { ... }
     ```  
   - Inside: validate inputs → create `S3Client` → create `PutObjectCommand` → call `getSignedUrl`.  
   - Return the URL string.

7. **Testing Approach:**  
   - Unit test: mock `getSignedUrl` to return a dummy URL, verify function returns that URL when inputs are valid.  
   - Integration test (optional MVP): call the function with a test bucket/key and attempt a PUT request with the returned URL to ensure AWS allows upload.

---

### 2.3 Final Code Output (Based on Reasoning)

```ts
// src/utils/presigner.ts
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

const REGION = process.env.AWS_REGION;
if (!REGION) {
  throw new Error("AWS_REGION environment variable is required.");
}

/**
 * Generates a presigned URL for uploading to S3.
 *
 * @param bucketName - The name of the S3 bucket.
 * @param objectKey - The object key (path/filename) where upload will be stored.
 * @returns A Promise resolving to a presigned PUT URL valid for 15 minutes.
 */
export async function generatePresignedUrl(
  bucketName: string,
  objectKey: string
): Promise<string> {
  // Input validation
  if (typeof bucketName !== "string" || bucketName.trim() === "") {
    throw new Error("bucketName must be a non-empty string.");
  }
  if (typeof objectKey !== "string" || objectKey.trim() === "") {
    throw new Error("objectKey must be a non-empty string.");
  }

  // Initialize S3 client
  const s3 = new S3Client({ region: REGION });

  // Create PutObjectCommand
  const command = new PutObjectCommand({
    Bucket: bucketName,
    Key: objectKey,
  });

  // Generate presigned URL valid for 900 seconds (15 minutes)
  const presignedUrl = await getSignedUrl(s3, command, { expiresIn: 900 });
  return presignedUrl;
}
