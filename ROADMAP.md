# NIAHCIA Compute Roadmap

This roadmap tracks the worker/runtime side of NIAHCIA decentralized compute. Consensus, canonical protocol formats, and interoperability rules live in the node/protocol repositories and must remain synchronized with worker behavior.

## Phase 1 — Worker identity and capability discovery

- ComputeWorker identity
- Operator binding
- hardware/accelerator detection
- runtime detection
- ExecutionProfile matching
- signed, expiring availability advertisements
- model HOT/WARM/COLD availability state
- capability and resource-limit reporting
- no consensus authority from worker registration/advertising

## Phase 2 — Initial vLLM execution

- pinned vLLM adapter/profile
- model retrieval/loading
- job input validation
- bounded job/session context
- deterministic generation settings where practical
- token/result streaming where policy permits
- canonical ResultCommitment generation
- execution telemetry without secret/private payload leakage

The initial pinned Qwen3-class model and NVIDIA Ampere target are test profiles, not permanent network requirements.

## Phase 3 — Authority isolation and portable Agent execution

- consume exact Agent/AgentVersion/AgentManifest context
- resolve current KeyAuthority epoch
- reject master/private signing-key handoff to the model runtime
- bounded delegated/session authority
- capability, destination, resource, and spending limits
- authorized memory/input scope only
- session expiry/revocation/cleanup
- AgentHostMigrationV1 integration
- AgentCheckpointV1 resume support
- source-host-independent recovery from durable committed state
- stale authority/checkpoint rejection
- duplicate-host/migration-race handling

## Phase 4 — Decentralized scheduling and failover

- peer/worker discovery
- candidate requirement filtering
- local/verifiable selection inputs
- decentralized selection strategy
- HOT/WARM/COLD preference
- failover/rescheduling
- avoid one permanent central scheduler
- operator/failure-domain diversity inputs where applicable
- do not make scheduling success a consensus vote

Exact production scheduling/scoring remains open and must be threat-modeled for gaming and centralization.

## Phase 5 — Policy-driven verification

- VerificationPolicy dispatch
- ResultCommitment handling
- ExecutionReceiptV1 production
- redundant execution where selected by policy
- audit participation
- optimistic-verification hooks
- deterministic/reproducible profiles where applicable
- operator diversity constraints where applicable
- future proof/TEE/private-compute adapters

There is no universal fixed 2-of-3 verification rule.

## Phase 6 — Durable workflows and side-effect safety

- SideEffectIntentV1 propagation through tool/economic calls
- stable effect identity across retry/migration
- UNKNOWN outcome preservation and reconciliation
- AgentWorkflowV1 durable step state
- checkpoint integration for pending/completed effects
- idempotency-key propagation where target systems support it
- signer/policy enforcement for payments and privileged effects
- aggregate/per-step budget containment
- compensation actions as new auditable effects, not rollback
- manual-review stop for unsafe ambiguity

The compute host must not invent a fresh economic action merely because execution restarted.

## Phase 7 — Runtime and hardware expansion

- additional vLLM profiles
- multi-GPU
- AMD and other accelerators
- llama.cpp
- SGLang
- additional model families
- runtime-specific ExecutionProfiles
- optional hardware/private-compute attestations
- future proof systems

No one accelerator vendor, model, runtime, or TEE should become a universal protocol authority.

## Phase 8 — Hardening and interoperability

- protocol-owned canonical vectors
- cross-implementation worker/job/result fixtures
- malicious-job and malicious-host tests
- prompt-injection-to-signer tests
- capability leakage tests
- stale authority/checkpoint tests
- duplicate execution and replay tests
- crash/restart/migration tests
- privacy/logging audits
- resource exhaustion/admission controls
- devnet soak testing

## Explicit non-goals for the compute worker

`niahcia-compute` is not intended to:

- perform CPU PoW consensus as part of AI execution;
- vote on chain finality/fork choice;
- custody an Agent's unrestricted master private key;
- become a universal Agent recovery authority;
- make storage possession equivalent to decryption authority;
- assume arbitrary external APIs provide exactly-once execution;
- permanently bind an Agent to one physical worker.
