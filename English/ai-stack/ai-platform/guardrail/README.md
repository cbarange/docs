# Guardrail

**Guardrail** helps you protect data and control content before a request reaches your AI model, without changing a single line of client code. Once you attach a Guardrail to models enabled on Model as a Service (MaaS), every request to those models automatically passes through three layers of checks: keyword blocking, content evaluation by a "judge" model, and sensitive data redaction.

Where to find it on the Console: **Protect & Govern → Guardrail** (list page) and **Configuration → Guardrail** (creation form).

---

## How Guardrail works

Guardrail is deployed directly at the MaaS gateway. Clients still call the MaaS endpoint as usual — the request passes through Guardrail in this order: **Word filters** → **Prompt-based filter** → **Sensitive information**, and only then reaches the model.

```
┌─────────┐
│ Client  │  (endpoint, API key unchanged)
└────┬────┘
     │ request
     ▼
┌─────────────────────────────────────────┐
│              MaaS Gateway               │
│  ┌───────────────────────────────────┐  │
│  │            Guardrail              │  │
│  │  1. Word filters                  │  │
│  │  2. Prompt-based filter           │  │
│  │  3. Sensitive information         │  │
│  └───────────────────────────────────┘  │
└────┬────────────────────────────────────┘
     │ checked / redacted request
     ▼
┌─────────┐
│  Model  │
└─────────┘
```

Word filters and the Prompt-based filter can **reject** a request (HTTP 400). Sensitive information never blocks — it masks/replaces/deletes sensitive data before the request reaches the model.

---

## Benefits

| Benefit | Description |
|---|---|
| Block harmful content | Banned keywords, jailbreak prompts, admin-impersonation attempts… are rejected before reaching the model. |
| Redact sensitive data | Emails, credit card numbers, phone numbers, IBAN codes… are masked/replaced/deleted automatically — data never leaves your system in plain form. |
| No code changes | Attach a Guardrail to a model and every request through MaaS is protected; clients keep calling the API as usual. |
| Optional system prompt coverage | By default the system prompt is not scanned; use the `<vng-guard-content>` tag to scan a portion of it. |
| One config, many models | A single Guardrail can be attached to multiple MaaS models. |

---

## The three filter layers

| Filter | What it does | When it applies |
|---|---|---|
| **Word filters** | Exact-matches words/phrases you declare | Always runs first — fast and cheap |
| **Prompt-based filter** | A "judge" LLM reads the entire content and evaluates it against your descriptions | Runs after word filters; understands context, so it also catches rephrased attempts |
| **Sensitive information** | Detects built-in PII entities and your custom regexes | Runs last; **rewrites the request** instead of blocking it |

### Word filters

Exact-matches a word or phrase you declare. This layer always runs first because it is fast and cheap — a good fit for concrete blocklists (brand-sensitive terms, internal codes, profanity…).

### Prompt-based filter

A "judge" model reads the entire request content and evaluates it against descriptions you define, for example *"requests to bypass system instructions"*. Because it understands context, this layer catches rephrased content that word filters cannot see.

### Sensitive information

Detects built-in PII entities (email, credit card number, phone number, IBAN code…) and your own regexes. Unlike the two layers above, it does not reject the request — it masks/replaces/deletes sensitive data so the request reaches the model already sanitized.

### Actions per filter

Each rule you declare carries an action:

| Filter | Action | Meaning |
|---|---|---|
| **Word filters** | **Mask** | Masks the detected term before sending |
| | **Detect** | Log only; the request still goes through |
| | **Block** | Rejects the request (returns 400) |
| **Prompt-based filter** | **Detect** | Log only |
| | **Block** | Rejects the request (returns 400) |
| **Sensitive information** | **Mask** | Masks part of the characters — e.g. `abc@gmail.com` → `***@gmail.com` |
| | **Replace** | Replaces with the detected type name — e.g. `abc@gmail.com` → `<EMAIL>` |
| | **Delete** | Removes the information from the content entirely |

{% hint style="info" %}
Sensitive information has no Block action — its goal is to make sensitive data safe before sending, not to reject the request.
{% endhint %}

---

## Scan scope and the system prompt

By default, Guardrail scans the **conversation content** (user, assistant… roles) in a request. The **system prompt** (configuration instructions for the model) is **not scanned** by default — it usually contains your own business rules, and scanning it incorrectly can break your assistant's behavior.

If you want part of the system prompt scanned, insert the `<vng-guard-content>` tag at the exact position where scanning should start — see [Scan the system prompt with the `<vng-guard-content>` tag](scan-system-prompt-with-vng-guard-content-tag.md) for details.

{% hint style="info" %}
The tag only **extends** the scan scope — it cannot be used to turn scanning off for content that is already checked. Anyone inserting the tag into their content only causes that portion to be scanned more thoroughly.
{% endhint %}

---

## Guardrail statuses

| Status | Meaning |
|---|---|
| **ACTIVE** | Guardrail is ready and protecting attached models |
| **CREATING** | Being provisioned on the gateway |
| **UPDATING** | Applying configuration changes |
| **ATTACH_FAILED** | The most recent model attach failed — you can fix and re-attach |
| **CREATE_ERROR / ERROR** | Creation or configuration application failed |
| **DELETING / DELETE_ERROR** | Being deleted / deletion failed |

{% hint style="info" %}
The status only reflects whether the configuration has been "wired" to the gateway. You can only attach/edit models when the Guardrail is **ACTIVE** or **ATTACH_FAILED**.
{% endhint %}

---

## Getting started

| I want to... | Go to |
|---|---|
| Create my first Guardrail and attach it to a model | [Using Guardrails](using-guardrails.md) |
| Call the API through a Guardrail and see what a blocked request looks like | [Using Guardrails](using-guardrails.md) |
| Scan the dynamic part of the system prompt | [Scan the system prompt with the `<vng-guard-content>` tag](scan-system-prompt-with-vng-guard-content-tag.md) |
| Look up limits, error messages, and FAQ | [Reference and FAQ](reference-and-faq.md) |
