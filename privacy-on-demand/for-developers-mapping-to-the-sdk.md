# For developers: mapping concepts to the SDK

This page is the **developer index**: concept → source file, plus a fast path into hands-on guides. For architecture and the three-package stack, start with [Architecture and main components](architecture-and-components.md). For the full reading list, use the [section index](README.md).

**Fast path:** [Tutorials overview](tutorials-privacy-on-demand.md) → [private Adder tutorial](tutorial-private-adder-sepolia.md) → [TypeScript PoD SDK](typescript-pod-sdk.md).

## Component → source file map

| Concept | Source / reference |
| --- | --- |
| **Inbox (interface)** | [IInbox.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/IInbox.sol) in `@coti-io/coti-contracts` |
| **Inbox (implementation)** | [Inbox.sol](https://github.com/coti-io/coti-pod-inbox-contracts/blob/main/contracts/Inbox.sol) in `@coti-io/coti-pod-inbox-contracts` |
| **Callback guard** | [InboxUser.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/InboxUser.sol) (`onlyInbox`) |
| **PodLib** | [PodLib.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/mpc/PodLib.sol), `PodLib64` / `128` / `256` — see [Reference: PodLib primitives](reference-podlib-and-primitives.md) |
| **PodUser / presets** | [PodUserSepolia.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/mpc/PodUserSepolia.sol), [PodUserFuji.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/mpc/PodUserFuji.sol) |
| **Network constants** | [PodNetworkConstants.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/PodNetworkConstants.sol) |
| **Types (`it*`, `ct*`, `gt*`)** | [MpcCore.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/utils/mpc/MpcCore.sol) — see [Reference: data types](reference-data-types.md) |
| **Custom COTI calls** | [MpcAbiCodec.sol](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/mpccodec/MpcAbiCodec.sol) — see [Tutorial: custom privacy logic](tutorial-custom-logic.md) |
| **Client crypto** | [coti-pod-crypto.ts](https://github.com/coti-io/coti-sdk-pod/blob/main/src/coti-pod-crypto.ts) — see [TypeScript PoD SDK](typescript-pod-sdk.md) |
| **Request tracking** | [pod-request.ts](https://github.com/coti-io/coti-sdk-pod/blob/main/src/pod-request.ts) — `PodRequest.trackRequest` |
| **Fee oracle** | [InboxFeeManager.sol](https://github.com/coti-io/coti-pod-inbox-contracts/blob/main/contracts/fee/InboxFeeManager.sol) |

## Before you ship

Use the [Contract patterns checklist](contract-patterns-checklist.md) for production contract shape, fees, security, async UX, and tests. Pair it with [Async private operations](async-private-operations.md) for product-facing pending states.

## Relationship to native COTI “build” documentation

If you build **directly on COTI V2**, start from **[Build on COTI](../build-on-coti/README.md)**. PoD adds the **Inbox-mediated cross-chain** angle.

## Package install

```bash
npm install "@coti-io/coti-contracts"
npm install "@coti/pod-sdk"
```

Solidity preset pattern: [Reference: PodLib primitives](reference-podlib-and-primitives.md).
