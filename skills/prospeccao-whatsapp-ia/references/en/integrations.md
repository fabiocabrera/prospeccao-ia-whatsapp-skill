# Integrations

## Google Places

Use text search to combine a segment and a location. Paginate up to a maximum of 60 results per query. Apply a configurable exclusion-word list after normalizing the name, category, and address. Deduplicate by place identifier and, as a fallback, by normalized phone number.

Restrict the key at the provider: limit the allowed API and the IPs or origins that match where the request runs. Keep the key on the backend only. Record cost, duration, received count, and discard reason without logging the credential.

## Company-registry enrichment

Use an external provider behind your own interface. Matching usually has a modest success rate and works better for companies with their own website. Store the source, confidence, date, and match criteria. Do not turn an inference into a confirmed fact.

Run enrichment after the initial review or only for approved prospects. The system described here does not promise revenue data or a decision-maker's phone number.

## Meta WhatsApp Cloud API

Each company connects its WABA and one or more numbers. Use a permanent token issued to a system user and keep it encrypted on the server. Tokens can expire, be revoked, or lose permissions; track validity, renew when applicable, and raise an operational alert on failure.

In the webhook:

- implement the verification challenge;
- validate `X-Hub-Signature-256` against the raw body;
- resolve the tenant from the received phone-number identifier;
- deduplicate by message identifier;
- acknowledge quickly and process the rest in the background.

The first prospecting contact must use an approved template. Free-form text should be sent only within the 24-hour window opened by a lead message. Verify template status on the server immediately before preparing or releasing a campaign.

Media limits to consider:

- video: 16 MB;
- image: 5 MB;
- document: 100 MB.

Validate size and type before upload and again before sending. Keep a margin below the ceiling for files that may be transcoded.

## LLM

Hide the provider behind an interface. Configure model, temperature, output limit, and credential per tenant. Apply timeout, limited retries with backoff, and usage measurement. Never send secrets or unnecessary data in the prompt.

## Redis

Use Redis for debounce, short-lived locks, rate limiting, and lightweight queues. Include `tenantId`, channel, and contact in keys. Set a TTL. If Redis fails, choose a safe behavior: do not duplicate sends and, for replies, use synchronous fallback only when the risk is controlled.

## Contracts and tests

Create adapters for every integration and test them with fixtures. Separate read tests from write tests. Never call a production write endpoint merely to discover its behavior. Use a sandbox, staging environment, or a documented simulation.

## Pitfalls

- A subsequent Google page may require a wait or a specific token. Model pagination instead of merely increasing `limit`.
- The webhook may be retried. Idempotency is mandatory.
- A Meta token that works today may expire. Renewal and alerting are part of the integration.
- A ceiling displayed in the UI may be overridden or reduced by the worker. Expose the effective limit.
