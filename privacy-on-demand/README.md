# Privacy on Demand (PoD)

Privacy on Demand lets applications use **strong privacy for data and computation** while still using **ordinary EVM chains** (Ethereum, L2s, and other compatible networks) for accounts, assets, and business workflows.

> **Development status:** This Privacy on Demand material and the **COTI PoD SDK** it describes are **under active development**. Treat them accordingly: on-chain and client code **may not yet be fully audited**, and **breaking changes** (APIs, ABIs, addresses, presets, or documentation) can occur as the stack matures. Pin versions, follow release notes, and perform your own review before relying on anything in production.

<div style="font-size: 1.45rem; line-height: 1.72; margin-bottom: 2rem;">

<h2 style="font-size: 2.35rem; font-weight: 600; margin-top: 0; margin-bottom: 1rem; line-height: 1.2;">Quick Access</h2>

- **[Tutorials: PoD dApps (choose your integration model)](tutorials-privacy-on-demand.md)** — Primitive-only vs custom COTI logic, then links to step-by-step guides.
- **[Architecture and design](architecture-and-components.md)** — Inbox, MPC executor, PodUser, PodLib, and how they connect.
- **[Interactive PoD architecture (pod.coti.io)](https://pod.coti.io/)** — Live demo: play the MpcAdder journey across Sepolia, relayer, and COTI, with GitHub source links and gas/fee visualization.
- **[Learn about fees](how-poa-fees-work.md)** — How PoA/PoD fees split across COTI and your host chain.
- **[Millionaires demo](https://millionaire.demo.coti.io)** — Live demo (external).

<h2 style="font-size: 2.35rem; font-weight: 600; margin-top: 1.75rem; margin-bottom: 1rem; line-height: 1.2;">Further resources</h2>

- **[Examples](reference-examples-and-contracts.md)** — Contract examples in `@coti-io/coti-contracts`.
- **[TypeScript PoD SDK](typescript-pod-sdk.md)** — Full `@coti/pod-sdk` reference.
- **[coti-sdk-pod on GitHub](https://github.com/coti-io/coti-sdk-pod)** — TypeScript SDK source.
- **[coti-contracts on GitHub](https://github.com/coti-io/coti-contracts/tree/main/contracts/pod)** — Solidity libraries and examples.
- **[coti-pod-inbox-contracts on GitHub](https://github.com/coti-io/coti-pod-inbox-contracts)** — Inbox implementation and fee contracts.

</div>

---

This section is the **canonical user-facing documentation** for PoD. See [Architecture and main components](architecture-and-components.md) for the three-package stack (`@coti/pod-sdk`, `@coti-io/coti-contracts`, `@coti-io/coti-pod-inbox-contracts`).

## Who this documentation is for

| Audience | What you will get here |
| --- | --- |
| **Product, compliance, and business readers** | Plain-language model of privacy, where data lives, and what “async” private operations mean in practice. |
| **Architects and technical leads** | End-to-end diagrams, component roles, and boundaries between chains and domains. |
| **Developers** | Tutorials, API reference, checklists, and links to authoritative contract source. |

## Table of contents

### Understand first (readable without writing code)

1. [What is Privacy on Demand?](what-is-privacy-on-demand.md)
2. [Privacy on Demand (PoD) for Dummies](pod-for-dummies.md) — non-technical overview
3. [How a private request travels end to end](how-a-private-request-travels-end-to-end.md)
4. [Architecture and main components](architecture-and-components.md)
5. [Glossary](glossary.md)

### Deeper context

6. [Async private operations (why it is not instant)](async-private-operations.md)
7. [How do PoA fees work?](how-poa-fees-work.md)
8. [For developers: mapping concepts to the SDK](for-developers-mapping-to-the-sdk.md)

### Integration reference

9. [Account onboarding (AES key)](account-onboarding-aes-key.md)
10. [COTI TypeScript SDK for PoD](coti-typescript-sdk-for-pod.md)
11. [TypeScript PoD SDK](typescript-pod-sdk.md) — full `@coti/pod-sdk` reference
12. [Reference: data types (`it*`, `ct*`, `gt*`)](reference-data-types.md)
13. [Reference: PodLib primitives](reference-podlib-and-primitives.md)
14. [Contract patterns checklist](contract-patterns-checklist.md)
15. [Reference: examples and contracts](reference-examples-and-contracts.md)

### Tutorials (hands-on)

16. [Tutorials: building PoD dApps](tutorials-privacy-on-demand.md)
17. [Cookbook: private investor allocations with PoD](cookbook-private-investor-allocations.md)
18. [Tutorial: private Adder on Sepolia](tutorial-private-adder-sepolia.md)
19. [Tutorial: custom privacy logic with PoD](tutorial-custom-logic.md)

## Official code repositories

Machine-readable **signatures and constants** live in the repos above. This book is the **documentation source of truth** for integration guidance:

- [COTI contracts — PoD contracts](https://github.com/coti-io/coti-contracts/tree/main/contracts/pod)
- [COTI PoD SDK — TypeScript source](https://github.com/coti-io/coti-sdk-pod)
- [COTI PoD inbox contracts](https://github.com/coti-io/coti-pod-inbox-contracts)
