# Release Notes — GreenNode AI Stack

A single timeline of updates across all GreenNode AI Stack products — GreenNode AgentBase, Model as a Service, AI Platform, AI Coding, and GenAI Studio.

***

## October 2026

**GreenNode AI Platform — Guardrail**

AI Platform introduces **Guardrail** — a feature that protects data and controls content before a request reaches your MaaS models, with zero client code changes:

* **Word filters:** block/mask based on keywords or phrases you declare (Mask / Detect / Block actions).
* **Prompt-based filter:** a "judge" model reads and evaluates content against your descriptions — catching even rephrased jailbreak attempts.
* **Sensitive information:** automatically masks/replaces/deletes PII data (email, credit card number, phone number…) and custom regexes before the data reaches the model.
* Attach a Guardrail to a MaaS model and every request passes through it automatically — endpoint and API key stay unchanged.
* Learn more at [Guardrail](ai-platform/guardrail/README.md).

**GreenNode AgentBase — Built-in Tools (Code Interpreter & Browser)**

AgentBase introduces **Built-in Tools** — ready-made tools that let AI agents run code and drive a browser inside a sandbox isolated per session:

* **Code Interpreter:** run Python and shell commands, read/write files, run background processes — each session in its own fully isolated sandbox.
  * Learn more at [Use Code Interpreter](agent-base/built-in-tools/use-code-interpreter.md).

* **Browser:** drive a real Chrome over CDP (Playwright/Puppeteer), watch the screen live via live view, and download session recordings.
  * Learn more at [Use Browser](agent-base/built-in-tools/use-browser.md).

* Integrates over **MCP**: connect your agent with an MCP endpoint and authenticate through the GreenNode Portal login flow — tokens from a Root user, IAM user, or Service Account all work.

* Each session runs inside its own fully isolated sandbox — state from one session never leaks into another.
  * Learn more at [Built-in Tools](agent-base/built-in-tools/README.md).

* The feature is rolling out by account **whitelist** — contact [support@greennode.ai](mailto:support@greennode.ai) to get enabled.

***

## August 2026

**GreenNode AgentBase — CLI & MCP**

AgentBase adds two ways to work alongside the Portal — the command line and AI assistants:

* **GreenNode CLI:** the `grn agentbase` command group manages the full agent lifecycle — Identity, Runtime, Memory, MCP Gateway, Policy and Container Registry — and deploys a whole agent from a single manifest via `grn agentbase deploy up`. Ideal for moving agent deployment into automation scripts and CI/CD.
  * Learn more at [Manage AgentBase with the GreenNode CLI](agent-base/manage-agentbase-with-the-greennode-cli.md).

* **AgentBase MCP:** an MCP server that lets AI assistants (Claude Desktop, Claude Code, Cursor, Windsurf...) operate AgentBase through natural language via the 3 meta-tools `list_servers` / `search_tools` / `execute`. Supports both local (stdio) and remote (HTTP through the MCP Gateway) connections.
  * Learn more at [Manage AgentBase with the GreenNode MCP](agent-base/manage-agentbase-with-the-greennode-mcp.md).

**GreenNode MaaS — Model Catalog & Pricing Update**

GreenNode is updating the MaaS model catalog into two groups — **GreenNode self-hosted models** and **third-party models** under official contracts — along with better pricing on many models. The Portal now shows a **model type label (Self-host / Partner)** right on the model list, making it easy to tell models apart and pick the right one.

* Current models are discontinued & the new catalog goes live on **August 3, 2026**, with an automatic extension until **September 2, 2026** before old-model requests start returning errors.
* Compare your current models against the [new catalog](model-as-a-service/available-models.md); only switch if your model is no longer listed.
* See how charges are calculated in [Pricing](model-as-a-service/pricing.md); per-model unit prices are shown directly on the [AI Platform Portal](https://aiplatform.console.greennode.ai/models).

{% hint style="info" %}
Need support during the transition? Contact [support@greennode.ai](mailto:support@greennode.ai), hotline **19001549**, or the [Help Center](https://helpdesk.greennode.ai/portal/en/home).
{% endhint %}

**GreenNode AI Gateway — Architecture Upgrade, Optimized Performance**

GreenNode has upgraded the AI Gateway architecture to better handle growing traffic from MaaS and Token Plan:

* **Low Latency:** Reduced latency in API key authentication and rate limiting.
* **Scalability:** No longer limited by memory — serves more models and more users.
* **High Performance:** Handles Token Plan traffic reliably.

**GreenNode AI Stack — Token Plan**

Token Plan lets you lock in your monthly AI budget with a **30-day prepaid** package that has a fixed token/request limit per model — instead of paying per token actually used like PAYG.

* Access via a **subscription-key**, fully separate from PAYG API Keys.
* Browse and buy packages directly under the **Token Plan** group on the Portal.
* Suited for stable daily usage (e.g. coding assistants).
* Learn more at [Token Plan](token-plan/README.md).

***

## July 2026

**GreenNode AgentBase — Security Runtime & MCP Connectors**

GreenNode AgentBase adds new security and integration capabilities for AI Agents:

**New Features:**

* **Security Runtime:** Control which clients can reach a Runtime and how incoming requests are authenticated — configure **IP Access Control** (allowed source IP CIDR ranges) and **Inbound Identity** (IAM Permissions, JWT, or No authorization) directly when creating a Runtime.
  * Learn more at [Create Runtime](agent-base/agent-runtime/create-runtime.md).

* **MCP Connectors:** Connect an agent to external services — GitHub, Slack, Microsoft 365 (M365 VNG bundle)... — in minutes via a prebuilt catalog, without building an MCP server or hand-coding OAuth.
  * Learn more at [MCP Connectors](agent-base/mcp-connectors/README.md).

***

## May 2026

**GreenNode AgentBase — Phase 2**

GreenNode AgentBase releases **Phase 2** with new features expanding governance, security, and integration capabilities for AI Agents:

**New Features:**

* **Marketplace:** Browse and deploy AI agents from a template library organized by category — AI Chat, Coding, Automation. Filter, view details, and deploy with one click.
  * Learn more at [Marketplace](agent-base/marketplace/README.md).

* **Container Registry:** A private container image repository automatically provisioned for your organization. Push images via AgentBase Skills (recommended) or Docker CLI manually; images are used directly when creating a Runtime.
  * Learn more at [Container Registry](agent-base/container-registry/README.md).

* **Rate Limit:** Control API call frequency by requests or tokens per model and API key, preventing system overload and managing costs.
  * Learn more at [Rate Limit](agent-base/protect-govern/rate-limit.md).

* **MCP Governance:** Centralized control over connections between AI Agents and external services:
  * **MCP Gateway** — Centralized proxy that auto-detects protocol, enforces Policies, and supports Private mode via VPC Peering.
  * **Policy Groups** — Allow/Deny rule sets based on tool name, input, and output patterns.
  * Learn more at [MCP Governance](agent-base/mcp-governance/README.md).

* **AI Coding:** Connect Claude Code CLI and OpenAI-compatible AI coding tools (OpenAI SDK, LiteLLM, Cursor...) directly to GreenNode MaaS — billed via internal credit-tokens.
  * Learn more at [AI Coding](ai-coding/README.md).

* **Usage & Budget:** Real-time dashboard tracking token consumption and costs by agent, model, API key, and time range; set monthly budget limits with automatic alerts at 80% and 100%.
  * Learn more at [Usage & Budget](usage-budget/README.md).

* **Private Networking:** VPC Peering for Agent Runtime and MCP Gateway — allowing agents to reach internal services without exposing them to the internet.
  * Learn more at [Private Networking](agent-base/private-networking.md).

***

## April 2026

**GreenNode AgentBase — OpenClaw 1-Click**

GreenNode releases **OpenClaw 1-Click** on AgentBase, enabling anyone to deploy a personal AI Agent powered by OpenClaw directly from the **Agent Marketplace** — no technical knowledge required, no manual setup, ready in just 40–60 seconds.

**New Features:**

* **OpenClaw 1-Click:** Deploy an OpenClaw instance directly from the Marketplace with minimal configuration.
  * **Auto-connected to GreenNode MaaS:** GreenNode users are automatically granted LLM access without manually configuring API keys. Default model: **qwen3-5-27b**.
  * **BYOK — Bring Your Own Key:** Supports bringing an API key from external providers (OpenAI, Anthropic, Gemini...).
  * **Channel integrations:** Configure Telegram and Zalo connections directly during the deploy step.
  * **My Agents:** Manage all OpenClaw instances with status filtering, stop, restart, and delete.
  * Learn more at [OpenClaw 1-Click](agent-base/agent-runtime/openclaw/openclaw-1-click.md).

***

## March 2026

**GreenNode AgentBase — Phase 1**

GreenNode AgentBase releases **Phase 1** — a purpose-built infrastructure platform for AI Agents, enabling developers to deploy and operate AI Agents on the cloud without managing servers, scaling, or credentials.

**New Features:**

* **Agent Runtime:** Deploy agents as containers with autoscaling, versioning, and zero-downtime deployment; supports Custom Agent and OpenClaw 1-Click.
* **Access Control & Team Permissions:** Manage agent identity, automatically inject credentials into containers; role-based member permissions with Service Accounts.
* **Memory Service:** Store conversation history (short-term) and extract semantic facts (long-term).
* **GreenNode MaaS Integration:** Direct connection to LLM models via an OpenAI-compatible API.
* Learn more at [GreenNode AgentBase](agent-base/README.md).
