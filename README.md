# HiveMesh Protocol

Normative specifications, schemas, test vectors, and conformance material for HiveMesh interoperability.

HiveMesh Protocol defines how independent peers identify each other, describe resources, grant visibility and execution rights, establish transport-neutral sessions, invoke resources, and account for usage.

## Repository map

- `.agents/docs/`: human-readable protocol specifications.
- `schemas/`: versioned machine-readable message and object definitions.
- `test-vectors/`: canonical examples for signatures, hashes, leases, and receipts.
- `conformance/`: implementation-independent behavioral cases.
- `.agents/rules/`: protocol governance and compatibility policy.

The specification is transport-vendor neutral. Tailscale, QUIC, WebRTC, WebSocket, LAN, and relay connectivity are adapters beneath the same HiveLink session and lease semantics.

## Status

The current material is a **v0.1 pre-implementation draft**. It captures accepted architecture from product planning, but wire encodings and cryptographic suites remain provisional until represented in `schemas` and `test-vectors`.
