# Leases

A HiveLease is an immutable, signed capability grant between peers. It defines who may perform which actions, against which resources, under which limits, and for what time interval.

## Lease types

### Visibility lease

Controls discovery:

- `catalog:list`
- `resource:describe`
- `lease:request`

It may limit resource selectors and individual descriptor fields. Visibility never implies invocation.

### Execution lease

Controls use of one or more specific resources:

- `resource:invoke`
- protocol-specific optional actions such as tool invocation or session creation.

It carries execution limits such as start time, expiry, requests, input/output tokens, rate, and concurrency.

## Common lease object

```json
{
  "version": "hive-lease/0.1",
  "lease_id": "lease:...",
  "revision": 1,
  "issuer": "peer:provider",
  "subject": "peer:consumer",
  "kind": "execution",
  "grants": [
    {
      "resource": "hive://peer:provider/resources/local-coder",
      "actions": ["resource:invoke"]
    }
  ],
  "limits": {
    "not_before": "...",
    "expires_at": "...",
    "max_requests": 100,
    "max_input_tokens": 500000,
    "max_output_tokens": 100000,
    "max_concurrency": 2,
    "max_rpm": 10
  },
  "parent_lease": "lease:visibility-optional",
  "supersedes": null,
  "signatures": {
    "issuer": "...",
    "subject": "..."
  }
}
```

Canonical serialization and signatures will be fixed by schemas and test vectors. Fields not understood by a verifier must follow the versioned compatibility policy; security-sensitive limits must never be silently ignored.

## Negotiation

```text
provider creates signed offer
  -> consumer verifies and accepts or counters
  -> provider verifies final terms
  -> both sign the same canonical lease
  -> both persist the activated lease
```

An offer may travel through IM, QR, or a file. A central service is optional and receives no inherent protocol authority.

The offer contains a fresh nonce, expiry, expected provider identity, and connection candidates. A one-time offer can activate at most one consumer identity.

## Lifecycle

```text
proposed -> accepted -> active -> expired
                         |  |
                         |  +-> exhausted
                         +----> revoked
```

- A lease is `active` only within its time interval, with valid signatures, no effective revocation, and remaining applicable quota.
- Lease content is immutable. Changes create a new revision with `supersedes` pointing to the previous version.
- A verifier **MUST** prevent two competing revisions from both being treated as current.
- Revocation is a signed protocol event and **MUST** be durable locally.
- If an execution lease names a parent visibility lease, revocation or expiry of that parent also makes the execution lease unusable unless the execution lease explicitly defines independent continuation.

## Enforcement

The provider is authoritative for admission and quota enforcement because it controls execution. The consumer may independently track limits and verify receipts, but its local count does not compel the provider to accept a request.

Every invocation **MUST** identify the execution lease and authenticated consumer peer. A lease is not transferable as a bearer token.

The provider must reject invocation when:

- the lease is not active;
- the consumer identity differs from the lease subject;
- the resource or action is outside the grant;
- a request, token, rate, or concurrency limit would be exceeded;
- the lease or an effective parent has been revoked;
- the invocation ID is an invalid replay.

## Clock and offline behavior

Peers should use bounded clock-skew tolerance defined by the final profile. An already active lease may be verified without a central service. Revocation propagation is peer-to-peer; a disconnected provider always retains the ability to disable a resource or lease locally.
