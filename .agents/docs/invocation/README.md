# Invocation

Invocation uses an active execution lease to call a resource through the provider peer. Model resources retain ordinary model-compatible semantics; HiveMesh adds identity, lease proof, routing, streaming, and metering around the request.

## Invocation envelope

```json
{
  "version": "hive-invoke/0.1",
  "invocation_id": "invoke:...",
  "lease_id": "lease:...",
  "resource_id": "resource:provider/local-coder",
  "protocol": "openai-compatible",
  "operation": "responses.create",
  "created_at": "...",
  "idempotency_key": "optional",
  "payload": {}
}
```

The authenticated HiveLink session identifies the consumer. The provider validates the envelope against the execution lease before forwarding any payload to the local resource.

HiveMesh protocol messages **MUST NOT** contain the provider's upstream credential. Provider adapters inject local credentials only after authorization.

## Model-compatible baseline

The protocol supports projecting leased model resources through familiar local interfaces, including:

- `GET /v1/models`
- `POST /v1/responses`
- `POST /v1/chat/completions`
- `POST /v1/messages` when an Anthropic-compatible profile is negotiated.

Streaming, tools, vision, log probabilities, and other capabilities are advertised per resource. Unsupported or unleased optional capabilities must fail explicitly. A generic task or worker API may coexist but cannot be the only way to use a model resource.

## Stream events

A stream carries ordered events with a monotonically increasing sequence number:

- `accepted`
- `output.delta`
- `tool_call.delta`
- `usage.delta` when available
- `completed`
- `cancelled`
- `failed`
- `accounting.incomplete`

The final schema will define event payloads and which terminal states are mutually exclusive. Consumers must ignore duplicate sequence numbers and detect gaps after reconnection.

## Cancellation and backpressure

The consumer may request cancellation by invocation ID. The provider must stop accepting further input and make a best effort to cancel local work. Cancellation is complete only after a terminal event or explicit unknown state.

Both peers must apply bounded buffering. A slow consumer may cause provider-side throttling or cancellation instead of unbounded memory growth.

## Usage receipt

The provider emits a signed receipt containing at least:

```json
{
  "version": "hive-receipt/0.1",
  "receipt_id": "receipt:...",
  "invocation_id": "invoke:...",
  "lease_id": "lease:...",
  "provider": "peer:provider",
  "consumer": "peer:consumer",
  "resource_id": "resource:provider/local-coder",
  "started_at": "...",
  "ended_at": "...",
  "status": "completed | cancelled | failed | incomplete",
  "usage": {
    "requests": 1,
    "input_tokens": 0,
    "output_tokens": 0
  },
  "sequence_final": 0,
  "provider_signature": "..."
}
```

The consumer may acknowledge or countersign a receipt. Acknowledgement means receipt, not agreement with quality or payment. Missing upstream token counts must not be invented; the receipt should distinguish measured, provider-reported, estimated, and unavailable values in the final schema.

## Replay and retry

- A completed invocation ID must not execute again.
- The same idempotency key under the same lease and operation should resolve to the previously known invocation where the resource profile supports idempotency.
- A provider must not charge a second request merely because a terminal receipt was retransmitted.
- An invocation with an uncertain outcome remains explicit; peers must not silently treat it as either free or completed.

## Errors

Protocol errors distinguish at least authentication failure, inactive lease, scope denial, quota exhaustion, rate limit, concurrency limit, unavailable resource, unsupported capability, malformed payload, cancellation, transport interruption, and provider failure. Provider errors must be sanitized so that local paths, credentials, and private adapter details are not disclosed.
