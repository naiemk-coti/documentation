# Tutorial: private Adder on Ethereum Sepolia

This walkthrough is the **primitive-only** path: your host-chain contract calls **`PodLib`** helpers (the SDK surface for **MpcLib**-style primitives) and never deploys custom Solidity on COTI. If you are unsure whether that is enough for your product, read **[Tutorials: building Privacy on Demand (PoD) dApps](tutorials-privacy-on-demand.md)** first.

This guide shows how to build a minimal **Privacy on Demand** dApp that **adds two encrypted integers** on COTI and stores the **encrypted sum** on your EVM contract. It follows the same ideas as the SDK’s [MpcAdder.sol](https://github.com/cotitech-io/coti-pod-sdk/blob/main/contracts/examples/MpcAdder.sol) example, extended with **Sepolia routing presets** and **request correlation** suitable for a real UI.

For background on async flows and fees, see [Async private operations](async-private-operations.md), [How do PoA fees work?](how-poa-fees-work.md), and the SDK’s [Fees, gas, and oracle](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/contracts/04-fees-gas-and-oracle.md) page.

## Writing a PoD example

In this example we will do the following:

1. **Use one built-in executor operation** from `PodLib` (the sample below uses **`add256`**) so you do not write a custom COTI contract yet.
2. **Submit a payable request** that forwards `msg.value` and splits out `callbackFeeLocalWei` for the return leg (two-way Inbox message).
3. **Implement a success callback** that decodes `abi.encode(ctUint256)` and stores the ciphertext.
4. **Wire `onDefaultMpcError.selector`** so failed remote runs surface through the SDK’s default error path (and emit `ErrorRemoteCall` from `PodUser`).

After that works, you harden for production: per-user request ownership, explicit `pending / completed / failed` state, fee estimation via the Inbox, and tests for under-funded sends. The SDK’s [Examples with description](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/05c-examples-with-description.md) lists what the shipped `MpcAdder` omits on purpose.

## Prerequisites

- **Solidity toolchain** (Foundry or Hardhat) targeting **Ethereum Sepolia** (where the SDK’s `PodUserSepolia` Inbox is deployed).
- **Node.js 18+** for scripts and `fetch` used by encryption helpers.
- **Sepolia ETH** for deployment and for **`msg.value`** on each `add` call (plus gas).
- **User onboarding** so your client can obtain an **account AES key** for decryption (see the SDK’s [TypeScript integration](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/06-typescript-integration-ux-development.md) and [Onboarding / account AES key](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/06c-onboarding-account-account-aes-key.md) docs).

Always confirm **Inbox**, **COTI chain id**, and **MPC executor** against the version of `PodUserSepolia.sol` in your installed `@coti/pod-sdk` package; constants can change between releases.

## Step 1: Install the SDK

```bash
npm install "@coti/pod-sdk"
```

## Step 2: Create the `PrivateAdder` contract

Save as `PrivateAdder.sol`. The contract:

- Inherits **`PodLib`** and **`PodUserSepolia`** (Sepolia defaults for Inbox and COTI routing).
- Calls **`add64`** with the caller’s encrypted inputs and your callback selector.
- Resolves **`requestId`** in the callback the same way as the SDK’s [Getting started](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/04-getting-started.md) example.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.26;

import "@coti/pod-sdk/contracts/mpc/PodLib.sol";
import "@coti/pod-sdk/contracts/mpc/PodUserSepolia.sol";
import "@coti/pod-sdk/contracts/utils/mpc/MpcCore.sol";

/// @title PrivateAdder
/// @notice Adds two encrypted uint64 values via PoD on Sepolia (SDK preset addresses).
contract PrivateAdder is PodLib, PodUserSepolia {
    enum RequestStatus {
        None,
        Pending,
        Completed
    }

    mapping(bytes32 => ctUint256) public sumByRequest;
    mapping(bytes32 => RequestStatus) public statusByRequest;

    event AddRequested(bytes32 indexed requestId, address indexed caller);
    event AddCompleted(bytes32 indexed requestId);

    constructor() PodLibBase(msg.sender) {
        setInbox(INBOX_ADDRESS);
        configureCoti(MPC_EXECUTOR_ADDRESS, COTI_CHAIN_ID);
    }

    /// @param callbackFeeLocalWei Wei reserved for the callback leg; must be <= msg.value (see SDK fee docs).
    function add(
        itUint256 calldata a,
        itUint256 calldata b,
        uint256 callbackFeeLocalWei
    ) external payable returns (bytes32 requestId) {
        requestId = add256( // Add two encrypted 256 bit integers
            a,
            b,
            msg.sender, // Who can decrypt the result
            this.addCallback.selector,
            this.onDefaultMpcError.selector,
            msg.value,
            callbackFeeLocalWei
        );
        statusByRequest[requestId] = RequestStatus.Pending;
        emit AddRequested(requestId, msg.sender);
    }

    function addCallback(bytes memory data) external onlyInbox {
        bytes32 requestId = inbox.inboxSourceRequestId();
        if (requestId == bytes32(0)) {
            requestId = inbox.inboxRequestId();
        }

        ctUint256 sum = abi.decode(data, (ctUint256));
        sumByRequest[requestId] = sum;
        statusByRequest[requestId] = RequestStatus.Completed;
        emit AddCompleted(requestId);
    }
}
```

**Notes:**

- **`onDefaultMpcError`** is implemented on `PodLibBase` and forwards failures to **`ErrorRemoteCall`** on `PodUser`. Your UI can listen for that event to mark a request failed.
- **`addCallback`** must stay **`onlyInbox`** so random accounts cannot forge results.

## Step 3: Compile and deploy on Sepolia

Configure remappings so `@coti/pod-sdk` resolves (Hardhat `paths`, Foundry `remappings.txt`, etc.), then compile and deploy `PrivateAdder` to **Ethereum Sepolia**. Record the deployed address for scripts.

## Step 4: Budget `msg.value` and `callbackFeeLocalWei`

Two-way Inbox traffic needs enough native token to cover **outbound execution** and the **callback**. Use the deployed Inbox’s **`calculateTwoWayFeeRequiredInLocalToken`** (or your operator’s runbook) to pick `msg.value` and `callbackFeeLocalWei`. Undershooting typically leaves the request stuck or failing.

## Step 5: Encrypt the two summands (TypeScript)

`CotiPodCrypto.encrypt` calls the PoD encryption service. For Sepolia-style test usage, pass **`"testnet"`** as the network key (see `coti-pod-crypto.ts` in the SDK: `testnet` maps to the COTI testnet encryption endpoint).

```typescript
import { CotiPodCrypto, DataType } from "@coti/pod-sdk";

const plainA = "10";
const plainB = "20";

const encA = await CotiPodCrypto.encrypt(plainA, "testnet", DataType.Uint64);
const encB = await CotiPodCrypto.encrypt(plainB, "testnet", DataType.Uint64);

// encA / encB match Solidity itUint256: { ciphertext, signature }
// Normalize for ethers (example: bigint ciphertext if your ABI encoder expects it)
function toitUint256(enc: { ciphertext: string | bigint; signature: string }) {
  return {
    ciphertext: typeof enc.ciphertext === "bigint"
      ? enc.ciphertext
      : BigInt(enc.ciphertext),
    signature: enc.signature,
  };
}
```

## Step 6: Submit the `add` transaction

Use your wallet provider and contract ABI. The call must be **payable** with sufficient **`value`** and a valid **`callbackFeeLocalWei`**.

```typescript
// privateAdder: ethers.Contract instance, signer funded on Sepolia
// totalWei and callbackFeeWei from your fee estimation

const tx = await privateAdder.add(
  toitUint256(encA),
  toitUint256(encB),
  callbackFeeWei,
  { value: totalWei }
);

const receipt = await tx.wait();
const requestId = receipt?.logs
  .map((log) => {
    try {
      return privateAdder.interface.parseLog(log)?.args.requestId;
    } catch {
      return null;
    }
  })
  .find(Boolean);

// requestId is bytes32 — keep it for reads and decryption
```

Private addition is **asynchronous**: the sum appears only after the Inbox invokes **`addCallback`** in a later transaction. Poll **`statusByRequest(requestId)`** or wait for **`AddCompleted`**.

## Step 7: Read the encrypted sum and decrypt locally

After status is **Completed**, read **`sumByRequest(requestId)`**. The value is **`ctUint256`** (ciphertext), not plaintext.

```typescript
import { CotiPodCrypto, DataType } from "@coti/pod-sdk";

// accountAesKey: hex string from your app’s COTI onboarding flow (never log it)

const ct = await privateAdder.sumByRequest(requestId);
const ctHex =
  typeof ct === "bigint"
    ? "0x" + ct.toString(16)
    : String(ct);

const decryptedString = CotiPodCrypto.decrypt(
  ctHex,
  accountAesKey,
  DataType.Uint64
);

console.log("sum (plaintext string):", decryptedString);
// Expect "30" for plainA=10 and plainB=20
```

`CotiPodCrypto.decrypt` delegates to `@coti-io/coti-sdk-typescript` and expects a **scalar ciphertext** as a **hex string** for `Uint64`, plus the user’s **AES key** (see SDK source [coti-pod-crypto.ts](https://github.com/cotitech-io/coti-pod-sdk/blob/main/src/coti-pod-crypto.ts)).

## Step 8: Sanity checks and next steps

- **Callback decode** must stay **`(ctUint256)`** — changing the executor op or COTI-side behavior without updating the decode tuple will corrupt storage reads.
- **Type width** must stay **uint64** end to end: `DataType.Uint64`, `itUint256`, `ctUint256`.
- **Production**: add tests for non-Inbox callers on `addCallback`, under-funded `msg.value`, and decrypt failures; follow the [first production checklist](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/04-getting-started.md) in Getting started.

## Reference links

- [MpcAdder.sol (minimal repo example)](https://github.com/cotitech-io/coti-pod-sdk/blob/main/contracts/examples/MpcAdder.sol)
- [Examples with description](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/05c-examples-with-description.md)
- [Getting started (PodUserSepolia pattern)](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/04-getting-started.md)
- [Async execution](https://github.com/cotitech-io/coti-pod-sdk/blob/main/docs/05a-async-execution.md)

<div style="width:100%; box-sizing:border-box; margin:2rem 0 0 0; padding:1.35rem 1rem; border:2px solid #334155; border-radius:10px; background:#f8fafc; text-align:center;">

<p style="margin:0.4rem 0; font-size:1.25rem; font-weight:700;"><a href="tutorial-private-adder-sepolia.md" style="color:#0f172a; text-decoration:none;">Tutorial: private Adder on Sepolia</a></p>

<p style="margin:0.4rem 0; font-size:1.25rem; font-weight:700;"><a href="tutorial-custom-logic.md" style="color:#0f172a; text-decoration:none;">Tutorial: custom privacy logic with PoD</a></p>

</div>
