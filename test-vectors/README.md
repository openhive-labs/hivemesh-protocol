# Test Vectors

Canonical vectors will cover:

- peer ID and key encoding;
- HiveHello challenge and transcript signatures;
- offer, acceptance, and one-time nonce consumption;
- visibility and execution lease canonicalization and signatures;
- supersession and revocation verification;
- invocation IDs, sequence handling, and replay rejection;
- completed, cancelled, failed, and incomplete usage receipts;
- malformed encodings and invalid signatures.

Vectors must include exact bytes, decoded representation, expected hash or signature, and the expected verification result. Security formats require both valid and intentionally invalid cases.

Deterministic signing, verification, serialization, and handshake vectors belong here.
