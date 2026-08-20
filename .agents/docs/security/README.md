# Security Model

## Trust boundary

HiveMesh protects provider credentials and enforces peer-specific access. It does not make consumer prompts confidential from the provider that executes them, and it does not prove that either peer's local software or model behaves honestly.

## Security goals

- Authenticate the peer identity on every HiveLink session.
- Bind leases and invocations to peer identity rather than bearer secrets or transport addresses.
- Keep provider credentials and administrative interfaces local.
- Prevent catalog, resource, and quota access from crossing lease boundaries.
- Detect invitation, lease, invocation, and receipt replay.
- Make expiry, exhaustion, supersession, and revocation durable.
- Avoid leaking hidden inventory through catalog responses or errors.

## Explicit non-goals for v0.1

- Confidential execution from the provider.
- Guaranteed prevention of model extraction or output collection.
- Anonymous public-market fraud prevention.
- Global consensus on usage or payment.
- Recovery from a fully compromised peer host.

## Required checks

Before invocation, the provider validates:

1. authenticated session identity;
2. lease signatures and current revision;
3. validity interval and revocation state;
4. resource and action scope;
5. request, token, rate, and concurrency limits;
6. resource availability and local exposure policy;
7. invocation replay and envelope validity.

## Sensitive data

Protocol objects must not include upstream API keys, OAuth refresh tokens, browser cookies, model-administration credentials, private prompts, or local management endpoints. Connection candidates, offers, catalogs, and receipts may still be sensitive metadata and should be protected in transit and at rest.

## Abuse and extraction resistance

Providers may enforce cumulative input/output quotas, rate and concurrency limits, optional capability restrictions, cross-lease risk scoring, or output fingerprinting. These mechanisms must not silently alter the declared normal model API contract. They raise extraction cost and improve attribution but cannot guarantee prevention.

## Threat-analysis queue

The protocol cannot freeze until tests cover offer theft, nonce replay, unknown-key acceptance, candidate substitution, lease forwarding, conflicting revisions, revocation races, invocation replay, cross-resource routing, quota races, receipt duplication, and reconnect ambiguity.
