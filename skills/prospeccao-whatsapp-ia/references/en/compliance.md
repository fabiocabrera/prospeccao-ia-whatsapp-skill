# Security and Compliance

## Required controls

- Immediate, persistent opt-out checked before every send.
- Approved template for the first contact.
- Automatic cadence pause when the lead replies.
- Workspace-wide AI pause.
- Per-conversation AI pause.
- Detection of human replies sent from the phone or inbox.
- Operating hours configurable by tenant and time zone.
- Blocklist by phone number and external identifier.
- Clear handoff to human service.

A human reply is not inbound from the lead. Record it as outbound, do not trigger another AI reply, and disable AI for the conversation when the handoff policy is active. A closed conversation must not be reopened by a phone echo.

## Transparency

The AI may introduce itself by its commercial role without starting every message with a technical disclosure. However, if the person asks directly, it must not deny being an AI. Offer a transfer to a person.

## Isolation and authorization

All operational tables and queries use `tenantId`. Resolve the company from the session or validated channel. Check membership and role on the backend. The frontend does not define authorization.

Use RLS as an additional layer when there is direct database access, but keep mutations behind the API. Test cross-tenant access, background jobs, and webhooks, not just authenticated screens.

## Secrets

Encrypt tokens, keys, and credentials at rest. Keep the encryption key outside the database. Never return the complete secret to the frontend or include it in logs. Separate credentials by environment and rotate them.

## Webhooks and data

Validate the signature over the raw body before interpreting the payload. Deduplicate events. Limit body size and accepted types. Store only what is necessary. Define retention, auditing, and deletion procedures.

## Safe operations

Campaigns start locked. Require explicit review and release. Apply a daily limit, rate limit, schedule, and pause button. The worker must recheck every block immediately before sending.

Do not test production write endpoints “to see what happens.” Use mocks, a sandbox, or a staging tenant with authorized recipients. Record administrative actions without including secrets.

## Compliance

Map the applicable legal basis, purpose, and retention with local legal guidance. Follow Meta policies, Google rules, access and deletion requests, and contact preferences. Maintain proper identification and a simple unsubscribe mechanism.

This project does not guarantee compliance by itself. It also makes no promise of sales or conversion results.
