# Create Runtime

> Step-by-step guide to creating an Agent Runtime from a container image on GreenNode AgentBase.

***

## Prerequisites

* A GreenNode account with role Root or Admin
* A container image for your agent (public registry or [private Container Registry](../container-registry/))
* An Identity created for the agent — see [Access Control](../access-control/)

***

## Quick Deploy with AgentBase Skills

If you use **Claude Code**, **Cursor**, or Codex, GreenNode provides a skills kit at [github.com/vngcloud/greennode-agentbase-skills](https://github.com/vngcloud/greennode-agentbase-skills) — a set of slash commands that automate the full workflow from scaffolding to deployment in one guided flow.

**Install:**

```bash
# Clone skills into your project
git clone https://github.com/vngcloud/greennode-agentbase-skills .claude/skills/agentbase
```

**Use in Claude Code / Cursor:**

| Skill                 | What it does                                                               |
| --------------------- | -------------------------------------------------------------------------- |
| `/agentbase-wizard`   | Full 9-step workflow: scaffold → configure → code → test → deploy → verify |
| `/agentbase-deploy`   | Build container, push image, and deploy to Runtime                         |
| `/agentbase-monitor`  | View logs, metrics, and Runtime status                                     |
| `/agentbase-identity` | Configure IAM Identity for your agent                                      |

Recommended for developers who want to integrate deployment into an AI-assisted coding workflow without manual Portal steps.

***

## Create via Portal

**Step 1:** Open [Agent Runtime](https://aiplatform.console.greennode.ai/agent-runtime?tab=runtime) → click **Deploy a new Agent**

A popup appears to choose the agent type:

![Deploy a new Agent — choose agent type](<../../../.gitbook/assets/deploy-new-agent (1).png>)

| Type             | Description                                                                                         |
| ---------------- | --------------------------------------------------------------------------------------------------- |
| **Custom Agent** | Agent you code and deploy yourself — full control over runtime, vCR, IAM, and scaling               |
| **OpenClaw**     | Platform-managed agent, pre-configured by GreenNode — quick deploy, no runtime configuration needed |

Click **DEPLOY** in the **Custom Agent** card to continue.

**Step 2:** Fill in the required information

| Field                     | Example                              | Notes                                     |
| ------------------------- | ------------------------------------ | ----------------------------------------- |
| **Name**                  | `my-order-agent`                     | Unique, lowercase, hyphens allowed        |
| **Description**           | `Production order agent`             | Optional                                  |
| **Image URL**             | `vcr.vngcloud.vn/<repo>/my-agent:v1` | Full image path including tag             |
| **Flavor**                | `1x1-general`                        | 1 vCPU, 1 GB RAM                          |
| **Min Replicas**          | `1`                                  | Range: 1–10                               |
| **Max Replicas**          | `1`                                  | Set >1 to enable autoscaling              |
| **CPU Threshold**         | `50`                                 | Scale out when CPU exceeds this % (25–75) |
| **Memory Threshold**      | `50`                                 | Scale out when RAM exceeds this % (25–75) |
| **Registry Auth**         | Enable if private                    | Username = robot account `backendName`    |
| **Environment Variables** | `KEY=value`                          | Non-sensitive config only                 |

**Step 3:** Configure networking (optional)

* **Private VPC**: enable to deploy inside your enterprise network

**Step 4:** Configure Security Settings — control which clients can reach the Runtime and how incoming requests are authenticated

![Security Settings — IP Access Control and Inbound Identity](<../../../.gitbook/assets/Agentbase-image/Security-Runtime.png>)

| Setting | Description |
| ------- | ----------- |
| **IP Access Control** | Restrict which source IP CIDR ranges can reach this Runtime. Add one or more CIDRs to **Allowed source IP ranges**; leave empty to allow all sources. |
| **Inbound Identity** | Configure how incoming requests to this Runtime are authenticated — set the **Inbound Auth type** below. |

**Inbound Auth type:**

| Mode | Description |
| ---- | ----------- |
| **IAM Permissions** | Authenticates callers using the GreenNode AI Platform IAM token. This is the **default** mode for new Runtimes (secure by default). |
| **JSON Web Tokens (JWT)** | Validates incoming JWT bearer tokens. Set the **JWT key source** to a **Discovery URL** (automatically fetches and refreshes public keys) or inline **JWKS**. |
| **No authorization** | No authentication required. |

{% hint style="warning" %}
New Runtimes default to **IAM Permissions** — requests to the endpoint must carry a valid IAM token. Only switch to **No authorization** if you truly need a public endpoint: the Runtime then becomes accessible to anyone with the endpoint URL, with no access control.
{% endhint %}

**Step 5:** Configure Endpoint — AgentBase automatically creates a **DEFAULT** Endpoint

**Step 6:** Review → click **Create**

The Runtime transitions from `CREATING` → `ACTIVE` once the container starts successfully.

***

## Create via API

> Requires `$TOKEN` — an IAM bearer token. See [Getting Started](../getting-started.md#configure-authentication) to obtain one.

**Public registry image:**

```bash
curl -s -X POST "https://agentbase.api.vngcloud.vn/runtime/agent-runtimes" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-order-agent",
    "description": "Production order agent",
    "imageUrl": "vcr.vngcloud.vn/<repo-backendName>/my-agent:v1.0.0",
    "command": [],
    "args": [],
    "environmentVariables": {
      "LOG_LEVEL": "info"
    },
    "flavorId": "1x1-general",
    "autoscaling": {
      "minReplicas": 1,
      "maxReplicas": 3,
      "cpuUtilization": 50,
      "memoryUtilization": 50
    }
  }' | jq .
```

**Private registry image (imageAuth required):**

```bash
curl -s -X POST "https://agentbase.api.vngcloud.vn/runtime/agent-runtimes" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-order-agent",
    "imageUrl": "vcr.vngcloud.vn/<repo-backendName>/my-agent:v1.0.0",
    "imageAuth": {
      "enabled": true,
      "username": "<robot-account-backendName>",
      "password": "<robot-account-secret>"
    },
    "command": [],
    "args": [],
    "environmentVariables": {},
    "flavorId": "1x1-general",
    "autoscaling": {
      "minReplicas": 1,
      "maxReplicas": 1,
      "cpuUtilization": 50,
      "memoryUtilization": 50
    }
  }' | jq .
```

**Poll until ACTIVE:**

```bash
RUNTIME_ID="<id-from-response>"
while true; do
  STATUS=$(curl -s "https://agentbase.api.vngcloud.vn/runtime/agent-runtimes/$RUNTIME_ID" \
    -H "Authorization: Bearer $TOKEN" | jq -r '.status')
  echo "Status: $STATUS"
  [ "$STATUS" = "ACTIVE" ] && break
  sleep 10
done
```

***

## Service Contract

Your agent container must satisfy the following requirements to work with the Runtime:

### Port and Health Check

| Requirement  | Value         | Notes                       |
| ------------ | ------------- | --------------------------- |
| Listen port  | `8080`        | Required — not configurable |
| Health check | `GET /health` | Must return HTTP 200        |

**Using the greennode-agentbase SDK (recommended):**

```python
from greennode_agentbase import GreenNodeAgentBaseApp, RequestContext

app = GreenNodeAgentBaseApp()

@app.entrypoint
def handler(payload: dict, context: RequestContext) -> dict:
    return {"output": "Hello!"}

if __name__ == "__main__":
    import os
    app.run(host="0.0.0.0", port=int(os.environ.get("PORT", "8080")))
```

**Without SDK (FastAPI example):**

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/health")
def health():
    return {"status": "ok"}

@app.post("/invoke")
def invoke(body: dict):
    return {"output": f"Echo: {body.get('input', '')}"}
```

### Auto-Injected Environment Variables

The Runtime automatically injects these into every container:

| Variable                   | Description                       |
| -------------------------- | --------------------------------- |
| `GREENNODE_CLIENT_ID`      | IAM service account client ID     |
| `GREENNODE_CLIENT_SECRET`  | IAM service account client secret |
| `GREENNODE_AGENT_IDENTITY` | The agent identity name           |

### Request Headers

Incoming requests to your agent may include the following headers — sent by the client and forwarded intact to the container:

| Header                             | Description                                                 |
| ---------------------------------- | ----------------------------------------------------------- |
| `X-GreenNode-AgentBase-User-Id`    | End-user ID — use as `actorId` for memory operations        |
| `X-GreenNode-AgentBase-Session-Id` | Session ID — use as `thread_id` for LangGraph checkpointing |

{% hint style="warning" %}
AgentBase does not pass the caller's identity (authenticated at Inbound Auth) into the container — these headers are not verified by AgentBase and can be spoofed by the client. If your agent uses memory, validate that these headers are present and return an error if missing. Do not fall back to default values and do not use these headers for authorization logic.
{% endhint %}

### Header Handling on Incoming Requests

How AgentBase handles headers depends on the endpoint's **Inbound Auth** mode:

| Inbound Auth | `Authorization` and `x-agentbase-endpoint-inbound-header-authorization` headers |
| ------------ | ------------------------------------------------------------------------------- |
| **No authorization** | Forwarded intact to the container. You can authenticate inside the agent using these headers. |
| **IAM Permissions**, **JWT** | AgentBase uses the token in these headers to authenticate the caller, then strips both headers before the request reaches the container. The container cannot read the token — even if you send the authentication token via `x-agentbase-endpoint-inbound-header-authorization` and put a different token in `Authorization`. |

Other headers (e.g. `X-Api-Key`, `Cookie`, `traceparent`, custom headers) are forwarded intact in all modes. `X-Forwarded-For` has the proxy's address appended.

{% hint style="info" %}
In **IAM Permissions**/**JWT** mode, if your agent needs its own token (e.g. an application API key, or an end-user JWT to call other APIs), send it via a custom header such as `X-Api-Key`. Do not use `Authorization`.
{% endhint %}

{% hint style="warning" %}
AgentBase does not currently pass the caller's identity (user ID, JWT claims) into the container. Do not build authorization logic on headers such as `x-agentbase-endpoint-inbound-user-id`, `portal-user-id`, `iam-user-id` — these headers are sent by the client and forwarded intact, so they can be spoofed.
{% endhint %}

***

## Next Steps

| I want to...                       | Go to                                             |
| ---------------------------------- | ------------------------------------------------- |
| Stop, start, or update the Runtime | [Manage Runtime](manage-runtime.md)               |
| Attach a Policy Group to a Gateway | [Policy Groups](../mcp-governance/policy-groups/) |
