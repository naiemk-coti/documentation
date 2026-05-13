---
hidden: true
---

# Node Ecosystem

Running a node helps secure and decentralize the COTI network and support the overall ecosystem. While there are some similarities to other L2 networks, COTI’s architecture has its own nuances and requirements that we cover in this documentation.

## What is a COTI node?

In the COTI network, full nodes are decentralized, lean clients that play a critical role in maintaining the network’s security, scalability, and overall functionality. Anyone can run a full node to support the network and, when they meet the ecosystem’s eligibility rules, earn rewards.

Running a COTI full node downloads a copy of the COTI blockchain and verifies the validity of every block. Unlike validator nodes in Ethereum, COTI full nodes do not actively participate in consensus nor in block proposal; that is the role of the COTI sequencer (see [**COTI Architecture**](https://docs.coti.io/coti-documentation/how-coti-works/introduction/coti-architecture)).

## Why run a node?

Running a COTI node offers several benefits:

* **Network participation** — Contribute to the decentralization and robustness of the network.
* **Community support** — Strengthen the ecosystem and help drive adoption of COTI’s technology.
* **Rewards and incentives** — Eligible operators can earn rewards when their node meets the thresholds and rules described in this Node Ecosystem section (for example uptime, DNS reachability, and license or holdings requirements where applicable).

The **COTI Node Ecosystem** is the product surface that lets anyone run, monitor, and earn rewards from a COTI full node through a guided flow. It is composed of:

* a web app that guides operators from zero to a live, reward-eligible node (see [Networks](./#networks) below for the URLs),
* an automated installer that stands up a COTI full node on Ubuntu 24.04 LTS in a single command,
* a set of backend services that discover peers, mint node NFTs, monitor uptime, and distribute rewards each epoch.

This section documents the product — what it does, how to install a node through it, how its UI is organized, and the terminology you will encounter along the way.

## Running a full node: two paths

The same **COTI full node** software powers the network whether you onboard through the web app or build the stack yourself. Pick the path that matches how you want to operate.

| Path | When to use it | Documentation |
| ---- | --------------- | --------------- |
| **Web app wizard** | You want the guided flow, the one-liner installer from [Networks](#networks), HTTPS, and automatic monitoring hooks from the product. | [**Installation**](installation.md), [**UI guide**](ui-guide.md) |
| **Manual (without the wizard)** | You prefer Git clone, Docker Compose, and scripts on your own terms — not the Nodes web UI installer. | [**Manual full node setup**](manual-full-node.md) |

The [**COTI Node Ecosystem Litepaper**](coti-node-ecosystem-litepaper.md) summarizes the Node Economy; incentive rules apply to **both** paths when you meet eligibility.

Operators on the **manual** path can still earn **rewards** when they satisfy the same thresholds as wizard users (FQDN, reachability, uptime, license / holdings, etc.) — see [**Installation**](installation.md) and [**Manual full node setup**](manual-full-node.md).

## Networks

The ecosystem runs on two networks. All guidance in this section applies to both unless noted; look up the right URL or value in the table below.

|                                 | **Testnet**                                              | **Mainnet**                              |
| ------------------------------- | -------------------------------------------------------- | ---------------------------------------- |
| Web app                         | [dev.nodes.coti.io](https://dev.nodes.coti.io)           | [nodes.coti.io](https://nodes.coti.io)   |
| Status page (public, hot nodes) | [testnet.uptime.coti.io](https://testnet.uptime.coti.io) | [uptime.coti.io](https://uptime.coti.io) |
| Recommended node disk space     | ≥ 100 GB                                                 | ≥ 700 GB                                 |
| Installer host                  | `fullnode.testnet.coti.io`                               | `fullnode.mainnet.coti.io`               |

The **status page** is the public [Better Stack](https://betterstack.com/) dashboard where every hot node's monitor is visible. It is the fastest way to eyeball the current health of the whole fleet.

## What the COTI Node Ecosystem gives you

* **One-command install** of a COTI full node on a certified OS (Ubuntu 24.04 LTS) with HTTPS and a proxy already configured.
* **Live visibility** into the node fleet — Who is online, which nodes are hot, how many earned rewards this epoch.
* **Per-operator dashboard** for your own node(s): thermal state, uptime, latency, rewards history, eligibility.
* **Automatic monitoring registration** in Better Stack once your node is recognized by the network.
* **Rewards distribution** every epoch to operators who meet the eligibility rules (holdings + uptime).

## High-level architecture

```mermaid
flowchart LR
    Operator([Node operator])
    UI["COTI Nodes web app"]
    FullNode["COTI full node<br/>(operator server)"]
    PDS["Peer Discovery"]
    NFT["NFT service"]
    BSI["Better Stack integration"]
    NHM["Node Health Monitor"]
    NRS["Node Rewards"]
    BetterStack[(Better Stack)]
    Chain[(COTI network)]

    Operator -->|guided setup| UI
    UI -->|one-liner installer| FullNode
    FullNode -->|admin_peers, eth_blockNumber| PDS
    PDS -->|"hot event"| NFT
    NFT -->|mint Soulbound NFT| Chain
    NFT --> BSI
    BSI -->|register monitor via FQDN| BetterStack
    BetterStack -->|probe RPC over HTTPS| NHM
    NHM -->|health check| FullNode
    NRS -->|read uptime + holdings| BetterStack
    NRS -->|distribute rewards per epoch| Chain
    UI -->|read stats + per-node data| PDS
    UI --> NFT
    UI --> BSI
    UI --> NRS
```

{% hint style="warning" %}
**A valid DNS (FQDN) is required to earn rewards.** The ecosystem measures your node's uptime by reaching its JSON-RPC endpoint through the domain name you configure. A node without a reachable DNS can still sync the chain, but it will **not** be credited with uptime and therefore will **not** receive rewards. See [installation.md](installation.md) for DNS prerequisites.
{% endhint %}

## Where to go next

* [**Manual full node setup (without the web app wizard)**](manual-full-node.md) — hardware, Docker Compose, ports, Git clone, restart/stop, FAQ.
* [**COTI Node Ecosystem Litepaper**](coti-node-ecosystem-litepaper.md) — Node Economy (PDF embed).
* [**Installation**](installation.md) — automated installer (wizard path) and DNS/server requirements.
* [**UI guide**](ui-guide.md) — a page-by-page tour of the web app, with focus on the spin-up flow and the warm-up period.
* [**Features**](features.md) — everything the product does, end-to-end.
* [**Backend services**](backend-services.md) — the five services behind the ecosystem, described from an operator's perspective.
* [**Glossary**](glossary.md) — thermal states, NFT states, warm-up windows, eligibility, and other terms you will see in the UI.
