# Status Codes

Reference for task and operation status codes used in EPMware REST API responses.

## Task Status Codes

| Code | Status | Description | Next State |
|------|--------|-------------|------------|
| **P** | Pending | Task queued | R, C |
| **R** | Running | Task executing | S, E, W, C |
| **S** | Success | Completed successfully | Terminal |
| **E** | Error | Failed with errors | Terminal |
| **W** | Warning | Completed with warnings | Terminal |
| **C** | Cancelled | User cancelled | Terminal |

## Operation Status

### Response Status Field

| Value | Meaning | Description |
|-------|---------|-------------|
| **S** | Success | Operation completed |
| **E** | Error | Operation failed |
| **W** | Warning | Completed with issues |
| **P** | Processing | Still processing |

## Import Status Codes

| Code | Status | Description |
|------|--------|-------------|
| **VAL** | Validating | Validating data |
| **IMP** | Importing | Processing records |
| **COM** | Complete | Import finished |
| **ERR** | Error | Import failed |

## Export Status Codes

| Code | Status | Description |
|------|--------|-------------|
| **QUE** | Queued | Export queued |
| **GEN** | Generating | Creating file |
| **CMP** | Compressing | Compressing output |
| **RDY** | Ready | File ready |

## Deployment Status Codes

| Code | Status | Description |
|------|--------|-------------|
| **INIT** | Initializing | Preparing deployment |
| **VALD** | Validating | Checking requirements |
| **DEPL** | Deploying | Applying changes |
| **VRFY** | Verifying | Post-deployment checks |
| **DONE** | Done | Deployment complete |
| **ROLL** | Rolling Back | Reverting changes |

## User Status Codes

| Code | Status | Description |
|------|--------|-------------|
| **A** | Active | User enabled |
| **D** | Disabled | User disabled |
| **L** | Locked | Account locked |
| **E** | Expired | Account expired |

---

[← Back to Reference](../) | [API Modules](../../modules/)
