# Worker Lifecycle

```text
START
  |
  v
Load identity
  |
  v
Detect hardware/runtime
  |
  v
Resolve ExecutionProfiles
  |
  v
Register / refresh availability
  |
  v
Receive candidate job
  |
  v
Validate requirements and budget
  |
  v
Accept
  |
  v
Retrieve input/model if needed
  |
  v
Execute
  |
  +--> stream results
  |
  v
Commit result
  |
  v
Verification
  |
  v
Settlement
```

Availability advertisements expire and must be refreshed. A worker that disappears should naturally fall out of selection without a central scheduler.
