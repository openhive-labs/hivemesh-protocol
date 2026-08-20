# Protocol Specifications

This directory is the human-readable source for HiveMesh Protocol v0.1.

## Scope

The protocol specifies:

- stable peer identity and mutual authentication;
- resource descriptors and peer-filtered catalogs;
- one-time offers and bilateral acceptance;
- visibility and execution leases;
- transport-neutral HiveLink sessions and channels;
- model, agent, tool, workflow, and service invocation;
- streaming, cancellation, backpressure, and idempotency;
- usage receipts, expiry, supersession, and revocation.

The protocol does not specify a marketplace, payment system, global directory, reputation system, resource implementation, UI, or a mandatory connectivity vendor.

## Core model

```text
out-of-band offer / acceptance
              |
              v
Peer A <--- authenticated HiveLink session ---> Peer B
   |                                             |
local resources                            local resources
```

Both participants run the same peer protocol. Provider and consumer are roles within an individual lease and may be reversed by another lease.

## Specification index

- [Identity](identity/README.md)
- [Resources and Catalog](resources/README.md)
- [Leases](leases/README.md)
- [HiveLink](hivelink/README.md)
- [Invocation and Usage Receipts](invocation/README.md)
- [Security Model](security/README.md)

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** describe interoperability requirements. Until v0.1 is frozen, all requirements remain draft and may change through the protocol change process.

## Invariants

- A transport address is never a peer identity.
- Network reachability never grants resource access.
- Visibility does not grant invocation.
- A lease version is immutable after signing.
- A provider enforces its own exposure policy and execution limits.
- Upstream credentials never appear in protocol messages.
- A normal model-compatible interface remains a valid resource surface.
