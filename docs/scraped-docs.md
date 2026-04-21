# Castari — Scraped Documentation

**Source:** https://castari.com
**Pages scraped:** 15
**Total characters:** 49396

---

## 

**URL:** https://docs.castari.com/cli/overview.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# CLI Overview
> The cast command-line interface
# CLI Overview
The `cast` CLI is the fastest way to deploy and manage agents on Castari.
## Installation
```bash theme={null}
npm install -g @castari/cli
```
## Commands
| Command                                                      | Description                        |
| ------------------------------------------------------------ | ---------------------------------- |
| [`cast login`](/cli/login)                                   | Authenticate with Castari          |
| [`cast logout`](/cli/login#logout)                           | Clear stored credentials           |
| [`cast whoami`](/cli/whoami)                                 | Show current user                  |
| [`cast init`](/cli/init)                                     | Create a new agent from a template |
| [`cast deploy`](/cli/deploy)                                 | Deploy an agent                    |
| [`cast stop`](/cli/stop)                                     | Stop a running agent               |
| [`cast invoke`](/cli/invoke)                                 | Invoke a deployed agent            |
| [`cast agents list`](/cli/agents)                            | List all agents                    |
| [`cast agents get`](/cli/agents#cast-agents-get)             | Get agent details                  |
| [`cast agents update`](/cli/agents)                          | Update agent configuration         |
| [`cast agents delete`](/cli/agents#cast-agents-delete)       | Delete an agent                    |
| [`cast secrets list`](/cli/secrets)                          | List secrets for an agent          |
| [`cast secrets set`](/cli/secrets#cast-secrets-set)          | Set a secret                       |
| [`cast secrets delete`](/cli/secrets#cast-secrets-delete)    | Delete a secret                    |
| [`cast apikey list`](/cli/apikey)                            | List API keys                      |
| [`cast apikey create`](/cli/apikey#cast-apikey-create)       | Create a new API key               |
| [`cast apikey revoke`](/cli/apikey#cast-apikey-revoke)       | Revoke an API key                  |
| [`cast usage`](/cli/usage)                                   | Show usage statistics              |
| [`cast buckets`](/cli/buckets)                               | Manage storage buckets             |
| [`cast mounts`](/cli/mounts)                                 | Mount buckets to agents            |
| [`cast files`](/cli/files)                                   | Manage files in storage            |
| [`cast sessions list`](/cli/sessions)                        | List agent sessions                |
| [`cast sessions delete`](/cli/sessions#cast-sessions-delete) | Delete a session                   |
| [`cast invocations list`](/cli/agents)                       | List invocation history            |
## Global Options
All commands support these options:
| Option      | Description             |
| ----------- | ----------------------- |
| `--help`    | Show help for a command |
| `--version` | Show CLI version        |
## Configuration
The CLI stores configuration in `~/.castari/`:
```
~/.castari/
├── config.json      # API URL, preferences
└── credentials.json # Auth tokens (do not share!)
```
## Environment Variables
| Variable          | Description                  |
| ----------------- | ---------------------------- |
| `CASTARI_API_URL` | Override API base URL        |
| `CASTARI_API_KEY` | Use API key instead of OAuth |
## Examples
```bash theme={null}
# Full workflow
cast login
cast init my-agent
cd my-agent
cast deploy
cast invoke my-agent "Hello!"
# Check agent status
cast agents get my-agent
# Add a secret
cast secrets set my-agent OPENAI_API_KEY sk-xxx
```

---

## 

**URL:** https://docs.castari.com/concepts/agents.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Agents
> What agents are and how they work
# Agents
An agent is your Claude-powered application deployed on Castari.
## What is an Agent?
An agent is a program that:
1. Receives a **prompt** as input
2. Uses **Claude** to reason and decide on actions
3. Executes **tools** to interact with the world
4. Returns a **response**
Agents can do things like:
* Read and write files
* Execute shell commands
* Search the web
* Query databases
* Call external APIs
## Agent Structure
Every Castari agent has this structure:
```
my-agent/
├── castari.json      # Agent configuration
├── package.json      # Dependencies
├── tsconfig.json     # TypeScript config
├── src/
│   └── index.ts      # Entry point
├── CLAUDE.md         # Agent instructions
└── README.md         # Documentation
```
### castari.json
The agent configuration file:
```json theme={null}
{
"name": "my-agent",
"version": "0.1.0",
"entrypoint": "src/index.ts",
"runtime": "node"
}
```
| Field        | Required | Description       |
| ------------ | -------- | ----------------- |
| `name`       | Yes      | Display name      |
| `version`    | Yes      | Semantic version  |
| `entrypoint` | Yes      | Path to main file |
| `runtime`    | Yes      | Runtime (`node`)  |
### CLAUDE.md
Instructions for Claude that shape the agent's behavior:
```markdown theme={null}
# Research Agent
You are a research assistant that finds and synthesizes information.
## Guidelines
- Always cite sources
- Present multiple perspectives
- Summarize findings at the end
```
## Agent Lifecycle
```
draft → deploying → active → stopped
↓
error
```
| Status      | Description                          |
| ----------- | ------------------------------------ |
| `draft`     | Created but not yet deployed         |
| `deploying` | Deployment in progress               |
| `active`    | Running and accepting invocations    |
| `stopped`   | Manually stopped (can be redeployed) |
| `error`     | Deployment or runtime failure        |
## Slugs and Naming
Every agent has a **slug** — a URL-safe identifier:
* Slugs are lowercase alphanumeric with hyphens
* Example: `my-agent`, `research-bot-v2`
* Slugs must be unique per user
* Used in CLI commands: `cast invoke my-agent "Hello"`
## Entry Point Contract
Your agent's entry point must:
1. **Read prompt from stdin**
2. **Process with Claude and tools**
3. **Write response to stdout**
```typescript theme={null}
// Read
let prompt = "";
process.stdin.on("data", (chunk) => (prompt += chunk));
process.stdin.on("end", async () => {
// Process
const response = await runAgent(prompt);
// Write
console.log(response);
});
```
## Tools
Agents use tools to take actions. Tools are defined as schemas:
```typescript theme={null}
const tools: Anthropic.Tool[] = [
{
name: "read_file",
description: "Read the contents of a file",
input_schema: {
type: "object",
properties: {
path: { type: "string" }
},
required: ["path"]
}
}
];
```
When Claude decides to use a tool, your code executes it:
```typescript theme={null}
async function handleTool(name: string, input: any): Promise
{
switch (name) {
case "read_file":
return fs.readFileSync(input.path, "utf-8");
// ... other tools
}
}
```
## See Also
Build and customize agents
Start with pre-built agents
Build from scratch
Execution environment

---

## 

**URL:** https://docs.castari.com/concepts/files.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Managed Files
> Simple file storage for agent data
# Managed Files
Castari provides zero-configuration file storage for your agents. Upload files, attach them to agents, and they automatically appear in the sandbox.
## Why Managed Files?
Your agents often need access to:
* Data files (CSV, JSON, etc.)
* Configuration files
* Documents for processing
* Reference materials
Managed files let you provide these **simply** without:
* Setting up cloud storage buckets
* Managing credentials
* Configuring mount paths manually
## How It Works
```mermaid theme={null}
graph LR
A[Upload File] --> B[Attach to Agent]
B --> C[Auto-mount in Sandbox]
C --> D[Agent reads /files/agent/]
```
1. **Upload** — Files go to Castari's managed storage
2. **Attach** — Link files to specific agents
3. **Mount** — Files automatically appear at `/files/agent/` in the sandbox
## File Scopes
Files can be scoped for different uses:
| Scope     | Description                  | Mount Path          |
| --------- | ---------------------------- | ------------------- |
| `user`    | Your personal files          | Available to attach |
| `agent`   | Attached to a specific agent | `/files/agent/`     |
| `session` | Per-invocation outputs       | `/files/session/`   |
## Uploading Files
### Via CLI
```bash theme={null}
cast files upload data.csv --description "Training data"
```
### Via Dashboard
1. Navigate to **Files** in the sidebar
2. Click **Upload File**
3. Select file and add optional description/tags
### Via SDK
```typescript theme={null}
const result = await client.files.upload(fileBlob, 'data.csv', {
description: 'Training data',
tags: ['dataset', 'csv'],
});
console.log(result.file_id); // file_abc123
```
## Attaching Files to Agents
Once uploaded, attach files to agents:
### Via CLI
```bash theme={null}
cast agents files add my-agent file_abc123
```
### Via Dashboard
1. Go to **Agents** → your agent
2. Find the **Attached Files** section
3. Click **Attach File** and select from your files
### Via SDK
```typescript theme={null}
await client.files.attachToAgent('my-agent', {
fileId: 'file_abc123',
readOnly: true,
});
```
## Using Files in Agents
Attached files are available at `/files/agent/`:
```typescript theme={null}
import { readFileSync } from 'fs';
// Read attached file
const data = readFileSync('/files/agent/data.csv', 'utf-8');
// Parse and process
const rows = data.split('\n').map(line => line.split(','));
```
Files are read-only by default. Use `--writable` flag or set `readOnly: false` to allow writes.
## Storage Limits
| Plan       | Storage Limit |
| ---------- | ------------- |
| Free       | 100 MB        |
| Pro        | 10 GB         |
| Enterprise | Custom        |
Check your usage:
```bash theme={null}
cast files usage
```
```
Total Files    12
Total Size     45.2 MB
Quota Used     45.2%
Quota Limit    100 MB
████████████████████░░░░░░░░░░░░░░░░░░░░ 45.2%
```
## Managed vs. BYO Storage
Castari supports both managed files and bring-your-own storage buckets:
| Feature  | Managed Files    | BYO Buckets           |
| -------- | ---------------- | --------------------- |
| Setup    | Zero config      | Configure credentials |
| Storage  | Castari hosted   | Your S3/GCS/R2        |
| Cost     | Included in plan | Your cloud costs      |
| Use case | Simple files     | Large datasets        |
Use managed files for simplicity. Use [Storage Buckets](/guides/storage) for large datasets or when you need files in your own cloud.
## File Metadata
Each file tracks:
* **Filename** — Original name
* **Size** — File size in bytes
* **Content Type** — MIME type
* **SHA256 Hash** — Integrity verification
* **Description** — Optional description
* **Tags** — Optional tags for organization
## Best Practices
### Organization
* **Use descriptions** — Document what each file is for
* **Tag consistently** — Use tags like `dataset`, `config`, `reference`
* **Clean up** — Delete files you no longer need
### Security
* **Don't upload secrets** — Use [Secrets](/concepts/secrets) for credentials
* **Verify hashes** — Check SHA256 for critical files
* **Review attachments** — Only attach files agents actually need
### Performance
* **Keep files small** — For large datasets, use BYO storage
* **Use appropriate formats** — CSV/JSON for structured data
* **Consider caching** — Files are cached in the sandbox
## See Also
CLI reference
BYO storage setup

---

## 

**URL:** https://docs.castari.com/concepts/how-it-works.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# How Castari Works
> Architecture and key concepts
# How Castari Works
Understand how Castari deploys and runs your agents.
## Architecture Overview
```
┌─────────────┐      ┌──────────────────┐      ┌─────────────┐
│   You       │      │  Castari API     │      │  E2B        │
│  (CLI/SDK)  │ ───▶ │  (Control Plane) │ ───▶ │  Sandboxes  │
└─────────────┘      └──────────────────┘      └─────────────┘
│
┌─────────┼─────────┐
▼         ▼         ▼
PostgreSQL    Redis    Clerk Auth
```
**Control Plane** — Manages agents, secrets, invocations, and usage tracking.
**E2B Sandboxes** — Isolated execution environments where your agents run.
## Deployment Flow
When you run `cast deploy`:
Your agent code is uploaded to Castari (or cloned from git).
An isolated E2B sandbox is created for your agent.
`npm install` runs inside the sandbox.
Any secrets you've set become environment variables.
Agent status becomes `active`. Ready to invoke.
## Invocation Flow
When you run `cast invoke`:
Your prompt is sent to the Castari API.
A fresh sandbox is spun up for this request.
Your agent code executes with the prompt as input.
Output is captured and returned to you.
Sandbox is destroyed. No state persists.
## Per-Request Scaling
Every invocation gets a **fresh sandbox**. This means:
* **No state leaks** — Each request is isolated
* **True security** — No cross-request data exposure
* **Automatic scaling** — Parallel invocations run in parallel sandboxes
* **No cold starts** — Sandboxes are pre-warmed
## Agent Entry Point Contract
Your agent communicates via stdin/stdout:
```
Input:  prompt → stdin
Output: response → stdout
```
Example:
```typescript theme={null}
// Read prompt from stdin
let prompt = "";
for await (const chunk of process.stdin) {
prompt += chunk;
}
// Process and respond
const response = await runAgent(prompt);
// Write to stdout
console.log(response);
```
## Security Model
| Layer   | Protection                             |
| ------- | -------------------------------------- |
| Sandbox | Isolated E2B container                 |
| Network | Egress allowed, no ingress             |
| Secrets | Encrypted at rest, injected at runtime |
| Code    | Your code, your sandbox, not shared    |
## Resource Limits
| Resource | Limit       |
| -------- | ----------- |
| Memory   | 2 GB        |
| CPU      | 2 cores     |
| Timeout  | 120 seconds |
| Disk     | 10 GB       |
Contact us if you need higher limits for production workloads.
## See Also
Agent structure and lifecycle
E2B sandbox details
Environment variable management
Request/response model

---

## 

**URL:** https://docs.castari.com/concepts/invocations.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Invocations
> How agent requests and responses work
# Invocations
An invocation is a single request-response cycle with an agent.
## What is an Invocation?
When you run:
```bash theme={null}
cast invoke my-agent "What files are here?"
```
You create an **invocation** — a record of:
* The prompt you sent
* The response you received
* Tokens used and cost
* Execution time
* Status (completed, failed)
## Invocation Flow
```
┌──────────┐    prompt    ┌──────────────┐    stdin    ┌─────────┐
│   You    │ ──────────▶  │  Castari API │ ─────────▶  │  Agent  │
└──────────┘              └──────────────┘             └─────────┘
▲                           │                          │
│         response          │         stdout           │
└───────────────────────────┴──────────────────────────┘
```
1. **Request** — You send a prompt via CLI, SDK, or API
2. **Routing** — Castari routes to your agent's sandbox
3. **Execution** — Agent processes prompt, uses tools, generates response
4. **Response** — Output captured and returned to you
5. **Tracking** — Tokens, cost, and duration recorded
## Input and Output
### Input
Your prompt is passed to the agent via **stdin**:
```typescript theme={null}
// Agent reads from stdin
let prompt = "";
process.stdin.on("data", (chunk) => (prompt += chunk));
process.stdin.on("end", () => {
// Process prompt
});
```
### Output
Agent response is written to **stdout**:
```typescript theme={null}
// Agent writes to stdout
console.log(response);
```
Only stdout is captured as the response. stderr is logged but not returned.
## Invocation Result
Every invocation returns:
```typescript theme={null}
{
invocation_id: "inv_abc123",
session_id: "sess_xyz789",
response_content: "The current directory contains...",
input_tokens: 124,
output_tokens: 1110,
total_cost_usd: 0.02,
duration_ms: 2341,
status: "completed"
}
```
| Field              | Description             |
| ------------------ | ----------------------- |
| `invocation_id`    | Unique identifier       |
| `session_id`       | Session identifier      |
| `response_content` | Agent's output          |
| `input_tokens`     | Tokens in the prompt    |
| `output_tokens`    | Tokens in the response  |
| `total_cost_usd`   | Total cost in USD       |
| `duration_ms`      | Execution time          |
| `status`           | `completed` or `failed` |
## Statuses
| Status      | Description                 |
| ----------- | --------------------------- |
| `completed` | Agent finished successfully |
| `failed`    | Agent encountered an error  |
## Timeouts
Default timeout is **120 seconds** (2 minutes). Maximum timeout: 600 seconds (10 minutes). Timeouts result in a `failed` status with an appropriate error message.
## Cost Tracking
Costs are calculated based on:
* **Input tokens** — Prompt + system instructions
* **Output tokens** — Agent's response
* **Model** — Claude pricing tiers
View your usage:
```bash theme={null}
cast usage
```
## Concurrency
Multiple invocations can run **in parallel**:
```typescript theme={null}
// These run concurrently in separate sandboxes
const results = await Promise.all([
client.agents.invoke('my-agent', { prompt: 'Task 1' }),
client.agents.invoke('my-agent', { prompt: 'Task 2' }),
client.agents.invoke('my-agent', { prompt: 'Task 3' }),
]);
```
Each gets its own isolated sandbox.
## Error Handling
### In CLI
```bash theme={null}
cast invoke my-agent "Bad request"
# Error: Agent execution failed
```
### In SDK
```typescript theme={null}
try {
const result = await client.agents.invoke('my-agent', { prompt });
} catch (error) {
if (error instanceof CastariError) {
console.log(error.status);  // 500
console.log(error.message); // "Agent execution failed"
}
}
```
## See Also
CLI reference
SDK reference
Troubleshooting tips

---

## 

**URL:** https://docs.castari.com/concepts/sandboxes.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Sandboxes
> Isolated execution environments for agents
# Sandboxes
Sandboxes are isolated environments where your agents run.
## What is a Sandbox?
A sandbox is a secure, isolated container that:
* Runs your agent code
* Has its own filesystem
* Has network access (egress only)
* Is completely isolated from other sandboxes
Castari uses [E2B](https://e2b.dev) for sandbox infrastructure.
## Per-Request Sandboxes
Every invocation gets a **fresh sandbox**:
```
Request 1 → Sandbox A → Response 1 → Sandbox A destroyed
Request 2 → Sandbox B → Response 2 → Sandbox B destroyed
Request 3 → Sandbox C → Response 3 → Sandbox C destroyed
```
This provides:
* **Security** — No data persists between requests
* **Isolation** — One request can't affect another
* **Scalability** — Parallel requests run in parallel sandboxes
## Sandbox Lifecycle
### During Deployment
1. New sandbox created
2. Agent code uploaded
3. `npm install` executed
4. Secrets injected as environment variables
5. Sandbox kept warm for invocations
### During Invocation
1. Warm sandbox receives request
2. Agent process started with prompt on stdin
3. Agent executes (Claude + tools)
4. Response captured from stdout
5. Sandbox state reset for next request
### On Redeploy
1. Old sandbox destroyed
2. New sandbox created with updated code
3. All state from old sandbox is lost
## Security Model
### Isolation
Each sandbox is a separate container with:
* Own filesystem
* Own process namespace
* Own network namespace
* No access to host system
### Network
| Direction         | Allowed                         |
| ----------------- | ------------------------------- |
| Outbound (egress) | Yes — can call external APIs    |
| Inbound (ingress) | No — cannot receive connections |
### Filesystem
* Agents can read/write within their sandbox
* No access to other sandboxes
* State does not persist between invocations
## Resource Limits
| Resource | Default Limit | Notes          |
| -------- | ------------- | -------------- |
| Memory   | 2 GB          | Per sandbox    |
| CPU      | 2 cores       | Shared         |
| Timeout  | 120 seconds   | Per invocation |
| Disk     | 10 GB         | Ephemeral      |
Need higher limits? Contact us for enterprise plans.
## Preinstalled Software
Sandboxes come with:
* Node.js 20
* npm
* git
* Common utilities (curl, wget, etc.)
## Debugging Sandbox Issues
### Timeout Errors
If your agent times out:
1. Check for infinite loops
2. Optimize slow operations
3. Break complex tasks into smaller steps
### Out of Memory
If your agent runs out of memory:
1. Process data in chunks
2. Avoid loading large files entirely into memory
3. Clean up resources after use
### Network Errors
If external API calls fail:
1. Check the API is accessible
2. Verify credentials are set as secrets
3. Check for rate limiting
## E2B vs Other Sandboxing
Why E2B?
| Feature      | E2B    | Docker  | Firecracker |
| ------------ | ------ | ------- | ----------- |
| Startup time | \~1s   | \~5s    | \~125ms     |
| Isolation    | Strong | Medium  | Strong      |
| API          | Simple | Complex | Complex     |
| Managed      | Yes    | No      | No          |
## See Also
Architecture overview
Troubleshooting tips

---

## 

**URL:** https://docs.castari.com/concepts/secrets.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Secrets
> Managing environment variables for agents
# Secrets
Secrets are environment variables injected into your agent at runtime.
## Why Secrets?
Your agents often need access to:
* API keys (OpenAI, Stripe, etc.)
* Database credentials
* Service tokens
* Configuration values
Secrets let you provide these **securely** without:
* Hardcoding in source code
* Committing to git
* Exposing in logs
## Setting Secrets
### Via CLI
```bash theme={null}
cast secrets set my-agent OPENAI_API_KEY sk-abc123...
```
### Via SDK
```typescript theme={null}
await client.secrets.set('my-agent', 'OPENAI_API_KEY', 'sk-abc123...');
```
### Interactive Mode
Avoid exposing secrets in shell history:
```bash theme={null}
cast secrets set my-agent OPENAI_API_KEY
# Prompts for value securely
```
## Using Secrets in Agents
Secrets are available as environment variables:
```typescript theme={null}
// Access in your agent code
const openaiKey = process.env.OPENAI_API_KEY;
const databaseUrl = process.env.DATABASE_URL;
// Use with OpenAI
const openai = new OpenAI({
apiKey: process.env.OPENAI_API_KEY,
});
```
## Built-in Secrets
Castari automatically provides:
| Variable            | Description                       |
| ------------------- | --------------------------------- |
| `ANTHROPIC_API_KEY` | Your Anthropic API key for Claude |
You don't need to set `ANTHROPIC_API_KEY` — Castari injects it automatically so your agents can use Claude.
## Secret Storage
Secrets are:
* **Encrypted at rest** — Using AES-256
* **Never logged** — Values are masked in logs
* **Never returned** — API only returns key names, not values
* **Scoped per agent** — Each agent has its own secrets
## Listing Secrets
```bash theme={null}
cast secrets list my-agent
```
```
KEY                  CREATED
OPENAI_API_KEY       2024-01-15 10:30:00
DATABASE_URL         2024-01-15 10:30:00
```
Values are never displayed — only keys and metadata.
## Updating Secrets
Set the same key again to update:
```bash theme={null}
cast secrets set my-agent OPENAI_API_KEY sk-new-key...
```
Changes take effect on the next invocation.
## Deleting Secrets
```bash theme={null}
cast secrets delete my-agent OLD_API_KEY
```
## Best Practices
### Naming Conventions
* Use `UPPERCASE_WITH_UNDERSCORES`
* Be descriptive: `STRIPE_SECRET_KEY` not `KEY1`
* Prefix by service: `OPENAI_API_KEY`, `STRIPE_API_KEY`
### Security
* **Never commit secrets** — Use `.gitignore` for `.env` files
* **Rotate regularly** — Update secrets periodically
* **Limit scope** — Only set secrets an agent actually needs
* **Audit access** — Review who can manage secrets
### Development
Keep a `.env.example` file in your repo:
```bash theme={null}
# .env.example (committed to git)
OPENAI_API_KEY=your-key-here
DATABASE_URL=postgres://user:pass@host/db
# .env (NOT committed — in .gitignore)
OPENAI_API_KEY=sk-actual-key...
DATABASE_URL=postgres://actual-connection...
```
## Secrets in CI/CD
Example GitHub Actions workflow:
```yaml theme={null}
jobs:
deploy:
steps:
- name: Deploy agent
run: cast deploy
- name: Set secrets
env:
CASTARI_API_KEY: ${{ secrets.CASTARI_API_KEY }}
run: |
cast secrets set my-agent OPENAI_API_KEY "${{ secrets.OPENAI_API_KEY }}"
cast secrets set my-agent DATABASE_URL "${{ secrets.DATABASE_URL }}"
```
## See Also
CLI reference
SDK reference

---

## 

**URL:** https://docs.castari.com/installation.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Installation
> Install the Castari CLI and SDK
# Installation
Install the Castari CLI to deploy and manage agents from your terminal.
## CLI Installation
```bash theme={null}
npm install -g @castari/cli
```
```bash theme={null}
yarn global add @castari/cli
```
```bash theme={null}
pnpm add -g @castari/cli
```
### Verify Installation
```bash theme={null}
cast --version
```
**Using Claude Code?** Install the [Castari plugin](https://skills.sh/castari/cli) with `npx skills add castari/cli` and type `/castari-deploy` to handle installation, auth, and deployment in one step. [Learn more](/guides/claude-code).
You should see output like:
```
@castari/cli/0.1.0
```
## SDK Installation
If you want to use Castari programmatically (e.g., in a Node.js app or CI/CD pipeline):
```bash theme={null}
npm install @castari/sdk
```
```bash theme={null}
yarn add @castari/sdk
```
```bash theme={null}
pnpm add @castari/sdk
```
## System Requirements
| Requirement      | Version               |
| ---------------- | --------------------- |
| Node.js          | 18.0 or higher        |
| npm              | 8.0 or higher         |
| Operating System | macOS, Linux, Windows |
## Updating
To update to the latest version:
```bash theme={null}
npm update -g @castari/cli
```
## Troubleshooting
If you see `EACCES` errors, either:
1. Use a Node version manager like [nvm](https://github.com/nvm-sh/nvm) (recommended)
2. Or fix npm permissions: [npm docs](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)
Make sure your global npm bin directory is in your PATH:
```bash theme={null}
npm bin -g
```
Add the output directory to your shell's PATH.
Update Node.js to version 18 or higher. We recommend using [nvm](https://github.com/nvm-sh/nvm):
```bash theme={null}
nvm install 18
nvm use 18
```
## Next Steps
Deploy your first agent in under 5 minutes

---

## 

**URL:** https://docs.castari.com/quickstart.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# Quick Start
> Deploy your first agent in under 5 minutes
# Quick Start
Deploy your first Claude agent in under 5 minutes.
**Prerequisites:** Node.js 18+ and npm installed.
**Using Claude Code?** Type `/castari-deploy` and the skill handles all of these steps for you. Install it with `npx skills add castari/cli`. [Learn more](/guides/claude-code).
```bash theme={null}
npm install -g @castari/cli
```
Verify installation:
```bash theme={null}
cast --version
```
```bash theme={null}
cast login
```
This opens your browser to authenticate. Once complete, you'll see:
```
✓ Logged in as you@example.com
```
```bash theme={null}
cast init my-agent
```
Select the `default` template when prompted. This creates a coding agent with file and bash tools.
```
? Select a template:
❯ default — Coding agent with file and bash tools (like Claude Code)
research-agent — Deep research with web search and document synthesis
support-agent — Customer support with ticket handling and escalation
mcp-tools — Agent with example MCP server integration
✓ Created my-agent/ from default template
✓ Installed dependencies
```
```bash theme={null}
cd my-agent
cast deploy
```
Wait for deployment to complete:
```
⠋ Deploying my-agent...
✓ Agent 'my-agent' deployed!
```
```bash theme={null}
cast invoke my-agent "What files are in the current directory?"
```
Your agent responds:
```
The current directory contains:
- src/index.ts
- package.json
- castari.json
- CLAUDE.md
- README.md
```
## You did it!
Your agent is deployed and running. Here's what you can do next:
Edit the code and CLAUDE.md to change behavior
Add API keys and environment variables
Research agents, support agents, and more
Invoke agents programmatically

---

## 

**URL:** https://docs.castari.com/sdk/overview.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# SDK Overview
> Use Castari programmatically with TypeScript
# SDK Overview
The `@castari/sdk` package lets you manage agents programmatically.
## Installation
```bash theme={null}
npm install @castari/sdk
```
## Quick Example
```typescript theme={null}
import { CastariClient } from '@castari/sdk';
const client = new CastariClient({
apiKey: process.env.CASTARI_API_KEY,
});
// List agents
const agents = await client.agents.list();
console.log(agents);
// Deploy an agent
await client.agents.deploy('my-agent');
// Invoke an agent
const result = await client.agents.invoke('my-agent', { prompt: 'Hello!' });
console.log(result.response_content);
```
## When to Use the SDK
| Use Case                         | CLI or SDK? |
| -------------------------------- | ----------- |
| Manual deploys                   | CLI         |
| CI/CD pipelines                  | SDK         |
| Building apps that manage agents | SDK         |
| Quick testing                    | CLI         |
| Programmatic invocations         | SDK         |
## Authentication
The SDK supports two authentication methods:
### API Key (Recommended for servers)
```typescript theme={null}
const client = new CastariClient({
apiKey: 'cast_xxxxxxxx_xxxxxxxxxx',
});
```
Generate an API key in the [Castari Dashboard](https://app.castari.com).
### OAuth Token (For user-scoped access)
```typescript theme={null}
const client = new CastariClient({
token: 'eyJhbGciOiJSUzI1NiIs...', // Clerk JWT
});
```
## TypeScript Support
The SDK is written in TypeScript and exports all types:
```typescript theme={null}
import {
CastariClient,
Agent,
AgentStatus,
InvocationResponse,
} from '@castari/sdk';
```
## Error Handling
```typescript theme={null}
import { CastariClient, CastariError } from '@castari/sdk';
try {
await client.agents.deploy('my-agent');
} catch (error) {
if (error instanceof CastariError) {
console.error(`API Error: ${error.message}`);
console.error(`Status: ${error.status}`);
}
}
```
## Next Steps
Client configuration and setup
List, deploy, and invoke agents
Manage agent secrets
TypeScript interfaces

---

## 

**URL:** https://docs.castari.com/cli/agents.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# cast agents
> List and manage agents
# cast agents
Commands for listing and managing your agents.
## cast agents list
List all your agents.
### Usage
```bash theme={null}
cast agents list
```
### Output
```
NAME           SLUG           STATUS    CREATED
My Agent       my-agent       active    2024-01-15
Research Bot   research-bot   active    2024-01-14
Test Agent     test-agent     stopped   2024-01-10
```
***
## cast agents get
Get details for a specific agent.
### Usage
```bash theme={null}
cast agents get
```
### Example
```bash theme={null}
cast agents get my-agent
```
### Output
```
Name:        My Agent
Slug:        my-agent
Status:      active
Created:     2024-01-15 10:30:00
Updated:     2024-01-15 14:22:00
Sandbox ID:  sbx_xyz789
Git Repo:    https://github.com/user/my-agent
```
***
## cast agents create
Create a new agent without deploying.
### Usage
```bash theme={null}
cast agents create
[options]
```
### Arguments
| Argument  | Description                | Required |
| --------- | -------------------------- | -------- |
| `name`    | Display name for the agent | Yes      |
| `git-url` | Git repository URL         | Yes      |
### Options
| Option          | Description                                            |
| --------------- | ------------------------------------------------------ |
| `--slug
` | Custom slug (auto-generated from name if not provided) |
### Example
```bash theme={null}
cast agents create "My Agent" https://github.com/user/my-agent
```
With a custom slug:
```bash theme={null}
cast agents create "My Agent" https://github.com/user/my-agent --slug my-custom-slug
```
Most users should use `cast init` + `cast deploy` instead of `cast agents create`. This command is for advanced use cases.
***
## cast agents delete
Delete an agent.
### Usage
```bash theme={null}
cast agents delete
```
### Example
```bash theme={null}
cast agents delete my-agent
```
### Output
```
? Are you sure you want to delete 'my-agent'? (y/N) y
✓ Agent 'my-agent' deleted
```
This permanently deletes the agent, its secrets, and all invocation history. This cannot be undone.
### Options
| Option        | Description              |
| ------------- | ------------------------ |
| `-f, --force` | Skip confirmation prompt |
```bash theme={null}
cast agents delete my-agent --force
```
***
## See Also
* [cast deploy](/cli/deploy) — Deploy an agent
* [cast invoke](/cli/invoke) — Invoke an agent
* [cast secrets](/cli/secrets) — Manage secrets

---

## 

**URL:** https://docs.castari.com/cli/apikey.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# cast apikey
> Manage API keys for authentication
# cast apikey
Manage API keys for programmatic access to the Castari API. Castari supports multiple named API keys per user.
## Subcommands
| Command                       | Description          |
| ----------------------------- | -------------------- |
| `cast apikey list`            | List all API keys    |
| `cast apikey create`          | Create a new API key |
| `cast apikey revoke
` | Revoke an API key    |
***
## cast apikey list
List all API keys for the authenticated user.
### Usage
```bash theme={null}
cast apikey list
```
### Output
Displays a table with key ID, name, prefix, creation date, and last used date.
***
## cast apikey create
Create a new named API key.
### Usage
```bash theme={null}
cast apikey create [--name
]
```
### Options
| Option          | Description                    | Default   |
| --------------- | ------------------------------ | --------- |
| `--name
` | A descriptive name for the key | `Default` |
### Example
```bash theme={null}
cast apikey create --name "CI/CD Pipeline"
```
The full API key is only displayed once at creation. Store it securely — you will not be able to see it again.
***
## cast apikey revoke
Revoke an existing API key.
### Usage
```bash theme={null}
cast apikey revoke
```
### Arguments
| Argument | Description                     | Required |
| -------- | ------------------------------- | -------- |
| `key-id` | The ID of the API key to revoke | Yes      |
### Example
```bash theme={null}
cast apikey revoke ak_abc123
```
## See Also
* [Auth API (SDK)](/sdk/auth) — Manage API keys programmatically
* [CLI Overview](/cli/overview) — All CLI commands

---

## 

**URL:** https://docs.castari.com/cli/buckets.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# cast buckets
> Manage cloud storage buckets
# cast buckets
Manage cloud storage buckets for agent file access. Supports Amazon S3, Google Cloud Storage, and Cloudflare R2.
## Subcommands
| Command                           | Description              |
| --------------------------------- | ------------------------ |
| `cast buckets list`               | List all storage buckets |
| `cast buckets create
`      | Create a new bucket      |
| `cast buckets get
`         | Get bucket details       |
| `cast buckets delete
`      | Delete a bucket          |
| `cast buckets test
`        | Test bucket connection   |
| `cast buckets credentials
` | Set bucket credentials   |
***
## cast buckets list
List all configured storage buckets.
### Usage
```bash theme={null}
cast buckets list
```
***
## cast buckets create
Create a new storage bucket configuration.
### Usage
```bash theme={null}
cast buckets create
[options]
```
### Arguments
| Argument | Description                 | Required |
| -------- | --------------------------- | -------- |
| `name`   | Display name for the bucket | Yes      |
### Options
| Option                  | Description                              | Required            |
| ----------------------- | ---------------------------------------- | ------------------- |
| `--slug
`         | URL-safe identifier                      | No (auto-generated) |
| `--provider
` | Storage provider: `s3`, `gcs`, or `r2`   | Yes                 |
| `--bucket-name
`  | Actual bucket name in the cloud provider | Yes                 |
| `--region
`     | AWS region (for S3)                      | No                  |
| `--endpoint
`      | Custom endpoint URL (required for R2)    | No                  |
### Example
```bash theme={null}
cast buckets create "Production Data" \
--provider s3 \
--bucket-name my-data-bucket \
--region us-east-1
```
***
## cast buckets get
Get details for a specific bucket.
### Usage
```bash theme={null}
cast buckets get
```
***
## cast buckets delete
Delete a bucket configuration.
### Usage
```bash theme={null}
cast buckets delete
[-f, --force]
```
***
## cast buckets test
Test the connection to a bucket.
### Usage
```bash theme={null}
cast buckets test
```
Verifies that the configured credentials can access the bucket.
***
## cast buckets credentials
Set credentials for a bucket.
### Usage
```bash theme={null}
cast buckets credentials
[options]
```
### Options
| Option                          | Description                           |
| ------------------------------- | ------------------------------------- |
| `--access-key-id
`          | AWS/R2 access key ID                  |
| `--secret-access-key
`     | AWS/R2 secret access key              |
| `--service-account-file
` | Path to GCS service account JSON file |
### Example (S3/R2)
```bash theme={null}
cast buckets credentials my-bucket \
--access-key-id AKIAIOSFODNN7EXAMPLE \
--secret-access-key wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```
### Example (GCS)
```bash theme={null}
cast buckets credentials my-gcs-bucket \
--service-account-file ./service-account.json
```
## See Also
* [cast mounts](/cli/mounts) — Mount buckets to agents
* [Storage API (SDK)](/sdk/storage) — Manage buckets programmatically

---

## 

**URL:** https://docs.castari.com/cli/deploy.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# cast deploy
> Deploy an agent to Castari
# cast deploy
Deploy an agent to Castari.
## Usage
```bash theme={null}
cast deploy [slug]
```
## Arguments
| Argument | Description | Required                                              |
| -------- | ----------- | ----------------------------------------------------- |
| `slug`   | Agent slug  | No (read from `castari.json` if in project directory) |
## Examples
### Deploy from a Castari project directory
If you're inside a directory with a `castari.json` file, just run:
```bash theme={null}
cast deploy
```
The CLI reads the `slug` field from `castari.json`. If no `slug` field is present, it derives the slug from the `name` field.
### Deploy a specific agent
```bash theme={null}
cast deploy my-agent
```
Deploys the agent with slug `my-agent`, pulling code from its configured git repository.
## What Happens During Deploy
Code is pulled from the agent's configured git repository.
An isolated E2B sandbox is created for your agent.
`npm install` runs inside the sandbox.
Any secrets you've set become environment variables.
Agent status becomes `active`. Ready to invoke.
## Agent Configuration
Your agent needs a `castari.json` file:
```json theme={null}
{
"name": "my-agent",
"slug": "my-agent",
"version": "0.1.0",
"entrypoint": "src/index.ts",
"runtime": "node"
}
```
## Redeploying
Running `cast deploy` on an already-deployed agent will redeploy it:
1. Previous sandbox is destroyed
2. New sandbox is created
3. Latest code is deployed
Redeploying does not preserve sandbox state. All files and data in the sandbox are lost.
## Errors
| Error                     | Cause                      | Fix                                                   |
| ------------------------- | -------------------------- | ----------------------------------------------------- |
| `Agent not found`         | Invalid slug               | Check `cast agents list` for correct slug             |
| `castari.json not found`  | Not in a project directory | Specify the slug explicitly or `cd` to your project   |
| `npm install failed`      | Dependency error           | Check `package.json`, run `npm install` locally first |
| `Authentication required` | Not logged in              | Run `cast login`                                      |
## See Also
* [cast init](/cli/init) — Create a new agent from a template
* [cast invoke](/cli/invoke) — Invoke a deployed agent
* [Your First Agent](/first-agent) — Understand agent structure

---

## 

**URL:** https://docs.castari.com/cli/files.md

> ## Documentation Index
> Fetch the complete documentation index at: https://docs.castari.com/llms.txt
> Use this file to discover all available pages before exploring further.
# cast files
> Manage files in Castari storage
# cast files
Manage files in Castari's managed storage. Upload, list, and manage files that can be attached to agents.
## Command Overview
| Command               | Description           |
| --------------------- | --------------------- |
| `cast files list`     | List files in storage |
| `cast files upload`   | Upload a file         |
| `cast files get`      | Get file details      |
| `cast files delete`   | Delete a file         |
| `cast files download` | Download a file       |
| `cast files usage`    | Show storage usage    |
***
## cast files list
List files in your managed storage.
### Usage
```bash theme={null}
cast files list [options]
```
### Options
| Option             | Description                                 |
| ------------------ | ------------------------------------------- |
| `--limit
`      | Maximum files to return (default: 50)       |
| `--offset
`     | Number of files to skip (default: 0)        |
| `--scope
`  | Filter by scope: `user`, `agent`, `session` |
| `--tags
`    | Filter by tags (comma-separated)            |
| `--search
` | Search in filename and description          |
### Example
```bash theme={null}
cast files list
```
### Output
```
File ID                  Filename                       Size         Scope      Created
file_abc123              training-data.csv              2.4 MB       user       Jan 15, 2024
file_def456              config.json                    1.2 KB       user       Jan 14, 2024
file_ghi789              reference.txt                  45.6 KB      user       Jan 13, 2024
ℹ Showing 3 of 3 files
```
### Filter by Tags
```bash theme={null}
cast files list --tags dataset,csv
```
### Search Files
```bash theme={null}
cast files list --search training
```
***
## cast files upload
Upload a file to managed storage.
### Usage
```bash theme={null}
cast files upload
[options]
```
### Arguments
| Argument | Description               |
| -------- | ------------------------- |
| `path`   | Local file path to upload |
### Options
| Option                 | Description          |
| ---------------------- | -------------------- |
| `--description
` | File description     |
| `--tags
`        | Comma-separated tags |
### Example
```bash theme={null}
cast files upload data.csv --description "Training data" --tags dataset,ml
```
### Output
```
✓ Uploaded data.csv
File ID    file_abc123
Size       2.4 MB
→ Attach to agent: cast agents files add
file_abc123
```
Files larger than 10MB automatically use presigned uploads for better performance.
***
## cast files get
Get detailed information about a file.
### Usage
```bash theme={null}
cast files get
```
### Example
```bash theme={null}
cast files get file_abc123
```
### Output
```
File ID       file_abc123
Filename      data.csv
Size          2.4 MB
Content Type  text/csv
Scope         user
Status        confirmed
Description   Training data
Tags          dataset, ml
SHA256        a1b2c3d4e5f6...
Created       Jan 15, 2024 10:30 AM
Updated       Jan 15, 2024 10:30 AM
```
***
## cast files delete
Delete a file from storage.
### Usage
```bash theme={null}
cast files delete
[options]
```
### Options
| Option        | Description              |
| ------------- | ------------------------ |
| `-f, --force` | Skip confirmation prompt |
### Example
```bash theme={null}
cast files delete file_abc123
```
### Output
```
Are you sure you want to delete file 'file_abc123'? [y/N] y
✓ File 'file_abc123' deleted
```
Deleting a file will also detach it from any agents it was attached to.
***
## cast files download
Download a file from storage.
### Usage
```bash theme={null}
cast files download
[options]
```
### Options
| Option            | Description                                   |
| ----------------- | --------------------------------------------- |
| `--output
` | Output file path (default: original filename) |
### Example
```bash theme={null}
cast files download file_abc123 --output ./local-data.csv
```
### Output
```
✓ Downloaded to ./local-data.csv
ℹ Size: 2.4 MB
```
***
## cast files usage
Show your storage usage and quota.
### Usage
```bash theme={null}
cast files usage
```
### Example
```bash theme={null}
cast files usage
```
### Output
```
Total Files    12
Total Size     45.2 MB
Quota Used     45.2%
Quota Limit    100 MB
████████████████████░░░░░░░░░░░░░░░░░░░░ 45.2%
```
***
## Agent Files Commands
Manage files attached to specific agents.
### cast agents files list
List files attached to an agent.
```bash theme={null}
cast agents files list
```
#### Example
```bash theme={null}
cast agents files list my-agent
```
#### Output
```
File ID                  Filename                 Mount Path                    Size         Mode
file_abc123              data.csv                 /files/agent/data.csv         2.4 MB       ro
file_def456              config.json              /files/agent/config.json      1.2 KB       ro
ℹ 2 files attached (2.4 MB total)
```
***
### cast agents files add
Attach a file to an agent.
```bash theme={null}
cast agents files add
[options]
```
#### Options
| Option                | Description                                            |
| --------------------- | ------------------------------------------------------ |
| `--mount-path
` | Custom mount path (default: `/files/agent/
`) |
| `--writable`          | Make file writable (default: read-only)                |
#### Example
```bash theme={null}
cast agents files add my-agent file_abc123
```
#### Output
```
✓ File attached to agent 'my-agent'
File ID      file_abc123
Filename     data.csv
Mount Path   /files/agent/data.csv
Mode         read-only
→ File will be available at /files/agent/data.csv in the sandbox
```
***
### cast agents files remove
Detach a file from an agent.
```bash theme={null}
cast agents files remove
[options]
```
#### Options
| Option        | Description              |
| ------------- | ------------------------ |
| `-f, --force` | Skip confirmation prompt |
#### Example
```bash theme={null}
cast agents files remove my-agent file_abc123
```
#### Output
```
Detach file 'file_abc123' from agent 'my-agent'? [y/N] y
✓ File 'file_abc123' detached from agent 'my-agent'
```
***
## Workflow Example
Complete workflow for using files with an agent:
```bash theme={null}
# 1. Upload a file
cast files upload training-data.csv --description "ML training data"
# → file_abc123
# 2. Attach to agent
cast agents files add my-ml-agent file_abc123
# 3. Verify attachment
cast agents files list my-ml-agent
# 4. Deploy and run agent
cast deploy my-ml-agent
cast invoke my-ml-agent "Process the training data"
# 5. Clean up when done
cast agents files remove my-ml-agent file_abc123
```
## See Also
* [Managed Files Concept](/concepts/files) — How managed files work
* [Storage Buckets](/guides/storage) — BYO storage for large datasets
