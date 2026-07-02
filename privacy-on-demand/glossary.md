# Glossary

Short definitions for **Privacy on Demand** readers. Precise type tables: [Reference: data types](reference-data-types.md).

| Term | Meaning |
| --- | --- |
| **Privacy on Demand (PoD)** | Pattern and tooling for **private computation on COTI** while **orchestrating** from **another EVM chain** via an **Inbox**. |
| **Inbox** | On-chain **message router** on each chain that forwards private jobs toward COTI and delivers callbacks. Implementation in [`coti-pod-inbox-contracts`](https://github.com/coti-io/coti-pod-inbox-contracts). |
| **MPC executor** | COTI-side contract configured as the execution target for **library-style** PoD flows. |
| **PodUser** | Solidity **configuration mixin** for Inbox address, COTI chain id, and executor address. |
| **PodUserSepolia / PodUserFuji** | Network presets that auto-wire inbox and COTI routing in the constructor. |
| **PodLib** | Solidity **helper library** for built-in private operations at 64/128/256-bit widths. |
| **`it*` (input types)** | Encrypted user input with signature material, prepared client-side. |
| **`gt*` (compute types)** | Internal private representation during computation on **COTI** — not for EVM public APIs. |
| **`ct*` (ciphertext types)** | Encrypted outputs stored on your chain; users decrypt locally with the account AES key. |
| **PoA / PoD fees** | Native token on your chain funding COTI-side and callback execution budgets. See [How do PoA fees work?](how-poa-fees-work.md). |
| **Two-way message** | Outbound request to COTI plus inbound callback to your contract. |
| **Request ID** | 32-byte correlator tying submission to callback; indexed in compact `MessageSent` events. |
| **Account AES key** | 32-hex-character user secret for decrypting `ct*` outputs. See [Account onboarding](account-onboarding-aes-key.md). |
| **PodRequest** | TypeScript helper (`@coti/pod-sdk`) that polls inbox state across chains for async UX. |
| **PodSdkConfig** | JSON config (chains, inbox addresses, RPCs, encryption network) shared by `PodContract` and `PodRequest`. |
| **`@coti-io/coti-contracts`** | npm package with PoD Solidity libraries, interfaces, and examples. |
| **`@coti-io/coti-pod-inbox-contracts`** | npm package with Inbox implementation, fee manager, and miner contracts. |
