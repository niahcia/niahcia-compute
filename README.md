# NIAHCIA Compute

Decentralized AI/accelerator compute-worker software for the NIAHCIA network.

`niahcia-compute` is the software a compute provider runs to offer GPU/accelerator execution to NIAHCIA. It discovers local hardware and runtimes, advertises eligible execution capabilities, accepts compatible jobs, executes them under bounded authority, streams outputs where permitted, and produces protocol-verifiable result/evidence commitments.

It is intentionally separate from CPU proof-of-work mining. CPU PoW secures the canonical NIAHCIA chain; compute workers perform AI/accelerator jobs and participate in the separate compute economy.

## Security boundary

A compute worker is a **replaceable execution host**, not the owner of an Agent.

Running an Agent or Job MUST NOT automatically grant the worker:

- the Agent's long-term/master signing private key;
- unrestricted Agent treasury authority;
- recovery or succession authority;
- permission to change AgentVersion, AgentManifest, or KeyAuthority state;
- unrestricted access to all historical private memory;
- chain-consensus or finality authority.

A worker should receive only the authority and data needed for the accepted execution: exact Agent/Job/version context, an ExecutionProfile, bounded session/capability authority, authorized memory/input scope, and applicable budget/policy limits.

Long-term signing should remain isolated from the model/runtime. Model output and natural-language intent are untrusted inputs to privileged signing/authorization.

## Current protocol direction

The worker side is expected to interoperate with protocol objects defined in `niahcia-protocol`, including as they mature:

- `ComputeWorker` and `Operator` identity;
- `Model` and `ExecutionProfile`;
- `Job` and `VerificationPolicy`;
- `ResultCommitment` and `ExecutionReceiptV1`;
- `Agent`, `AgentVersion`, and `AgentManifestV1`;
- `KeyAuthorityV1` and bounded delegated/session authority;
- `AgentHostMigrationV1`;
- `AgentCheckpointV1`;
- `SideEffectIntentV1` and `AgentWorkflowV1` where execution participates in durable Agent workflows.

Candidate protocol documents are not automatically production behavior. Canonical encodings, IDs, vectors, and activation rules must be locked before interoperability-sensitive implementation is treated as stable.

## Worker responsibilities

Initial/current target responsibilities are:

- ComputeWorker identity and Operator binding;
- hardware/accelerator/runtime discovery;
- vLLM integration as the initial runtime direction;
- ExecutionProfile matching;
- model availability and HOT/WARM/COLD state reporting;
- decentralized, expiring worker advertisements;
- job requirement/capability/budget validation;
- AI job acceptance and execution;
- authorized input/memory retrieval;
- token/result streaming where policy permits;
- ResultCommitment and execution-evidence production;
- policy-driven verification participation;
- service/storage-assisted model and durable-state retrieval;
- bounded session cleanup/revocation after execution;
- telemetry that does not expose secret key material or private payloads.

## Portable Agent execution

NIAHCIA is being designed so Agent identity and durable state are independent of one physical GPU host.

Conceptually:

```text
NIAHCIA Agent
     |
     +-- AgentManifest / AgentVersion
     +-- current KeyAuthority epoch
     +-- durable checkpoint + encrypted memory
     +-- capabilities / policies
     |
     v
niahcia-compute host A
     |
     X failure / migration
     |
     v
niahcia-compute host B
     |
     +-- resolve exact Agent/version/context
     +-- validate current authority/policy
     +-- receive bounded session authority
     +-- unlock only authorized memory/input scope
     +-- execute
     +-- produce commitments/receipts
     +-- expire/revoke session
```

Host A must not become a portability veto when committed Agent state survives elsewhere.

The worker must assume duplicate execution can occur during failures, partitions, or migration races. Safety comes from protocol job/session/effect identities, current state versions, bounded capabilities and budgets, signer policy, reconciliation, and settlement rules—not from assuming only one process is running.

## Memory and confidentiality

Durable private Agent memory should be stored using envelope-encryption semantics: bulk data is encrypted with data-encryption keys (DEKs), while NIAHCIA cryptographic authority governs access to those keys.

A compute host receives only authorized key/data scope for an execution session. It should never require the Agent's long-term signing key to decrypt bulk memory.

**Important:** encryption at rest and secure migration are not private inference. An ordinary worker may observe plaintext intentionally delivered to its runtime. Stronger confidentiality requires a separately versioned private-compute mechanism. NIAHCIA does not assume one TEE vendor as universal protocol authority.

## Verification

NIAHCIA does **not** assume one universal fixed 2-of-3 redundant execution scheme.

Verification is policy-driven through `VerificationPolicy`. Different workloads may eventually use redundant execution, audits, optimistic verification, deterministic/reproducible profiles, TEEs, proof systems, or other explicitly versioned mechanisms.

A worker participates according to the Job's applicable verification policy; it does not decide chain consensus.

## Initial runtime profile

The original Prototype-0 direction remains useful as an implementation test profile, not a permanent network-wide requirement:

- vLLM;
- a pinned Qwen3-class ~8B model for reproducible early testing;
- NVIDIA Ampere-class hardware as an initial known target;
- deterministic generation settings where practical;
- canonical token/result commitments.

The architecture is intended to expand to additional models, GPUs/accelerators, runtimes, and verification/private-compute profiles without redesigning PoW consensus.

## Relationship to other repositories

```text
niahcia/niahcia
  blockchain node, consensus/state, execution-engine integration,
  protocol activation and settlement

niahcia/niahcia-protocol
  implementation-independent protocol objects, canonical formats,
  security boundaries and interoperability vectors

niahcia/niahcia-compute
  GPU/accelerator worker runtime that executes eligible compute jobs
```

Protocol-visible compute behavior must remain synchronized with `niahcia-protocol` and the node implementation.

## Planned layout

```text
worker/
protocol/
runtimes/
  vllm/
scheduler/
verification/
storage/
authority/
checkpoint/
telemetry/
docs/
.github/workflows/
```

## Status

**Pre-alpha.** Architecture and protocol candidates are evolving. Do not treat candidate object layouts or unallocated canonical fields as stable wire compatibility until explicitly locked with protocol-owned vectors.
