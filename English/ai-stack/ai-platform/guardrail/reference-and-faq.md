# Reference and FAQ

This page consolidates limits, common error messages, and frequently asked questions for **Guardrail** on AI Platform. For step-by-step instructions, see [Using Guardrails](using-guardrails.md).

---

## Limits

| Item | Limit |
|---|---|
| Guardrails per account | Per system quota (exceeding it returns *"Exceeded GUARDRAILS quota."*). |
| Word filter | Up to 10,000 words; each word ≤ 100 characters and ≤ 3 words. |
| Prompt-based | Up to 10 rules; name ≤ 100 characters, unique. |
| Regex | Up to 10 patterns; pattern ≤ 255 characters, must be valid. |
| Score | 0.0 – 1.0 for both PII and regex; defaults to 0.5. |
| Model / Guardrail | Each model attaches to only one Guardrail at a time. |
| System prompt | Not scanned by default; use the `<vng-guard-content>` tag to scan it. |
| Self-hosted / BYOK models | Guardrail attachment not yet supported. |

---

## Common error messages

### Request returns 400

| Symptom | Cause | Fix |
|---|---|---|
| `Prompt has been rejected by AI Guard` + reason naming a rule | The prompt-based filter flagged a violation | Review the outgoing content; for false positives, narrow the rule description or switch the action to Detect. |
| `Request blocked due to detected keyword` | Word filter hit a keyword with action Block | Remove the word, or switch the action to Detect/Mask if it blocks in error. |
| 400 error when creating/editing on the Console | Field rule violation (malformed name, broken regex pattern, duplicate names…) | Read the error message — it names the offending field; cross-check the rules in [Create a Guardrail](using-guardrails.md#create-a-guardrail). |

### Guardrail does not block content it should

| Cause | Fix |
|---|---|
| The content is in a system prompt without a tag | Insert `<vng-guard-content>` before the part that needs scanning (see [Scan the system prompt](scan-system-prompt-with-vng-guard-content-tag.md)). |
| Misspelled tag | Copy the exact string `<vng-guard-content>` — a single wrong character makes it ineffective, with no error. |
| Prompt-based rule description too narrow | Add a clearer description / add rules for new violation patterns. |
| Model not attached to the Guardrail | Check the **Protected models** column; re-attach via **Manage Models**. |

### Operation rejected

| Message | Cause | Fix |
|---|---|---|
| `Guardrails is attached to one or more models. Please detach them first.` | Deleting a Guardrail that still has attached models | Manage Models → deselect all → Save → delete again. |
| `Model 'X' is already attached to guardrails 'Y'` | The model belongs to another Guardrail | Detach the model from Guardrail 'Y' first. |
| `Another guardrails attach/detach is in flight for this user. Please wait.` | Another attach/detach operation is running | Wait a few seconds and retry. |
| `Guardrails is <status>. Please wait for provisioning to complete and retry.` | The Guardrail is not ACTIVE yet | Wait until the status is ACTIVE and retry. |
| `Exceeded GUARDRAILS quota.` | Guardrail quota exceeded | Delete unused Guardrails or contact support to raise the quota. |

---

## FAQ

### Does Guardrail slow down requests?

Somewhat. Word filters are fast; the prompt-based filter calls an additional "judge" model and is slower. For clean content, this overhead is small compared to the main model's response generation time.

### Do I have to change my client code?

No. Attach the Guardrail to a model and you are done — endpoint, headers, and body stay the same.

### Why am I getting a 400 "Prompt has been rejected by AI Guard"?

The request content (or the system prompt portion after the `<vng-guard-content>` tag) was flagged by the prompt-based filter. The `reason` field tells you which rule caught it — see [Common error messages](#common-error-messages).

### Can Sensitive information "Block"?

No. This filter always rewrites content (mask/replace/delete) before sending — the goal is that data reaching the model is already safe.

### What if my system prompt happens to contain the exact string `<vng-guard-content>`?

That is exactly how the tag works: everything from that position onward will be scanned. If that is not what you want, rephrase the prompt to avoid the exact tag string.

### How many models can one Guardrail attach to?

As many as you like (among the MaaS models you have enabled), as long as each model is not already attached to another Guardrail.

### Do I need to re-attach models after changing a Guardrail's configuration?

No. Configuration changes apply automatically to attached models (the Guardrail is in UPDATING status while applying).

### Does Guardrail also apply to model responses?

Currently Guardrail checks input requests (the content you send). Model responses do not pass through Guardrail yet.

---

## When to contact support

If the error persists or is not covered above, contact support with:

- The **Guardrail ID** (`gr-…`) and affected **model**
- The **time** it occurred and the **region**
- The **HTTP status** and error content received
