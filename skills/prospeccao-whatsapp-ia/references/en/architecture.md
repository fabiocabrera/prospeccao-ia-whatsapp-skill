# Architecture

## Goal

Build a multi-tenant SaaS that discovers companies, prepares contacts, sends official WhatsApp messages, replies with AI, and records opportunities. Keep every stage auditable and reversible.

## Main flow

```text
Google Places
  -> normalization and deduplication
  -> company-registry enrichment
  -> prospect review and approval
  -> campaign and queue
  -> separate worker
  -> WhatsApp Cloud API
  -> reply webhook
  -> rules, debounce, RAG, and LLM
  -> conversation, qualification, and CRM
```

The system must not claim to obtain revenue figures or a decision-maker's phone number. Treat missing fields as missing.

## Suggested stack

- Frontend: React with Vite.
- API: Node.js with Fastify and TypeScript.
- Persistence: Prisma with Supabase Postgres.
- Vectors: the pgvector extension in Postgres.
- Ephemeral state, locks, and debounce: Redis.
- Delivery: a worker in a process separate from the API.
- Channel: Meta's official WhatsApp Cloud API.
- AI: an LLM provider configurable per company.

## Multi-tenancy from the first schema

Use `tenantId` on prospects, searches, campaigns, queues, conversations, messages, agents, documents, chunks, leads, blocklists, and events. Resolve the tenant from the session in authenticated APIs and from the official number or channel in webhooks. Never treat a `tenantId` sent by the browser as authorization.

Create compound indexes for frequent queries, such as `(tenantId, status)`, `(tenantId, conversationId)`, and external identifiers unique within a tenant. Test that a user from one company cannot read or change another company's data.

## Recommended domains

- `identity`: company, user, membership, and roles.
- `prospecting`: search, prospect, campaign, cadence, enrollment, and queue.
- `channels`: official connection, template, send, webhook, and status.
- `ai`: agent, rules, RAG, tools, and usage.
- `crm`: contact, conversation, lead, stage, task, and appointment.
- `audit`: operational events and recoverable failures.

## Queue and worker

The API creates items in `draft`. An explicit action releases the campaign. The worker processes only due items belonging to active, unlocked campaigns. Before every send, check opt-out, blocklist, previous reply, daily limit, operating hours, and channel state.

Use an atomic lock per item, an attempt identifier, and message idempotency. Preserve sent, paused, and failed items for audit. The webhook should acknowledge receipt quickly and delegate heavy processing to a queue.

## Minimum states

- Prospect: captured, approved, in campaign, replied, opted out, archived.
- Campaign: draft, ready, active, paused, completed, canceled.
- Queue: draft, scheduled, sending, sent, paused, failed, canceled.
- Conversation: open, handoff, closed; with AI enabled or paused.

## Architecture pitfalls

- Simultaneous deployments can exceed the database connection limit. Set a small per-process limit and consider a pooler compatible with your connection mode.
- A numeric field set to `0` is a valid configuration. Do not use truthy/falsy coercion that replaces it with a default.
- Limits may exist in the interface, API, campaign, and worker. Define one source of truth and display the effective ceiling.
- Schema migration must not depend on the build alone. Create an explicit, verifiable production step.
