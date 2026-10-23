# Build Prompts

Paste the prompts below into Claude Code in order. Before each stage, provide the repository and decisions already made. Ask for small changes and verify the definition of done before moving on.

## 1. Multi-tenant foundation

> Create the foundation of a multi-tenant SaaS with React/Vite, Node/Fastify, TypeScript, Prisma, and Postgres. Model Tenant, User, and Membership. Every operational entity must have tenantId. Resolve the tenant from the session on the backend. Add tests proving that one company cannot access another company's data. Do not implement external integrations yet.

**Done when:** the schema validates, authentication and authorization run on the backend, the isolation test passes, and no secret is present in the frontend.

## 2. Prospecting and CRM domain

> Model searches, prospects, campaigns, cadences, enrollments, queue items, opt-outs, events, contacts, conversations, messages, and leads. Define explicit states and transitions. Campaigns must start locked. Preserve history for audit. Treat zero as a valid value in numeric settings.

**Done when:** migrations apply to a clean database, invalid transitions are rejected, and every query filters by tenantId.

## 3. Google Places

> Implement a backend adapter for Google Places Text Search. Combine segment and location, paginate up to 60 results, normalize and deduplicate, apply exclusion words, and store only required fields. Keep the key on the server and restrict it at the provider. Add metrics without exposing the key.

**Done when:** fixtures cover multiple pages, exclusions, and duplicates; a controlled search never exceeds 60 results.

## 4. Company-registry enrichment

> Create an interface for a company-registry provider. Enrich approved prospects only. Store source, confidence, date, and failure reason. Treat a match as uncertain and do not invent data. Do not claim to provide revenue or a decision-maker's phone number.

**Done when:** success, no match, ambiguity, rate limiting, and provider outage have clear, tested states.

## 5. WhatsApp Cloud API

> Implement a tenant-scoped connection with WABA, phone number, and an encrypted system-user token. Implement webhook verification, X-Hub-Signature-256 validation over the raw body, deduplication, and fast acknowledgement. Process events in the background. Use approved templates for first contact and free-form text only inside the 24-hour window.

**Done when:** an invalid signature is rejected, repeated events do not duplicate messages, the tenant is resolved from the number, and first contact without a template is blocked.

## 6. Queue and worker

> Create a separate worker for scheduled items. Use atomic claims, locks with TTL, and idempotency. Before sending, check active campaign, removed safety lock, opt-out, blocklist, lead reply, schedule, limit, and channel state. Record success and failure. Set a per-process database connection limit.

**Done when:** two concurrent workers do not duplicate a send, a restart recovers an expired lock, and a pause prevents new sends.

## 7. Webhook, pause, and human takeover

> When a lead replies, pause the enrollment before running any AI. Also detect messages sent by a person from the phone or inbox; record them as outbound and apply handoff without triggering AI. Add workspace-wide pause, per-conversation pause, blocklist, opt-out, and operating hours.

**Done when:** tests cover inbound messages, human echoes, closed conversations, opt-out, and every pause layer.

## 8. Agent, debounce, and RAG

> Implement an agent with an XML prompt, short replies split into limited parts, and a configurable interval. Group rapid messages with Redis debounce. For RAG, extract real text from PDFs and FAQs, create chunks, embeddings, and retrieval filtered by tenant and agent. Never index raw PDF bytes.

**Done when:** a message burst creates one reply, a PDF returns readable passages, and there is no leakage between tenants or embedding models.

## 9. Tools, materials, and qualification

> Implement typed tools to send material and create or update a lead. The AI may promise material only when it calls the tool in the same turn. Validate file, tenant, window, and limits of 16 MB for video, 5 MB for image, and 100 MB for documents. Keep qualification short and idempotent.

**Done when:** a promise without a tool is blocked, an oversized file fails before reaching the provider, and one conversation cannot create duplicate leads.

## 10. Interface, observability, and release

> Build the Discover, Review, Prepare, Send, and Monitor flow. Display the effective ceiling when limits exist in several layers. Add audit, metrics, Meta token alerts, worker status, and pause buttons. Create preflight checks for secrets, database, Redis, templates, and policies before production.

**Done when:** an operator can understand why every item sent, paused, or failed; no secret appears in the bundle or logs; all checks pass in staging.

## 11. Final security review

> Perform a read-only review. Look for missing tenantId filters, direct production writes, secrets in logs, zero values replaced by defaults, conflicting limits, binary PDF data in RAG, material promises without tool calls, slow webhooks, fragile locks, excessive database connections, and Meta tokens without renewal. List findings by risk before making fixes.

**Done when:** findings are documented with evidence, priority, and a proposed regression test.
