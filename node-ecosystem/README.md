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
* an automated installer that stands up a COTI full node on **Ubuntu 24.04 LTS** (Linux servers) or a certified **Windows 11 + WSL 2 + Ubuntu 24.04 LTS** setup in a single command,
* a set of backend services that discover peers, mint node NFTs, monitor uptime, and distribute rewards each epoch.

This section documents the product — what it does, how to install a node through it, how its UI is organized, and the terminology you will encounter along the way.

## Running a full node: two paths

The same **COTI full node** software powers the network whether you onboard through the web app or build the stack yourself.

**If you are new to running a node**, start with the **web app wizard** — it is the fastest path for most people: open the web app from [Networks](./#networks), follow the setup flow, then read [**Installation**](installation.md) and the matching subpage — [**Wizard tunnel**](installation-wizard-tunnel.md) (`--with-frp`) or [**Own domain (Nginx)**](installation-own-domain.md) (`--with-nginx`) — plus the [**UI guide**](ui-guide/). [**Manual full node setup**](manual-full-node.md) is for operators who want Git clone, Docker Compose, and scripts **without** the wizard.

| Path                                   | When to use it                                                                                                                                                 | Documentation                                                                                                                                                    |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Web app wizard (recommended first)** | Guided flow and one-liner from [Networks](./#networks). Use **tunnel** (`--with-frp`, COTI subdomain, no host TLS) or **own domain + Nginx** (`--with-nginx`). | [**Installation**](installation.md), [**Wizard tunnel**](installation-wizard-tunnel.md), [**Own domain**](installation-own-domain.md), [**UI guide**](ui-guide/) |
| **Manual (without the wizard)**        | You administer the stack yourself — not the Nodes web UI installer.                                                                                            | Under [**Installation**](installation.md): [**Manual full node setup**](manual-full-node.md)                                                                     |

The [**COTI Node Ecosystem Litepaper**](coti-node-ecosystem-litepaper.md) summarizes the Node Economy; incentive rules apply to **both** paths when you meet eligibility.

**Certified OS and hardware** for both paths are documented once on [**Server requirements**](server-requirements.md).

Operators on the **manual** path can still earn **rewards** when they satisfy the same thresholds as wizard users (FQDN, reachability, uptime, license / holdings, etc.) — see the [**Installation**](installation.md) section, especially [**Manual full node setup**](manual-full-node.md).

## Networks

The ecosystem runs on two networks. All guidance in this section applies to both unless noted; look up the right URL or value in the table below.

|                                 | **Testnet**                                              | **Mainnet**                              |
| ------------------------------- | -------------------------------------------------------- | ---------------------------------------- |
| Web app                         | [testnet.nodes.coti.io](https://testnet.nodes.coti.io)   | [nodes.coti.io](https://nodes.coti.io)   |
| Status page (public, hot nodes) | [testnet.uptime.coti.io](https://testnet.uptime.coti.io) | [uptime.coti.io](https://uptime.coti.io) |
| Installer host                  | `fullnode.testnet.coti.io`                               | `fullnode.mainnet.coti.io`               |

Installer scripts (replace `<network>` with `testnet` or `mainnet` from the row above):

* **Linux / WSL** — `https://fullnode.<network>.coti.io/install-linux` (`install_coti-full-node.sh`)
* **macOS** — `https://fullnode.<network>.coti.io/install-mac` (`install_coti-full-node-mac.sh`)

The **status page** is the public [Better Stack](https://betterstack.com/) dashboard where every hot node's monitor is visible. It is the fastest way to eyeball the current health of the whole fleet.

## What the COTI Node Ecosystem gives you

* **One-command install** of a COTI full node on a certified OS (**Ubuntu 24.04 LTS** on Linux, or **Windows 11** with **WSL 2** and **Ubuntu 24.04 LTS**), with either a **COTI-managed tunnel** (no host Nginx) or **HTTPS on your server** when you bring your own domain.
* **Local operator status page** on the node host (`http://127.0.0.1:8090`, plus `/operator/` over HTTPS when exposed) for sync, peers, and reachability checks.
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
**A valid DNS (FQDN) is required to earn rewards.** The ecosystem measures your node's uptime by reaching its JSON-RPC endpoint through the domain name you configure. A node without a reachable DNS can still sync the chain, but it will **not** be credited with uptime and therefore will **not** receive rewards. See [**Own domain (Nginx + TLS)**](installation-own-domain.md) for DNS and port prerequisites (or [**Wizard tunnel**](installation-wizard-tunnel.md) for the COTI-assigned hostname path).
{% endhint %}

## Where to go next

**Installation (pick your path — order matches the docs sidebar):**

* [**Installation hub**](installation.md) — overview, shared flags, after-wizard notes.
* [**Wizard tunnel**](installation-wizard-tunnel.md) (`--with-frp`) — COTI subdomain, FRP, no host TLS.
* [**Own domain (Nginx + TLS)**](installation-own-domain.md) (`--with-nginx`) — your FQDN, Let’s Encrypt on the host.
* [**Manual full node setup**](manual-full-node.md) — Git clone, Docker Compose, ports, restart/stop, FAQ (no wizard; OS/hardware still [**Server requirements**](server-requirements.md)).

**Also:**

* [**Server requirements**](server-requirements.md) — certified OS, Docker stack, hardware and disk.
* [**UI guide**](ui-guide/) — wizard walkthrough and warm-up.

**Reference:**

* [**COTI Node Ecosystem Litepaper**](coti-node-ecosystem-litepaper.md) — Node Economy (PDF embed).
* [**Features**](features.md) — everything the product does, end-to-end.
* [**Backend services**](backend-services.md) — the five services behind the ecosystem, described from an operator's perspective.
* [**Glossary**](ui-guide/glossary.md) — thermal states, NFT states, warm-up windows, eligibility, and other terms you will see in the UI.
