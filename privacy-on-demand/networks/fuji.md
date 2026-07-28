# Avalanche Fuji

Avalanche Fuji is a supported **host chain** for Privacy on Demand. Your dApp contracts and the local **Inbox** live here; private computation still runs on [COTI Testnet](coti-testnet.md).

> **Note:** Addresses below match `pod-ecosystem-integration/deployConfig.json` (Inbox salt `pod.inbox.v2.2`). They may change after contract redeploys. Confirm against that file or your SDK release before production use.

## Network details

| Parameter | Value |
| --- | --- |
| Network name | Avalanche Fuji C-Chain |
| Chain ID | `43113` |
| Currency | AVAX |
| RPC URL | `https://api.avax-test.network/ext/bc/C/rpc` |
| Block explorer | [https://testnet.snowscan.xyz](https://testnet.snowscan.xyz) |

Private execution for Fuji dApps targets **COTI Testnet** (`7082400`). See [COTI Testnet](coti-testnet.md) for the MPC executor and COTI-side Inbox.

## PoD contracts

| Contract | Address | Description |
| --- | --- | --- |
| Inbox | [`0x3b8B70819f27e0438cBcE7f31894f799da52648F`](https://testnet.snowscan.xyz/address/0x3b8B70819f27e0438cBcE7f31894f799da52648F) | Cross-chain message router (CREATE3; same address on every PoD chain) |
| Price oracle | [`0x95ce33378c88734f3d86b51a4c6dc588722995fd`](https://testnet.snowscan.xyz/address/0x95ce33378c88734f3d86b51a4c6dc588722995fd) | Local/remote token prices used by Inbox fee conversion |
| MpcAdder (example) | [`0x8b7d9e70477aabe68500b72acb7f367993edde39`](https://testnet.snowscan.xyz/address/0x8b7d9e70477aabe68500b72acb7f367993edde39) | Reference primitive-only adder dApp on Fuji |

## PoD cross-chain Privacy Portal (Fuji)

This is the **PoD host-chain Privacy Portal** (factory + pTokens on Fuji, private compute via COTI Inbox). It is **not** the [native COTI Privacy Portal / PrivateERC20](../../coti-privacy-portal/README.md) that runs entirely on COTI.

| Contract | Address |
| --- | --- |
| Privacy Portal factory | [`0x0e6b35da5aa3e3aeb6f98471821d5b16552efced`](https://testnet.snowscan.xyz/address/0x0e6b35da5aa3e3aeb6f98471821d5b16552efced) |
| Portal implementation | [`0xf7f7472394868e7e32bddbbd2bd66994a40f047b`](https://testnet.snowscan.xyz/address/0xf7f7472394868e7e32bddbbd2bd66994a40f047b) |
| Pod token implementation | [`0xa7e4838327317f4ce6cc8b5ab07a57fdba842c77`](https://testnet.snowscan.xyz/address/0xa7e4838327317f4ce6cc8b5ab07a57fdba842c77) |

### Privacy Portal tokens

| Token | Underlying | Portal | pToken |
| --- | --- | --- | --- |
| pMTT | `0x328e70e1c52662cd5f19f824fcb8b463d77a6686` | `0x397b1DE4EbAaC2e522B583120C29ff97F011c84c` | `0x7BE9Cd10b51eFf6FFCE8f620EA17f6C4dc37a379` |
| pUSDC | `0x5425890298aed601595a70AB815c96711a31Bc65` | `0xa15aBf3BBf23795F7F6d018D592B448F3af1A2e5` | `0x01635605900E3200679079BD35AF0BefF25e2072` |
| pWAVAX | `0xd00ae08403B9bbb9124bB305C09058E32C39A48c` | `0x1A775D5a9d034f27dB1328B446088E93BC1bF9EE` | `0x5910f4f38660A7932485a6De854e9895E6d78D85` |

## How this network fits PoD

| Piece | Role on Fuji |
| --- | --- |
| Inbox | Host-side courier: accepts encrypted requests from your dApp and delivers COTI callbacks |
| Your dApp | Configures Inbox + COTI chain ID `7082400` + [MPC executor on COTI Testnet](coti-testnet.md#pod-contracts) |
| Price oracle | Converts Fuji fee budgets against COTI-side costs |

Flow at a glance: **user / dApp on Fuji → Fuji Inbox → (relayer) → COTI Inbox → MPC executor → callback to Fuji**.

## SDK constants

In `@coti-io/pod-sdk` / `@coti-io/coti-contracts`:

| Constant | Value |
| --- | --- |
| Chain ID | `43113` |
| Inbox | `0x3b8B70819f27e0438cBcE7f31894f799da52648F` (`FUJI_DEFAULT_INBOX_ADDRESS`) |

Point `configureCoti` at the [COTI Testnet MPC executor](coti-testnet.md#pod-contracts) and chain ID `7082400`.
