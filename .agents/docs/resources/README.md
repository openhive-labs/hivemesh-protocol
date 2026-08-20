# Resources and Catalog

A resource is a locally owned capability that a peer may describe, reveal, and lease without revealing its internal implementation or upstream credentials.

## Resource kinds

v0.1 recognizes an extensible set of kinds:

- `model`
- `agent`
- `tool`
- `workflow`
- `service`

Unknown kinds may be displayed as generic services but **MUST NOT** be invoked unless both peers negotiate a compatible protocol.

## Resource descriptor

```yaml
version: hive-resource/0.1
resource_id: resource:provider/local-coder
kind: model
name: Local Coder
protocols:
  - name: openai-compatible
    operations:
      - models.list
      - responses.create
      - chat.completions
capabilities:
  streaming: true
  websocket: false
  tools: true
  input_modalities: [text]
  output_modalities: [text]
  logprobs: false
availability: online
lease_modes:
  - session
  - capability
```

A descriptor MAY include public pricing hints, capacity, context limits, data-handling declarations, or expected latency. It **MUST NOT** contain upstream tokens, cookies, private prompts, local filesystem paths, administrative endpoints, or credentials.

Protocol support is declared per operation. Advertising `openai-compatible`, `anthropic-compatible`, `gemini-compatible`, `codex-compatible`, or another dialect does not imply that every route in that ecosystem is available. Streaming, WebSocket, tools, image input, image output, and usage reporting remain independent capability fields.

`resource_id` is stable within the provider identity. Consumers **MUST** pair it with the provider peer ID and must not assume two providers using the same local name refer to the same resource.

## Filtered catalog

The catalog visible to a consumer is computed by the provider:

```text
registered resources
  intersect provider exposure policy
  intersect active visibility lease scope
  intersect current resource availability
  -> filtered catalog
```

The provider's local exposure policy always wins. A visibility lease can grant less than policy permits; it cannot force the provider to expose more.

Catalog responses **MUST** omit unauthorized resources and unauthorized fields rather than returning redacted placeholders that reveal inventory. A consumer **MUST** treat catalog results as a current view, not proof that an execution lease exists.

## Model-compatible discovery

For model resources, a local HiveMesh gateway may project the filtered catalog into `/v1/models`. Only models both visible to the consumer and mountable through the selected compatible API should appear.

The HiveMesh-native `/v1/hive/capabilities` view may expose the negotiated descriptor for local clients. That HTTP path is an implementation-facing projection; peer-to-peer exchange uses the versioned catalog protocol rather than remote access to another daemon's local API.

## Change events

A provider may publish catalog-change events after visibility, exposure, or availability changes. Consumers must tolerate missed events and re-query the catalog after reconnecting.
