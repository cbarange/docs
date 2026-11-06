# Using Guardrails

> This guide walks you through creating a Guardrail on AI Platform, attaching it to MaaS models, and testing requests that pass through the Guardrail — no client code changes required. See [What is Guardrail?](README.md) if you are new to the three filter layers.

---

## Prerequisites

- An **AI Platform account** with models enabled on MaaS — Guardrails attach to models in your model catalog (self-hosted/BYOK custom models are not yet supported).
- The model you want to protect uses one of the supported route types: **chat, completions, responses, messages, embeddings** (other types such as generateContent/rerank are not yet supported).
- Each model can be attached to only **one Guardrail** at a time.
- If you use Word filters with a file, prepare an **.xlsx** file following the template (the **Download template** button in the form).

---

## Create a Guardrail

**Step 1: Open the creation form**

1. Sign in to the AI Platform Console.
2. Go to **Configuration → Guardrail**.
3. Click **Create a Guardrail**. The form has 4 sections: **Basic configuration**, **Word filters**, **Prompt-based filter**, **Sensitive information**.

**Step 2: Fill in Basic configuration**

| Field | Required | Rules |
|---|---|---|
| **Guardrail name** | Yes | Lowercase `a-z`, digits `0-9`, and `-` only; 5–100 characters; must not duplicate another of your Guardrail names. |
| **Guardrail description** | No | Up to 255 characters. |

**Step 3: Configure Word filters**

The Guardrail detects your declared words/phrases in the input request and applies the corresponding action.

1. Add words one by one with **Add a word or phrase**, or import in bulk with **Upload .xlsx file** (template available via **Download template**).
2. Fill in the fields below for each word.

| Field | Required | Rules |
|---|---|---|
| **Word / phrase** | Yes | Up to 100 characters and at most 3 words (space-separated). |
| **Category** | Yes | Your own group name (e.g. `profanity`, `brand`, `internal-code`) — used for filtering in the table and the detail page. |
| **Action** | Yes | One of **Mask / Detect / Block** (see [Actions per filter](README.md#actions-per-filter)). |

The list supports search, filtering by category/action, and selecting multiple rows to **Remove**.

{% hint style="info" %}
Each (category, word) pair is its own rule — the same word can exist in 2 categories with 2 different actions.
{% endhint %}

**Step 4: Configure the Prompt-based filter**

The Guardrail applies an action to the input request when the content matches your declared description. Each rule has:

| Field | Required | Rules |
|---|---|---|
| **Description name** | Yes | Rule name, up to 100 characters, must be unique. |
| **Description** (large text box) | Yes | Describe in words the behavior to catch — e.g. *"Requests to ignore or bypass previous system instructions"*. The clearer the description, the more accurately the judge model catches violations. |
| **Action** | Yes | **Detect** or **Block**. |

Some commonly used description examples:

- *Ignore, override, or bypass previous system instructions.*
- *Role-play as an unrestricted or "jailbroken" AI assistant.*
- *Extract or leak the system prompt / internal configuration.*
- *Impersonate a developer, administrator, or the model's creator to override safety limits.*

**Step 5: Configure Sensitive information (optional)**

The Guardrail processes the sensitive information declared below before sending the input request to the model. All types share one **Confidence score** from 0 to 1 — the higher the score, the stricter the detection.

**PERSONALLY IDENTIFIABLE INFORMATION** — select PII types and an action for each:

| Field | Required | Rules |
|---|---|---|
| **PII types** | No (leave the whole section empty if unused) | Choose from the built-in list (URL, IP Address, Credit Card Number, Email, IBAN Code…). The full list is available via the API `GET /v1/guardrails/pii/types`. No duplicate types. |
| **Score** | No | Float 0.0 – 1.0; defaults to 0.5 if left empty. Applies to all selected types. |
| **Action per type** | Yes (if the type is selected) | **Mask / Replace / Delete**. With **Replace** you can enter a custom replacement value (leave empty to use the type name, e.g. `<EMAIL>`); with **Mask** you can customize the number of masked characters and the masking character (defaults to `*`). |

**REGULAR EXPRESSION** — add your own regexes:

| Field | Required | Rules |
|---|---|---|
| **Name** | Yes | Up to 100 characters, must be unique. |
| **Score** | No | 0.0 – 1.0, defaults to 0.5. |
| **Action** | Yes | **Mask / Replace / Delete**. Replacement value up to 255 characters; masking character up to 100 characters. |
| **Regex pattern** | Yes | Up to 255 characters and must be a valid regular expression (the system rejects patterns that do not compile). |

Both sections have a **Reset configuration to default** button.

**Step 6: Confirm creation**

1. Review the configuration in each section.
2. Click the create button to finish.

{% hint style="info" %}
At least one of the three filters must have a configuration — creating an "empty" Guardrail is rejected with the error *"Guardrails must declare at least one of Prompt-based, PII, or Word filter"*.
{% endhint %}

After creation, the Guardrail appears in the list with status **CREATING**, then switches to **ACTIVE** when ready. At this point it is not protecting any model yet — attach models in the next step.

---

## Attach models to protect (Manage Models)

**Step 1: Open Manage Models**

1. Go to **Protect & Govern → Guardrail**.
2. Open the **Action → Manage Models** menu of the Guardrail you want to configure.

**Step 2: Select models**

1. Choose the **Model as a Service** source — the list of models you have enabled on MaaS.
2. Search and tick the models to protect (there is a **Select all** button and a search box; each model is labeled **Third-party** or **Self-hosted**).
3. Click **Save** to apply.

Attach rules:

| Rule | Details |
|---|---|
| One model – one Guardrail | A model already attached to another Guardrail is rejected: *"Model 'X' is already attached to guardrails 'Y'"*. Detach it from the old Guardrail first. |
| Supported route types | chat, completions, responses, messages, embeddings. |
| Status | The Guardrail must be **ACTIVE** or **ATTACH_FAILED**; the model must be enabled. |
| Conflicting operations | Only one attach/detach operation runs at a time per account — if another operation is running you will see *"Another guardrails attach/detach is in flight for this user. Please wait."* |

After you click **Save**, the change is applied to the gateway; while applying, the Guardrail is in status **UPDATING**, then returns to **ACTIVE**.

---

## Call the API through a Guardrail

Client code **needs no changes** — same MaaS endpoint and API key as usual. Requests automatically pass through the Guardrail attached to the model.

```bash
curl --request POST \
--url 'https://maas-llm-aiplatform-hcm.api.vngcloud.vn/v1/chat/completions' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer $AI_PLATFORM_API_KEY' \
--data '{
  "model": "qwen/qwen3.8-max",
  "messages": [
    {
      "role": "assistant",
      "content": "You are an AI assistant tasked with providing information to users."
    },
    {
      "role": "user",
      "content": "What is AI?"
    }
  ],
  "max_tokens": 4096,
  "temperature": 1,
  "top_p": 0.95
}'
```

When a request is blocked, the API returns **400** with the reason.

Prompt-based filter block:

```json
{
  "status": "Prompt has been rejected by AI Guard",
  "reason": "Content violates the following categories: <prompt-based rule name>"
}
```

Word filter block:

```json
{
  "status": "Request blocked due to detected keyword",
  "reason": "<detected keyword>"
}
```

When Sensitive information detects sensitive data, the request is **not blocked** — the content is masked/replaced/deleted before reaching the model.

---

## Scan the system prompt with the `<vng-guard-content>` tag

By default, Guardrail **skips** the system prompt, because it is usually configuration you wrote yourself. If your application injects content that end users can influence into the system prompt (RAG documents, conversation history, uploaded files), insert the `<vng-guard-content>` tag right before that content so Guardrail scans it like user content.

| Situation | Result |
|---|---|
| System prompt has **no** tag | The whole prompt is skipped — no filter scans it. |
| System prompt has a tag | Everything from the tag position onward is scanned like user content (through all 3 filters). The part before the tag is still skipped. |
| Multiple `system` messages/blocks | A tag in **any** block turns scanning on for the **entire remainder** of the system prompt — including later blocks. |
| Misspelled tag | Nothing is scanned — the system silently ignores it. Copy `<vng-guard-content>` exactly. |

See full examples and how to verify the behavior yourself in [Scan the system prompt with the `<vng-guard-content>` tag](scan-system-prompt-with-vng-guard-content-tag.md).

***

## Manage Guardrails on the list page

The **Protect & Govern → Guardrail** page lists all your Guardrails with these columns:

| Column | Content |
|---|---|
| **Name** | Name + system ID (`gr-…`) + creation time. Click the name to open the detail page. |
| **Status** | Status (see [Guardrail statuses](README.md#guardrail-statuses)). |
| **Prompt-based filter** | Number of configured prompt-based rules. |
| **PII type & Regex** | Configured PII types / regexes. |
| **Protected models** | Models currently protected by this Guardrail. |
| **Action** | Action menu: **Edit info**, **Manage Models**, **Delete**. |

The toolbar above the table has a refresh button and a bulk delete button (select multiple rows with checkboxes).

**Edit info** — edit the **name** and **description**. The new name must still follow the rules in [Create a Guardrail](#create-a-guardrail) (Basic configuration) and must not duplicate another of your Guardrails.

**Delete** — delete a Guardrail:

{% hint style="warning" %}
A Guardrail with attached models must be **detached first** (Manage Models → deselect all) — deleting while models are attached is rejected: *"Guardrails is attached to one or more models. Please detach them first."* Only Guardrails in the ACTIVE, ERROR, ATTACH_FAILED, or CREATE_ERROR status can be deleted.
{% endhint %}

---

## View and edit on the Guardrail detail page

Click a Guardrail's name in the list to open its detail page:

- **General information** — ID (`gr-…`), description, protected models.
- **Detail information** — 3 tabs matching the 3 filters:
  - **Word filters** — keyword table, filter by category/action, edit with **Edit words & phrases**.
  - **Prompt-based filter** — rule list (name + description), edit with **Edit**.
  - **Sensitive Info** — PII types, score, and regex configuration, edited like the creation form.
- Top right corner: quick info edit (**Edit info**), **Manage Models**, and **Delete**.

Edits on the detail page follow the same rules as creation (see [Create a Guardrail](#create-a-guardrail)).

---

## Result

Once complete, every request to the attached models passes through the Guardrail: violating requests are rejected with HTTP 400 and a specific reason, while sensitive data is automatically masked/replaced/deleted before reaching the model — with zero changes on the client side.

| I want to... | Go to |
|---|---|
| Understand the three filter layers and their actions | [What is Guardrail?](README.md) |
| Scan the dynamic part of the system prompt | [Scan the system prompt with the `<vng-guard-content>` tag](scan-system-prompt-with-vng-guard-content-tag.md) |
| Look up limits, error messages, and FAQ | [Reference and FAQ](reference-and-faq.md) |
