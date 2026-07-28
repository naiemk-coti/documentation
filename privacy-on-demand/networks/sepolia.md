# Ethereum Sepolia

Ethereum Sepolia is a supported **host chain** for Privacy on Demand. Your dApp contracts and the local **Inbox** live here; private computation still runs on [COTI Testnet](coti-testnet.md).

> **Note:** Addresses below match `pod-ecosystem-integration/deployConfig.json` (Inbox salt `pod.inbox.v2.2`). They may change after contract redeploys. Confirm against that file or your SDK release before production use.

## Network details

| Parameter | Value |
| --- | --- |
| Network name | Ethereum Sepolia |
| Chain ID | `11155111` |
| Currency | ETH |
| RPC URL | `https://rpc.sepolia.org` (or your preferred Sepolia provider) |
| Block explorer | [https://sepolia.etherscan.io](https://sepolia.etherscan.io) |

Private execution for Sepolia dApps targets **COTI Testnet** (`7082400`). See [COTI Testnet](coti-testnet.md) for the MPC executor and COTI-side Inbox.

## PoD contracts

| Contract | Address | Description |
| --- | --- | --- |
| Inbox | [`0x3b8B70819f27e0438cBcE7f31894f799da52648F`](https://sepolia.etherscan.io/address/0x3b8B70819f27e0438cBcE7f31894f799da52648F) | Cross-chain message router (CREATE3; same address on every PoD chain) |
| Price oracle | [`0x71f0deac8adb89b7f1b09b38e2531e06bcca0b03`](https://sepolia.etherscan.io/address/0x71f0deac8adb89b7f1b09b38e2531e06bcca0b03) | Local/remote token prices used by Inbox fee conversion |
| MpcAdder (example) | [`0xacc74e07db35fb740509fc82228e631f4f08441c`](https://sepolia.etherscan.io/address/0xacc74e07db35fb740509fc82228e631f4f08441c) | Reference primitive-only adder dApp on Sepolia |

## PoD cross-chain Privacy Portal (Sepolia)

This is the **PoD host-chain Privacy Portal** (factory + pTokens on Sepolia, private compute via COTI Inbox). It is **not** the [native COTI Privacy Portal / PrivateERC20](../../coti-privacy-portal/README.md) that runs entirely on COTI.

| Contract | Address |
| --- | --- |
| Privacy Portal factory | [`0x6bffca5073e83cde03438cca60feed6e49582044`](https://sepolia.etherscan.io/address/0x6bffca5073e83cde03438cca60feed6e49582044) |
| Portal implementation | [`0xd22f4182d57637dd6f125ae8464a82f5647023cd`](https://sepolia.etherscan.io/address/0xd22f4182d57637dd6f125ae8464a82f5647023cd) |
| Pod token implementation | [`0xe8e2fdd23ea2d5f9bb4632d11f7267602a059e5d`](https://sepolia.etherscan.io/address/0xe8e2fdd23ea2d5f9bb4632d11f7267602a059e5d) |

### Privacy Portal tokens

| Token | Underlying | Portal | pToken |
| --- | --- | --- | --- |
| pMTT | `0xd3f5c63f4D87D2235b295FbA83351d31d0eD1BeE` | `0x3af63ceb47E47CD9742F0Cf9C715c5A1c778d548` | `0xbf5971D4791EaC5c727eE23a613E5f755ED7dE37` |
| pUSDC | `0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238` | `0xc32F55450db6fD66d0Bf5c875e791803271b6862` | `0x433e0AFDe6b8a0102a1C93aeEd61aE27794ae484` |
| pWETH | `0x7b79995e5f793A07Bc00c21412e50Ecae098E7f9` | `0xc666c0eFA5C5DDb953dF3881bC3C54C770bc59A4` | `0x00A69024717Ae8D6EA128972a52F969951474279` |

## How this network fits PoD

| Piece | Role on Sepolia |
| --- | --- |
| Inbox | Host-side courier: accepts encrypted requests from your dApp and delivers COTI callbacks |
| Your dApp | Configures Inbox + COTI chain ID `7082400` + [MPC executor on COTI Testnet](coti-testnet.md#pod-contracts) |
| Price oracle | Converts Sepolia fee budgets against COTI-side costs |

Flow at a glance: **user / dApp on Sepolia → Sepolia Inbox → (relayer) → COTI Inbox → MPC executor → callback to Sepolia**.

Hands-on next step: [Tutorial: private Adder on Sepolia](../tutorial-private-adder-sepolia.md).

## SDK constants

In `@coti-io/pod-sdk` / `@coti-io/coti-contracts`:

| Constant | Value |
| --- | --- |
| Chain ID | `11155111` |
| Inbox | `0x3b8B70819f27e0438cBcE7f31894f799da52648F` (`SEPOLIA_DEFAULT_INBOX_ADDRESS`) |
| Solidity preset | `PodUserSepolia` — sets Sepolia Inbox and configures COTI Testnet MPC executor |

Point `configureCoti` at the [COTI Testnet MPC executor](coti-testnet.md#pod-contracts) and chain ID `7082400` if you are not using `PodUserSepolia`.
