# HiveLink

HiveLink defines the authenticated session between two HiveMesh peers. It treats the underlying connectivity mechanism as a replaceable byte or stream transport.

## Layering

```text
Hive application channels
  catalog | visibility | lease | invocation | events | receipts
                         |
Hive session
  authentication | version negotiation | multiplexing | heartbeat | resume
                         |
Transport adapter
  LAN | Tailscale | QUIC | WebRTC | WebSocket | relay
```

Transport selection affects reachability and performance, not peer identity or lease authority.

## Adapter requirements

A conforming adapter must provide:

- bidirectional connectivity between two daemons;
- confidentiality and integrity appropriate for carrying a Hive session;
- streaming and multiple concurrent logical operations;
- cancellation and backpressure support;
- connection status and direct-versus-relayed metadata;
- clean closure and a reconnect path.

The Hive session still performs peer authentication even when the underlying network authenticates users or devices.

## Connection candidates

An offer may advertise multiple candidates:

```json
{
  "type": "quic | tailscale | webrtc | websocket | lan | relay",
  "uri": "transport-specific URI",
  "priority": 100,
  "features": ["stream", "multiplex", "resume"]
}
```

Candidate URIs are sensitive connection hints, not credentials and not peer identity. Implementations should prefer a mutually supported direct path, may fall back to an overlay network, and may use an encrypted relay as a last resort.

## Handshake

```text
connect transport
  -> exchange HiveHello
  -> authenticate peer identities with fresh nonces
  -> negotiate protocol versions and capabilities
  -> bind session keys/transcript to both identities
  -> open standard channels
```

`HiveHello` carries protocol versions, peer identity, nonce, channel capabilities, and a proof over the handshake transcript. It does not carry leases or upstream credentials.

## Standard channels

- `catalog`: list and describe currently visible resources.
- `visibility`: receive visibility-lease and catalog-scope updates.
- `lease`: propose, counter, accept, supersede, and revoke leases.
- `invocation`: invoke resources and stream events.
- `event`: availability, state, and advisory notifications.
- `receipt`: usage receipt and acknowledgement exchange.

Channel identifiers and frame encodings remain to be fixed in the schemas.

## Reconnection and resumption

A new transport connection always performs fresh mutual authentication. Session resumption may restore negotiated capabilities and continue eligible operations, but must not bypass current lease, revocation, quota, or resource checks.

Each invocation has a stable ID. After reconnecting, peers may query its final status or resume only if the negotiated invocation profile supports safe resumption. Retrying a non-idempotent invocation under a new ID is an application decision and must not happen silently.

## Failure behavior

Transport loss closes no lease by itself. It may interrupt in-flight invocations, which then require an explicit terminal or unknown state and corresponding accounting treatment. Peers must tolerate candidate failure, session replacement, missed advisory events, and catalog refresh after reconnection.
