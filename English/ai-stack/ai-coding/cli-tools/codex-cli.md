# Codex CLI

> Guide to configuring the [OpenAI Codex CLI](https://github.com/openai/codex) to call the Minimax model via GreenNode MaaS — using the Responses API through a custom `maas` provider defined in `codex.toml`.

***

## Prerequisites

* Prepare your key, Base URL, and model following [Getting Started with AI Coding](../getting-started.md)
* Node.js ≥ 22 installed

***

## Pick your configuration by service type

Codex uses the **OpenAI standard** → the Base URL **includes** `/v1` on both service types. Only the host and the key type differ:

| Service type | `base_url` | Key | `model` |
|---|---|---|---|
| **PAYG** | `https://maas-llm-aiplatform-hcm.api.vngcloud.vn/v1` | API Key from the [API Keys page](https://aiplatform.console.greennode.ai/keys) | `minimax/minimax-m2.5` |
| **Token Plan** | `https://tokenplan.api.greennode.ai/v1` | subscription-key from Plan Detail → **Subscription keys** tab | Model code on the plan's **Models** tab |

{% hint style="warning" %}
**Your key and Base URL must belong to the same service type.** A PAYG API Key sent to the `tokenplan…` host (or the reverse) returns `401 Unauthorized` even while the key is still valid. Go by where you got the key — see [section 2 of the Prerequisites page](../getting-started.md).
{% endhint %}

***

## Step 1 — Install Codex CLI

```bash
npm install -g @openai/codex
```

Confirm the install succeeded:

```bash
codex --version
```

***

## Step 2 — Get your key

{% tabs %}
{% tab title="PAYG — API Key" %}
1. Log in to the [AI Platform Console](https://aiplatform.console.greennode.ai/)
2. Go to **API Keys** → **Create API Key**
3. Name the key (5–50 characters, lowercase letters + numbers + hyphens)
4. Copy the API key you just created

{% hint style="warning" %}
A newly created API key starts in `pending` status. Wait until status = `ACTIVE` before using it.
{% endhint %}
{% endtab %}

{% tab title="Token Plan — subscription-key" %}
1. Go to **API Key** → **Token Plan** → **My Token Plans** and open the plan you bought
2. **Subscription keys** tab → copy `default-key` or a key you created
3. **Models** tab → copy the **Model code** of the model you want

No plan yet? See [Buy Token Plan](../../token-plan/buy-token-plan.md).
{% endtab %}
{% endtabs %}

***

## Step 3 — Configure `codex.toml`

Create or edit `~/.codex/config.toml` (system-wide) or `codex.toml` in your project root (that project only). Copy the tab matching your service type:

{% tabs %}
{% tab title="PAYG" %}
```toml
# API key — export before running Codex
# export MAAS_API_KEY="<your-PAYG-API-key>"

model_provider = "maas"
model = "minimax/minimax-m2.5"

# Required because MAAS doesn't return model metadata — prevents wrong context truncation
model_context_window = 204800
model_max_output_tokens = 16400

# The MAAS backend is stateless — Codex must resend the full conversation each turn
disable_response_storage = true

[model_providers.maas]
name = "MAAS"

# base_url must NOT have a trailing /responses — Codex appends it (→ .../v1/responses)
base_url = "https://maas-llm-aiplatform-hcm.api.vngcloud.vn/v1"
env_key = "MAAS_API_KEY"
wire_api = "responses"
request_max_retries = 3
```
{% endtab %}

{% tab title="Token Plan" %}
```toml
# subscription-key — export before running Codex
# export MAAS_API_KEY="<your-subscription-key>"

model_provider = "maas"
model = "glm-5.2"   # replace with the Model code from your plan's Models tab

# Required because MAAS doesn't return model metadata — prevents wrong context truncation
model_context_window = 204800
model_max_output_tokens = 16400

# The MAAS backend is stateless — Codex must resend the full conversation each turn
disable_response_storage = true

[model_providers.maas]
name = "MAAS"

# base_url must NOT have a trailing /responses — Codex appends it (→ .../v1/responses)
base_url = "https://tokenplan.api.greennode.ai/v1"
env_key = "MAAS_API_KEY"
wire_api = "responses"
request_max_retries = 3
```

{% hint style="info" %}
`model` must match the **Model code** exactly as shown on the plan's **Models** tab, and that model must be included in the plan — calling a model outside the plan returns `403 Forbidden`.
{% endhint %}
{% endtab %}
{% endtabs %}

**Key fields explained:**

| Field | Purpose |
|---|---|
| `model_provider` | The provider key under `[model_providers.*]` |
| `model` | Model ID sent to MaaS — PAYG uses the Models portal ID, Token Plan uses the plan's Model code |
| `model_context_window` | Declared manually because MaaS doesn't expose model metadata |
| `disable_response_storage` | Required for a stateless backend — resends the full conversation each turn |
| `base_url` | The endpoint for your service type, including `/v1` — Codex appends `/responses` itself |
| `env_key` | Name of the environment variable holding the key |
| `wire_api` | Protocol used — `responses` maps to the OpenAI Responses API |

***

## Step 4 — Set the key and run Codex

Export the key in your shell — use the key belonging to the same service type as the `base_url` you set in Step 3:

{% tabs %}
{% tab title="PAYG" %}
```bash
export MAAS_API_KEY="<your-PAYG-API-key>"
```
{% endtab %}

{% tab title="Token Plan" %}
```bash
export MAAS_API_KEY="<your-subscription-key>"
```
{% endtab %}
{% endtabs %}

To set it automatically every time you open a terminal, add it to `~/.zshrc` or `~/.bashrc`:

```bash
echo 'export MAAS_API_KEY="<your-key>"' >> ~/.zshrc
source ~/.zshrc
```

Run Codex in your project directory:

```bash
codex
```

Codex shows the active provider and model in the session header:

```
model:     minimax/minimax-m2.5   /model to change
directory: ~/your-project
```

<figure><img src="../../../.gitbook/assets/use-codex-with-minimax (1).png" alt=""><figcaption><p>Codex running with the minimax/minimax-m2.5 model via GreenNode MaaS</p></figcaption></figure>

***

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `401 Unauthorized` | Key wrong, missing, or not yet ACTIVE | Re-export `MAAS_API_KEY`; check the key status |
| `401` while the key is still valid | **Key and `base_url` belong to different service types** | Re-check the table at the top: a key from the **API Keys** page → the `maas-llm-…` host; a key from the **Subscription keys** tab → the `tokenplan…` host |
| `403 Forbidden` (Token Plan) | Model isn't included in the plan | Set `model` to a Model code listed on the plan's **Models** tab |
| `402 Payment Required` (Token Plan) | Plan expired or was deleted | Buy the plan again or enable **Auto-renew** |
| `404` on request | `base_url` wrong or missing `/v1` | Codex is OpenAI-standard — `base_url` must end with `/v1` (no `/responses`) |
| Context truncated incorrectly | Model metadata not declared | Check `model_context_window` and `model_max_output_tokens` in the config |
| Loses earlier context each turn | `disable_response_storage` not set | Add `disable_response_storage = true` to the config |
| Connection timeout | Endpoint unreachable | Check VPN / connectivity to `*.api.vngcloud.vn` (PAYG) or `tokenplan.api.greennode.ai` (Token Plan) |

***

## Result

Codex CLI now routes every request through the GreenNode endpoint for your chosen service type. Usage is recorded in the [AI Platform Console → Usage](https://aiplatform.console.greennode.ai/).

| I want to next... | Go to |
|---|---|
| Use the GUI version | [Codex Desktop](../gui-tools/codex-desktop.md) |
| Use OpenCode with MaaS | [OpenCode](opencode.md) |
| Connect Claude Code to MaaS | [Claude Code](claude-code.md) |
| Learn about Token Plan packages | [Token Plan](../../token-plan/README.md) |
| View usage and billing | [AI Platform Console](https://aiplatform.console.greennode.ai/) |

***

## Need help?

If you've followed the steps and it's still not working, feel free to contact GreenNode Customer Support:

* Email: [support@greennode.ai](mailto:support@greennode.ai)
* Hotline: 19001549
* Help center: [helpdesk.greennode.ai](https://helpdesk.greennode.ai)

Thank you for using GreenNode's services.
