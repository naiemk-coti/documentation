# Installation

This page explains what happens when you run the automated installer produced by the [**`/setup`** wizard](ui-guide/). It is the destination for the **"Learn more about installation"** link in the installer step. See [Networks](./#networks) for the testnet and mainnet web-app URLs.

{% hint style="info" %}
This page covers the **automated** installer (the single `curl | sudo bash` command) — the **web app wizard** path. OS, Docker, and hardware sizing are shared with the manual path — see [**Server requirements**](server-requirements.md). For the **manual** Docker-based procedure (Git clone, start scripts, FAQ) **without** the wizard, see [**Manual full node setup**](manual-full-node.md).
{% endhint %}

## Certified operating system and hardware

The **same** certified OS and server sizing apply to the wizard and to [manual full node setup](manual-full-node.md). Full detail — Ubuntu version, tested Docker/Compose, CPU, memory, disk by network, and example cloud SKUs — is on [**Server requirements**](server-requirements.md).

## Installation flavors

There are **three** supported ways to bring up a node end-to-end. The first two use the **same** installer script from the wizard; the third is documented on [**Manual full node setup**](manual-full-node.md).

### 1. Wizard + COTI-managed tunnel (`--with-frp`) — simplest

This is the fastest path for new operators: complete the [**UI guide**](ui-guide.md) flow, generate a key in the app, receive a **COTI-assigned hostname** (a subdomain under the `fullnode.<network>.coti.io` namespace, for example `<your-id>.fullnode.testnet.coti.io`), and run the one-liner the wizard gives you **including** the `--with-frp` flag.

**What COTI provides**

* **No third-party domain to buy** — DNS and naming for that hostname are operated by COTI.
* **No TLS certificate on your server** — HTTPS terminates at COTI’s edge; the **FRP (frps)** tier and DNS routing deliver traffic to your node’s **FRP client (frpc)**, which forwards to the full node’s JSON-RPC on the Docker network.

**What you skip on the host**

* **Inbound firewall rules for 80, 443, and 7400 from the public internet are not required** for this mode to work as designed: HTTP(S) for RPC is reached via the tunnel; P2P can operate with outbound connectivity typical of a home or locked-down VPS.

The installer, when run with `--with-frp`, enables the **FRPC** Compose profile, leaves **Nginx + Let’s Encrypt disabled**, and **does not** enforce `ufw` / `iptables` checks that assume you must open 80/443/7400 inbound.

### 2. Wizard + your own domain + Nginx (`--nginx`)

Use this when you already own a DNS name and want **Let’s Encrypt** and **Nginx** on **your** server (HTTPS on your VM).

**What you need**

* A publicly resolvable **FQDN** with an **A record** to your server’s public IP, propagated before install.
* **Ports 80 and 443** free on the host (ACME + HTTPS).
* **Port 7400** (TCP and UDP) available for P2P; firewall and cloud security groups must allow the traffic the [**Server requirements**](server-requirements.md) and [**Manual full node setup → Network configuration**](manual-full-node.md#network-configuration) pages describe.

The wizard’s command should include **`--nginx`** (or rely on your installer defaults if your distribution enables Nginx by default).

### 3. Manual install (no wizard)

Clone the repo, configure Docker, and run start/stop scripts yourself — see [**Manual full node setup**](manual-full-node.md). You choose Nginx, FRPC, or neither according to that page and your `.env`.

---

The sections below focus on the **automated** `curl | sudo bash` installer; prerequisites depend on which flavor (1 vs 2) you use.

## What you need before running the installer (flavor 2 — own domain + Nginx)

Use this checklist only when you are **not** using **`--with-frp`**.

1. **A server** that satisfies [**Server requirements**](server-requirements.md), with **root access**.
2. **Ports 80 and 443** free on the server (HTTP challenge + HTTPS).
3. **Port 7400 (TCP + UDP)** free and allowed through firewall and cloud security groups (P2P + discovery).
4. **A publicly-resolvable FQDN** (for example `node1.example.com`) with an **A record** pointing to the server’s public IP — configured and propagated **before** you run the installer.
5. **A node private key** (64 hex characters) — the wizard can generate one for you, or you can bring your own.

{% hint style="warning" %}
**Reward eligibility still requires a working public RPC URL.** For flavor **2**, the installer obtains a Let’s Encrypt certificate and serves `https://<your-fqdn>/rpc`. For flavor **1**, monitoring uses the **COTI-assigned** hostname and edge TLS — your node must still be reachable for health checks through that name. If DNS or the tunnel is wrong, uptime may not accrue. See [glossary.md](glossary.md) and [**Server requirements**](server-requirements.md).
{% endhint %}

## What you need before running the installer (flavor 1 — `--with-frp`)

1. **A server** that satisfies [**Server requirements**](server-requirements.md) (disk, RAM, Ubuntu 24.04 LTS), with **root access**.
2. The **FQDN string** issued by the wizard (your `*.fullnode.<network>.coti.io`-style hostname).
3. **A node private key** (64 hex characters) from the wizard or your own.
4. **Port 7400** must still be **free on the host** (not used by another process) so the node container can bind it locally; **inbound** 7400/80/443 from the internet are **not** required on the firewall for this flavor.

## The one-line command

The wizard generates a command of this shape:

**Flavor 1 (COTI tunnel):**

```bash
curl -sL https://fullnode.<network>.coti.io | sudo bash -s -- "<PRIVATE_KEY>" "<FQDN>" --with-frp
```

**Flavor 2 (own domain + Nginx):**

```bash
curl -sL https://fullnode.<network>.coti.io | sudo bash -s -- "<PRIVATE_KEY>" "<FQDN>" --nginx
```

`<network>` is either `mainnet` or `testnet`, `<PRIVATE_KEY>` is the 64-hex node key (with or without the `0x` prefix), and `<FQDN>` is the hostname (COTI-issued or yours).

{% hint style="danger" %}
Piping `curl` into `sudo bash` executes a remote script as root. Only run commands you obtained from the official wizard, served over `https://fullnode.mainnet.coti.io` or `https://fullnode.testnet.coti.io`. Inspect the script first if you are unsure — the source is the [`coti-full-node`](https://github.com/coti-io/coti-full-node) repository's `install_coti-full-node.sh`.
{% endhint %}

## What the installer does

The installer is driven by [`install_coti-full-node.sh`](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh) in the `coti-full-node` repository. It executes the following phases on your server:

### 1. OS and input validation

* Detects `/etc/os-release` and warns if the distribution is not Ubuntu 24.04.x.
* Confirms the script is running as root.
* Validates that the private key is exactly 64 hex characters.
* Validates the FQDN as a well-formed hostname (≤ 253 chars).

### 2. Environment pre-checks

* Confirms the current directory is writable and has enough free disk space for the selected network (see [**Server requirements → Storage by network**](server-requirements.md#storage-by-network)).
* **Flavor 2 (`--nginx`):** confirms ports **80**, **443**, and **7400** are free; confirms `ufw` / `iptables` do not block those ports when applicable.
* **Flavor 1 (`--with-frp`):** skips inbound firewall checks for 80/443/7400; still checks **7400** is not already bound by another process on the host.

### 3. Host dependencies

Installs the packages required to run the node:

* Docker and Docker Compose (skipped if Docker is already present).
* **`certbot`** only when **Nginx / Let’s Encrypt** is enabled (`--nginx`).
* `curl`, `git`, `jq`, `dnsutils`.

### 4. Repository checkout

Clones the [`coti-full-node`](https://github.com/coti-io/coti-full-node) repository into the current directory on the tagged branch (for example `coti-mainnet`), and refuses to proceed if a clone already exists.

### 5. Node configuration

* Writes a `.env` file with the server's detected public IP and the chosen FQDN.
* Writes the private key into a `nodekey` file for the node process.
* **Flavor 1:** writes **FRPC** client configs and sets `FRPC_ENABLED=true` so Compose starts the `frpc` profile.

### 6. HTTPS and reverse proxy (flavor 2 only)

When **`--nginx`** is in effect:

* Starts a temporary Nginx on port 80 to serve the ACME challenge.
* Requests a Let's Encrypt certificate for the FQDN via `certbot`.
* Tears the temporary Nginx down.
* Writes the full Nginx config (`/rpc`, `/ws`, `/metrics` upstreams) and starts the production proxy.

With **`--with-frp`**, this entire step is **skipped** — TLS and public HTTP(S) for RPC are handled **at COTI’s edge**, not on your VM.

### 7. Node launch

Runs `./start_coti-full-node.sh`, which brings up the COTI full node container under Docker Compose and begins syncing the chain. With **`--with-frp`**, the **FRPC** containers start as well.

## After the command finishes

On success the script prints a summary (HTTPS URL, FRPC status, log command). At this point:

* The node starts syncing blocks from peers.
* The wizard in the browser polls the peer-discovery service every \~60 seconds and advances as soon as your node appears in the peer set.
* You enter the **warm-up period** — once your node has been continuously observed for `HOT_THRESHOLD_HOURS` inside a `HOT_WINDOW_HOURS` window, a Soulbound **Node NFT** is minted to your operator wallet and your node becomes **hot**. See the [Glossary](ui-guide/glossary.md) for the exact definitions.

## Optional flags

The installer accepts flags beyond what the wizard always shows:

| Flag | Purpose |
|------|---------|
| **`--with-frp`** | **Flavor 1:** enables the **FRPC** profile, keeps **Nginx off**, and relaxes **inbound** 80/443/7400 firewall checks. Use with the **COTI-assigned** FQDN from the wizard. |
| `--nginx` | **Flavor 2:** Nginx + Let’s Encrypt on the host for your own FQDN. Clears tunnel mode if combined after `--with-frp`. |
| `--no-nginx` | Skip Nginx + Let’s Encrypt. Not suitable for reward eligibility when the ecosystem expects HTTPS on **your** domain. |
| `--frpc` | Enable FRPC **without** tunnel relaxations (advanced; own FRPC setup). |
| `--no-frpc` | Disable FRPC. |
| `--staging` | Use the Let's Encrypt **staging** environment (only relevant with `--nginx`). |

Example (flavor 2 dry run):

```bash
curl -sL https://fullnode.mainnet.coti.io | sudo bash -s -- "0x..." "node1.example.com" --nginx --staging
```

## Troubleshooting

* **Certbot failed** (flavor 2) — Confirm your A-record has propagated (`dig <your-fqdn>`) and that ports 80/443 are reachable from the public internet. Then re-run the installer.
* **Port already in use** — Stop whatever is occupying the port (another COTI install, Apache, etc.). The installer still requires **7400** free locally for the node process.
* **`coti-full-node` directory already exists** — The installer requires a clean directory. Remove or archive the previous clone before re-running.
* **Node does not appear in the wizard** — Confirm the node container is running (`docker ps`), the chain is syncing (`docker logs -f coti-<network>-full-node`), and for flavor 2 that the FQDN resolves to your server's public IP; for flavor 1 that FRPC containers are healthy and the COTI hostname routes correctly.

For manual node management (restart, stop, logs, cleanup) when you run the repo yourself, see [**Manual full node setup → Restarting your node**](manual-full-node.md#restarting-your-node).
