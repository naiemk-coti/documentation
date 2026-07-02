# TypeScript PoD SDK (`CotiPodCrypto`, `PodContract`, `PodRequest`)

The npm package **`@coti/pod-sdk`** is the canonical TypeScript client for PoD dApps. It covers encryption/decryption, fee-aware contract calls, and cross-chain request tracking through [`coti-pod-crypto.ts`](https://github.com/coti-io/coti-sdk-pod/blob/main/src/coti-pod-crypto.ts), [`pod-method-call.ts`](https://github.com/coti-io/coti-sdk-pod/blob/main/src/pod-method-call.ts), and [`pod-request.ts`](https://github.com/coti-io/coti-sdk-pod/blob/main/src/pod-request.ts).

```bash
npm install @coti/pod-sdk ethers
```

| Concern | Class / helpers |
| --- | --- |
| Encrypt `it*` inputs and decrypt `ct*` results | `CotiPodCrypto` |
| Submit PoD method calls (fees, encryption, event parsing) | `PodContract` |
| Track async request lifecycle across chains | `PodRequest` |

Account key recovery is **not** in this package — use [`@coti-io/coti-sdk-typescript`](coti-typescript-sdk-for-pod.md) (see [Account onboarding](account-onboarding-aes-key.md)).

## Shared config (`PodSdkConfig`)

`PodSdkConfig` is plain JSON shared by `PodContract` and `PodRequest`:

```typescript
import {
  CotiPodCrypto,
  DataType,
  PodContract,
  PodRequest,
  SEPOLIA_DEFAULT_INBOX_ADDRESS,
  COTI_TESTNET_DEFAULT_INBOX_ADDRESS,
  type PodSdkConfig,
} from "@coti/pod-sdk";

const config: PodSdkConfig = {
  encryptionNetwork: "testnet", // or "mainnet" or a full service URL
  chains: [
    {
      chainId: 11155111,
      inboxAddress: SEPOLIA_DEFAULT_INBOX_ADDRESS,
      rpcUrl: process.env.SEPOLIA_RPC_URL!,
    },
    {
      chainId: 7082400,
      inboxAddress: COTI_TESTNET_DEFAULT_INBOX_ADDRESS,
      rpcUrl: process.env.COTI_TESTNET_RPC_URL!,
    },
  ],
};
```

- **`chainId`** — EIP-155 chain id
- **`inboxAddress`** — PoD Inbox on that chain (CREATE3 address from [`PodNetworkConstants`](https://github.com/coti-io/coti-contracts/blob/main/contracts/pod/PodNetworkConstants.sol))
- **`rpcUrl`** — JSON-RPC for fee estimation and `PodRequest` polling
- **`encryptionNetwork`** — `"testnet"` / `"mainnet"` keyword or full URL for `CotiPodCrypto.encrypt`

`PodContract` resolves inbox address from `config.chains` or falls back to `DEFAULT_INBOX_ADDRESS_BY_CHAIN_ID`.

## Encrypt and decrypt (`CotiPodCrypto`)

`CotiPodCrypto.encrypt` POSTs to the PoD encryption service (`"testnet"`, `"mainnet"`, or a full URL). `CotiPodCrypto.decrypt` uses the account AES key and `@coti-io/coti-sdk-typescript` under the hood.

```typescript
import { CotiPodCrypto, DataType } from "@coti/pod-sdk";

const enc = await CotiPodCrypto.encrypt("42", "testnet", DataType.itUint256);
const plain64 = CotiPodCrypto.decrypt("0x...", accountAesKey, DataType.Uint64);
const plain256 = CotiPodCrypto.decrypt(
  { ciphertextHigh, ciphertextLow },
  accountAesKey,
  DataType.Uint256
);
```

Off-chain decrypt shapes for `ct*` / `it*` types: [Reference: data types](reference-data-types.md).

## Method calls (`PodContract`)

`PodContract` wraps `ethers.Contract` with:

- `estimateFee` — Inbox `calculateTwoWayFeeRequiredInLocalToken`
- `encryptAndCallMethod` — encrypt `it*` plaintext args and send
- `callMethod` — submit pre-built `it*` JSON ciphertext
- `extractRequestIds` — read `requestId` from compact Inbox `MessageSent` events

```typescript
import { PodContract, DataType, type PodFeeEstimationConfig, type PodMethodArgument } from "@coti/pod-sdk";
import { ethers } from "ethers";

const pod = new PodContract(contractAddress, abi, signer, { config });

const args: PodMethodArgument[] = [
  { type: DataType.itUint256, value: "10", isCallBackFee: false },
  { type: DataType.itUint256, value: "20", isCallBackFee: false },
  { type: DataType.Uint256, value: "0", isCallBackFee: true },
];

const feeCfg: PodFeeEstimationConfig = {
  forwardGasLimit: 400_000n,
  gasPrice: (await signer.provider!.getFeeData()).gasPrice ?? 0n,
  callBackGasLimit: 250_000n,
  callBackDataSize: 512n,
};

const tx = await pod.encryptAndCallMethod("add", args, feeCfg);
const receipt = await (tx as ethers.ContractTransactionResponse).wait();
const requestIds = receipt?.hash ? await pod.extractRequestIds(receipt.hash) : [];
```

**Fee config rules:**

- `forwardGasLimit` and `gasPrice` are required
- `callBackGasLimit` and `callBackDataSize` must be set together or both omitted
- Exactly one argument may have `isCallBackFee: true` — `PodContract` overwrites its value with the computed callback fee

## Request tracking (`PodRequest`)

Poll `trackRequest(chainId, requestId)` for a fresh snapshot until the UI reaches a terminal state:

```typescript
const tracker = new PodRequest(config);
const status = await tracker.trackRequest(11155111n, requestId);
```

| Field | Meaning |
| --- | --- |
| `minedOnTarget` | Target inbox ingested the request |
| `isTwoWay` | Callback expected |
| `execution` | Encode/subcall failure on target (`errorCode`, `errorMessage`) |
| `response` | Recursive tracking of the callback leg (two-way only) |

**Completed two-way round-trip:**

```
status.isTwoWay === true
status.minedOnTarget === true
status.response !== null
status.response.minedOnTarget === true
```

**Error codes:** `ERROR_CODE_EXECUTION_FAILED` (1n), `ERROR_CODE_ENCODE_FAILED` (2n). Use `decodeInboxErrorMessage(rawHex)` to decode revert data. UI state mapping: [Async private operations](async-private-operations.md).

## End-to-end example

```typescript
import {
  CotiPodCrypto,
  DataType,
  PodContract,
  PodRequest,
  SEPOLIA_DEFAULT_INBOX_ADDRESS,
  COTI_TESTNET_DEFAULT_INBOX_ADDRESS,
  type PodSdkConfig,
} from "@coti/pod-sdk";
import { ethers } from "ethers";

const config: PodSdkConfig = {
  encryptionNetwork: "testnet",
  chains: [
    { chainId: 11155111, inboxAddress: SEPOLIA_DEFAULT_INBOX_ADDRESS, rpcUrl: SEPOLIA_RPC },
    { chainId: 7082400, inboxAddress: COTI_TESTNET_DEFAULT_INBOX_ADDRESS, rpcUrl: COTI_RPC },
  ],
};

const tracker = new PodRequest(config);

async function compareAndWait(
  signer: ethers.Signer,
  aesKey: string,
  aPlain: string,
  bPlain: string
): Promise<boolean> {
  const pod = new PodContract(COMPARE_APP_ADDRESS, COMPARE_APP_ABI, signer, { config });
  const feeData = await signer.provider!.getFeeData();

  const tx = await pod.encryptAndCallMethod(
    "compare",
    [
      { type: DataType.itUint64, value: aPlain, isCallBackFee: false },
      { type: DataType.itUint64, value: bPlain, isCallBackFee: false },
      { type: DataType.Uint256, value: "0", isCallBackFee: true },
    ],
    {
      forwardGasLimit: 1_500_000n,
      callBackGasLimit: 400_000n,
      gasPrice: feeData.gasPrice!,
    }
  );
  const receipt = await tx.wait();
  const [requestId] = await pod.extractRequestIds(receipt!.hash);
  const { chainId } = await signer.provider!.getNetwork();

  while (true) {
    const s = await tracker.trackRequest(chainId, requestId);
    if (s.execution) throw new Error(s.execution.errorMessage);
    if (s.isTwoWay && s.response?.minedOnTarget) break;
    if (!s.isTwoWay && s.minedOnTarget) break;
    await new Promise((r) => setTimeout(r, 3_000));
  }

  const ct: string = await pod.contract.resultByRequest(requestId);
  return CotiPodCrypto.decrypt(ct, aesKey, DataType.Bool) === "true";
}
```

## Common patterns

- **Long-lived `PodRequest`, short-lived `PodContract`** — one tracker per app; new `PodContract` per contract/signer binding
- **Persist `requestId`** alongside user-facing entities; transport status and on-chain `ct*` storage are separate concerns
- **Polling cadence** — 2–5 seconds; cap total wait time and offer manual re-check

## See also

- [Account onboarding (AES key)](account-onboarding-aes-key.md)
- [COTI TypeScript SDK for PoD](coti-typescript-sdk-for-pod.md)
- [Tutorial: private Adder on Sepolia](tutorial-private-adder-sepolia.md)
- [How do PoA fees work?](how-poa-fees-work.md)
