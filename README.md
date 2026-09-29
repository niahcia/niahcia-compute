# NIAHCIA Compute

Decentralized AI compute-worker software for the NIAHCIA network.

This client executes AI workloads, advertises capabilities, streams results, and participates in verification. It is economically and architecturally separate from CPU mining.

## Initial target

- ComputeWorker identity
- Operator binding
- hardware/runtime discovery
- vLLM integration
- ExecutionProfile support
- model hot/warm state reporting
- decentralized worker advertisements
- AI job acceptance
- streaming responses
- ResultCommitments
- redundant verification support
- service-node model retrieval

## Prototype 0 profile

- vLLM
- pinned Qwen3-class ~8B model
- NVIDIA Ampere-class hardware
- deterministic generation settings where practical
- canonical token-sequence commitments

## Planned layout

```text
worker/
protocol/
runtimes/
  vllm/
scheduler/
verification/
storage/
telemetry/
docs/
.github/workflows/
```

## Status

Pre-alpha.
