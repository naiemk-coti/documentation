# Privacy on Demand (PoD)

Privacy on Demand lets applications use **strong privacy for data and computation** while still using **ordinary EVM chains** (Ethereum, L2s, and other compatible networks) for accounts, assets, and business workflows.

This section explains **what PoD is**, **how it feels to users and operators**, and **how the main pieces fit together**. For step-by-step integration with the **COTI PoD SDK**, use the [npm package](https://www.npmjs.com/package/@coti/pod-sdk), the [documentation on GitHub](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs), and the links below.

## Who this documentation is for

| Audience | What you will get here |
| --- | --- |
| **Product, compliance, and business readers** | Plain-language model of privacy, where data lives, and what “async” private operations mean in practice. |
| **Architects and technical leads** | End-to-end diagrams, component roles, and boundaries between chains and domains. |
| **Developers** | A clear map from concepts to Solidity/TypeScript work, plus pointers to the authoritative SDK docs. |

## Table of contents

### Understand first (readable without writing code)

1. [What is Privacy on Demand?](what-is-privacy-on-demand.md) — Problem, promise, and constraints in everyday language.
2. [How a private request travels end to end](how-a-private-request-travels-end-to-end.md) — Timeline from user action to decrypted result.
3. [Architecture and main components](architecture-and-components.md) — Where **Inbox**, **MPC executor**, **PodUser**, and **PodLib** sit, with diagrams.
4. [Glossary](glossary.md) — Short definitions of terms you will see in PoD and SDK docs.

### Deeper context

5. [Async private operations (why it is not instant)](async-private-operations.md) — What “pending” means and why UX must reflect it.
6. [For developers: mapping concepts to the SDK](for-developers-mapping-to-the-sdk.md) — Checklists and links to the [PoD SDK documentation on GitHub](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs).
7. [Tutorial: private Adder on Sepolia](tutorial-private-adder-sepolia.md) — How to write a minimal adder contract, deploy with `PodUserSepolia`, and encrypt/decrypt with TypeScript.

## Official technical reference

The machine-readable contracts, types, and APIs live in the open-source SDK. Treat this book chapter as the **human-oriented companion**; treat the repository as the **source of truth** for signatures, fees, and network constants:

- [COTI PoD SDK — documentation index](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs)
