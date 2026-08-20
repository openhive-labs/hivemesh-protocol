# Schemas

Versioned machine-readable definitions will be added here for:

- peer identity documents and signed handshake transcripts;
- connection candidates and offer capsules;
- resource descriptors and catalog responses;
- visibility and execution leases;
- lease proposals, acceptance, supersession, and revocation;
- HiveLink channel frames;
- invocation envelopes and stream events;
- usage receipts and acknowledgements;
- protocol error objects.

Every schema must identify its protocol version, define unknown-field behavior, distinguish required from optional security-sensitive fields, and have matching positive and negative test vectors.

No placeholder schema should be treated as wire-stable until its canonical encoding and signature coverage are documented.

Machine-readable protocol schemas belong here.
