# Async private operations

Privacy on Demand private calls are **asynchronous**. That single sentence drives most product and operations decisions.

## What “async” means for users

On a normal smart contract call, many teams think in terms of: **submit transaction → see result in the same flow**.

With PoD, the meaningful private result usually arrives in a **second step**:

1. **Request transaction** — your dApp contract submits work **through the Inbox** and emits a **request identifier** (in the compact `MessageSent` event).
2. **Wait** — COTI performs private computation.
3. **Callback transaction** — the Inbox invokes your contract’s **callback** with **encrypted outputs** (`ct*`).

So the user’s mental model should be closer to **“I submitted a job”** than **“I got the answer immediately.”**

## Why the platform works this way

Private execution happens **outside** your chain’s normal synchronous EVM frame. The **Inbox** pattern carries a message out and brings a response back through a controlled channel.

See [Contract patterns checklist](contract-patterns-checklist.md) for callback lifecycle requirements.

## Tracking requests in TypeScript

Use [`PodRequest.trackRequest`](typescript-pod-sdk.md) to poll inbox state until a terminal condition:

| UI state | Typical condition |
| --- | --- |
| **Pending** | Request emitted on source chain, not yet mined on target |
| **Executing** | `minedOnTarget === true` on outbound leg; waiting for callback |
| **Completed** | Two-way: `response.minedOnTarget === true`. One-way: outbound `minedOnTarget` only |
| **Failed** | `execution` or `response.execution` populated with error code/message |

Extract `requestId` from the submit transaction with [`PodContract.extractRequestIds`](typescript-pod-sdk.md).

## What product and support teams should plan for

| Topic | Recommendation |
| --- | --- |
| **UI states** | Show **Pending / Completed / Failed** per request ID |
| **Indexing** | Index compact Inbox `MessageSent` events (`requestId` in indexed topics) |
| **Errors** | Surface `execution.errorMessage` from `PodRequest`; use `decodeInboxErrorMessage` for raw revert data |
| **Support** | “Stuck pending” may be fee, routing, or miner issues — not always user error |

## Relationship to decryption

Even after **completion**, plaintext is **not** public on-chain. Authorized clients decrypt **`ct*` outputs** locally. Plan for [account onboarding](account-onboarding-aes-key.md), device loss, and clear disclosure of who can decrypt what.

## Next steps

- [How a private request travels end to end](how-a-private-request-travels-end-to-end.md)
- [TypeScript PoD SDK](typescript-pod-sdk.md) — `PodRequest` reference
- [For developers: mapping concepts to the SDK](for-developers-mapping-to-the-sdk.md)
