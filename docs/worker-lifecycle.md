# Worker Lifecycle

A NIAHCIA compute worker is a replaceable execution provider. It does not become an Agent owner or chain-consensus authority by executing work.

## Baseline lifecycle

```text
START
  |
  v
Load ComputeWorker identity + Operator binding
  |
  v
Detect hardware / accelerators / runtimes
  |
  v
Resolve supported ExecutionProfiles + model state
  |
  v
Publish / refresh signed expiring availability
  |
  v
Receive candidate Job
  |
  v
Resolve exact Job / Agent / AgentVersion / AgentManifest
  |
  v
Validate current KeyAuthority epoch
  |
  v
Validate requirements, VerificationPolicy,
capabilities, budget and resource limits
  |
  +---- reject if incompatible / unauthorized
  |
  v
Accept Job
  |
  v
Establish bounded session authority
  |
  v
Retrieve exact model / inputs / authorized memory scope
  |
  v
Execute under ExecutionProfile
  |
  +----> stream permitted results
  |
  v
Produce ResultCommitment / execution evidence
  |
  v
Participate in applicable VerificationPolicy
  |
  v
Settlement / receipt handling
  |
  v
Commit/checkpoint durable Agent state where applicable
  |
  v
Expire / revoke / erase session authority and temporary secrets
  |
  v
Return to availability
```

## Availability

Availability advertisements expire and must be refreshed. A worker that disappears should naturally fall out of candidate selection without requiring one permanent central scheduler.

Advertisements are scheduling/service information. They do not grant PoW, finality, governance, recovery, or storage authority.

## Job acceptance

Before execution, the worker should validate the exact protocol context required by the Job rather than trusting descriptive metadata from a peer or frontend.

Validation eventually includes:

- worker/Operator identity requirements;
- model and ExecutionProfile compatibility;
- Agent/AgentVersion/AgentManifest identity where applicable;
- current KeyAuthority epoch;
- capability/session scope;
- VerificationPolicy;
- memory/input authorization;
- budget/resource limits;
- job/session nonce and validity window.

## Authority boundary

The worker should never need an Agent's long-term signing private key simply to execute a Job.

The execution/model process receives the minimum session authority necessary for the accepted work. Long-term signing remains isolated behind an authorization/signer boundary.

Natural-language model output is not privileged authorization.

## Memory retrieval

Private durable state may be encrypted at rest. The worker should retrieve only the committed state and decryption material authorized for the current execution scope.

Ordinary execution may expose authorized plaintext to the worker/runtime. This lifecycle does not claim confidential/private inference unless a stronger separately versioned private-compute profile is active.

## Crash and migration

A dead worker must not become an Agent portability veto if durable committed state survives elsewhere.

A replacement worker should resume from an accepted durable checkpoint rather than treating old host-local state as authoritative.

The replacement validates current authority/checkpoint state and receives a new bounded session. The old worker's session should expire/revoke according to policy.

## Duplicate execution

Old and new workers can overlap because of network partitions, delayed failure detection, or malicious behavior. The protocol must assume duplicate execution is possible.

Workers therefore preserve Job/session/effect identities across retries. Economic/tool effects use stable `SideEffectIntentV1` identities where applicable; restarting on another host does not create a new spending allowance or justify a duplicate action.

## Unknown external effects

If a worker submits an external effect and crashes before receiving confirmation, the resumed workflow treats the result as `UNKNOWN` until reconciled. It must not infer failure from a missing acknowledgement and blindly issue a new effect.

## Verification

Verification is selected by `VerificationPolicy`, not a universal fixed redundant-execution rule. A worker may execute, verify, audit, or provide evidence according to the applicable policy and ExecutionProfile.

Compute verification never grants chain fork-choice authority.

## Session cleanup

At completion, rejection, expiry, or migration, the worker should remove/revoke temporary session material as appropriate, including scoped keys/capabilities and sensitive transient data.

Secret material must not be written to ordinary logs or telemetry.
