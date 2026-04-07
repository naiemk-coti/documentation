# Welcome

<div style="font-size: 1.45rem; line-height: 1.72; margin-bottom: 2rem;">

**Privacy on Demand (PoD)** lets you run private computation on COTI while keeping orchestration on an EVM chain. Use the links below by what you want to do first. The rest of the COTI documentation stays in the sidebar.

<h2 style="font-size: 2.35rem; font-weight: 600; margin-top: 1.75rem; margin-bottom: 1rem; line-height: 1.2;">Quick Access</h2>

- **[Start building with PoD (example)](privacy-on-demand/tutorial-private-adder-sepolia.md)** — Step-by-step Adder on Sepolia with Solidity and TypeScript.
- **[Architecture and design](privacy-on-demand/architecture-and-components.md)** — Inbox, MPC executor, PodUser, PodLib, and how they connect.
- **[Learn about fees](privacy-on-demand/how-poa-fees-work.md)** — How PoA/PoD fees split across COTI and your host chain.
- **[Millionaires demo](https://milionaire.demo.coti.io)** — Live demo (external).

<h2 style="font-size: 2.35rem; font-weight: 600; margin-top: 1.75rem; margin-bottom: 1rem; line-height: 1.2;">Further resources</h2>

- **[Examples](https://github.com/cotitech-io/coti-pod-sdk/tree/main/contracts/examples)** — Contract examples in the PoD SDK repo.
- **[PoD SDK documentation](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs)** — Full SDK docs on GitHub.

For the broader COTI network (native chain, tools, guides), see the sidebar: [How COTI Works](how-coti-works/), [Build on COTI](build-on-coti/).

</div>

---

## Privacy on Demand (PoD)

Privacy on Demand lets applications use **strong privacy for data and computation** while still using **ordinary EVM chains** (Ethereum, L2s, and other compatible networks) for accounts, assets, and business workflows.

This section explains **what PoD is**, **how it feels to users and operators**, and **how the main pieces fit together**. For step-by-step integration with the **COTI PoD SDK**, use the [npm package](https://www.npmjs.com/package/@coti/pod-sdk), the [documentation on GitHub](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs), and the links below.

### Who this documentation is for

| Audience | What you will get here |
| --- | --- |
| **Product, compliance, and business readers** | Plain-language model of privacy, where data lives, and what “async” private operations mean in practice. |
| **Architects and technical leads** | End-to-end diagrams, component roles, and boundaries between chains and domains. |
| **Developers** | A clear map from concepts to Solidity/TypeScript work, plus pointers to the authoritative SDK docs. |

### Table of contents

#### Understand first (readable without writing code)

1. [What is Privacy on Demand?](privacy-on-demand/what-is-privacy-on-demand.md) — Problem, promise, and constraints in everyday language.
2. [How a private request travels end to end](privacy-on-demand/how-a-private-request-travels-end-to-end.md) — Timeline from user action to decrypted result.
3. [Architecture and main components](privacy-on-demand/architecture-and-components.md) — Where **Inbox**, **MPC executor**, **PodUser**, and **PodLib** sit, with diagrams.
4. [Glossary](privacy-on-demand/glossary.md) — Short definitions of terms you will see in PoD and SDK docs.

#### Deeper context

5. [Async private operations (why it is not instant)](privacy-on-demand/async-private-operations.md) — What “pending” means and why UX must reflect it.
6. [How do PoA fees work?](privacy-on-demand/how-poa-fees-work.md) — Two-way Inbox budgets, oracle conversion, and step-by-step gas-unit consumption (worked example).
7. [For developers: mapping concepts to the SDK](privacy-on-demand/for-developers-mapping-to-the-sdk.md) — Checklists and links to the [PoD SDK documentation on GitHub](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs).
8. [Tutorial: private Adder on Sepolia](privacy-on-demand/tutorial-private-adder-sepolia.md) — How to write a minimal adder contract, deploy with `PodUserSepolia`, and encrypt/decrypt with TypeScript.

### Official technical reference

The machine-readable contracts, types, and APIs live in the open-source SDK. Treat this book chapter as the **human-oriented companion**; treat the repository as the **source of truth** for signatures, fees, and network constants:

- [COTI PoD SDK — documentation index](https://github.com/cotitech-io/coti-pod-sdk/tree/main/docs)
