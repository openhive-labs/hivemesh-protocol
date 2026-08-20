# Conformance

Conformance cases describe externally observable behavior without depending on the official implementation.

Minimum v0.1 suites:

- two-peer mutual authentication;
- one-time offer activation and replay rejection;
- field-level catalog visibility;
- execution admission and denial;
- quota, rate, concurrency, expiry, and revocation enforcement;
- lease supersession and conflicting-revision handling;
- streaming order, cancellation, and backpressure;
- reconnect and invocation-status recovery;
- usage-receipt verification and duplicate handling;
- transport substitution without identity or lease changes.

A conforming implementation must pass the relevant suite for every declared capability and transport adapter.

Implementation-independent compatibility fixtures and expected outcomes belong here.
