# Identity

Peer identity is the stable authorization principal used by catalogs, leases, sessions, invocations, and receipts.

## Identity document

A peer identity document contains at least:

```json
{
  "version": "hive-identity/0.1",
  "peer_id": "peer:...",
  "signing_keys": [
    {"key_id": "key:...", "algorithm": "...", "public_key": "..."}
  ]
}
```

The final key algorithms, encodings, and `peer_id` derivation are not frozen until schemas and test vectors are published.

## Requirements

- A peer **MUST** control the private key corresponding to its advertised signing key.
- Lease subjects and issuers **MUST** be peer identities or explicitly scoped peer keys, never IP addresses, DNS names, tailnet users, or bearer URLs.
- Private keys **MUST NOT** leave the peer that owns them.
- A peer **MUST** reject a signature whose key is not valid for the claimed identity and protocol purpose.
- Implementations **MUST** keep network candidates and peer identity as separate fields.

## Session authentication

After establishing any transport connection, peers exchange a signed challenge:

```text
transport connected
  -> exchange protocol version, peer ID, key ID, and fresh nonce
  -> each side signs the transcript or bound challenge
  -> each side verifies possession and expected identity
  -> negotiate capabilities
  -> open HiveLink channels
```

The authenticated transcript **MUST** bind both peer identities, both nonces, the negotiated protocol version, and the current connection. A captured proof from one session must not authenticate another.

## Invitation binding

A one-time invitation may introduce an expected provider identity and connection candidates. Acceptance binds the consumer identity to the offer. The human-readable code or nonce is only a first-use confirmation factor; it is not a reusable API credential.

An implementation **MUST** reject:

- an expired offer;
- a previously consumed one-time nonce;
- an acceptance signed by a different consumer from the activated lease;
- a session whose authenticated provider does not match the offer.

## Key lifecycle

Peer-key rotation and recovery are protocol-visible operations because active leases refer to peer identity. v0.1 may initially require explicit re-pairing after key loss. Silent replacement of an identity key is forbidden.
