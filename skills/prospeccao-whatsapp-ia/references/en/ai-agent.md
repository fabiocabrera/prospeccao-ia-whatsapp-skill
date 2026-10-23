# AI Agent

## Structured prompt

Organize the system prompt with simple XML tags, for example:

```xml
<identity>...</identity>
<goal>...</goal>
<tone>...</tone>
<limits>...</limits>
<qualification>...</qualification>
<tool_use>...</tool_use>
<handoff>...</handoff>
```

Separate company facts, operational rules, and conversation instructions. Tell the model not to invent prices, availability, or prospect data. If asked directly, the AI must not deny that it is an AI.

## WhatsApp replies

Prefer short, natural messages. When a reply needs several ideas, split it into a small number of parts and send them with a configurable interval. Limit the number of parts. Do not split first-contact templates as if they were multiple free-form messages.

Group rapid messages from the lead with debounce. Every new message restarts the window. At flush time, process the set once. Use a key scoped by tenant, channel, and contact, a buffer limit, and a comparison token to prevent two flushes.

## RAG over PDFs and FAQs

Extract real text from PDFs. Do not embed the raw file bytes: indexing a PDF as binary produces a useless knowledge base. Normalize the text, preserve page references, split it into overlapping chunks, and store the embedding model used.

During retrieval, always filter by `tenantId` and agent. Do not compare vectors produced by different models. Include only the required passages and instruct the model to admit when the knowledge base does not contain the answer.

## Material library and tools

Register materials with type, size, a description of when to use them, and a safe reference. The model selects by description and calls a tool such as `send_material` with an allowed identifier. The tool validates tenant, 24-hour window, type, size, and file existence.

Never accept a reply in which the AI promises to send material without calling the tool in the same turn. Treat this as an observable failure and record the reason.

## Qualification and CRM

Collect only what is needed, one question at a time. Examples include need, context, timing, and interest in speaking with a person. When the criteria are met, use a tool to create or update the lead. The operation must be idempotent and linked to the conversation and tenant.

Do not invent a decision-maker's phone number, revenue, or stage. Store a conversation-based summary, source, explainable temperature, and next step. Handoff when requested, when there is sensitive uncertainty, or when a rule requires human service.

## Decision order

Before calling the LLM, evaluate: opt-out, blocklist, enabled module, workspace-wide pause, conversation pause, handoff, campaign AI setting, schedule, keyword rules, and manual approval. Only then run RAG, the LLM, and tools.
