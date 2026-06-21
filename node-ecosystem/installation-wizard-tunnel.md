# Wizard tunnel (COTI subdomain + `--with-frp`)

This is the **simplest** wizard path: on **Setup FQDN**, click **Generate FQDN for Me**. The wizard shows a success message and the **Node FQDN** value to use in the installer — a **COTI-assigned hostname** under the network’s managed zone (for example `drove-nova-11.testnet.nodes.coti.network` on testnet; the exact parent suffix depends on the environment). Then run the installer with **`--with-frp`**.

← Back to [**Installation overview**](installation.md) · Related: [**Own domain (Nginx)**](installation-own-domain.md) · [**Manual full node setup**](manual-full-node.md)

## What runs on your machine

There is **no host Nginx** (no Let’s Encrypt, no ports **80/443** published for TLS on the VM). The tunnel flow **does** run an internal **`nginx-frpc-gateway`** Docker container: **COTI edge → frpc → internal Nginx (:8080) → full node / operator dashboard**. That gateway rewrites `/rpc`, `/ws`, `/metrics`, and `/operator/` to the node services inside Compose.

## What COTI provides

* **No third-party domain to buy** — DNS for that hostname is operated by COTI.
* **No TLS certificate on your server** — HTTPS terminates at COTI’s edge; **FRP (frps)** and DNS route traffic to **frpc** on your machine, which forwards through an **internal Nginx gateway** (Docker-only, no certificates) to the full node’s JSON-RPC, WebSocket, metrics, and operator dashboard.

## What you skip on the host

* **Inbound firewall rules for 80, 443, and 7400 from the public internet** are not required for this mode as designed: RPC over HTTPS reaches you through the tunnel; P2P can work with normal outbound connectivity.

The installer enables the **FRPC** Compose profile (including an internal **nginx-frpc-gateway** for `/rpc`, `/ws`, `/metrics`, and `/operator/` path rewrite), keeps **host Nginx + Let’s Encrypt off**, and **skips** `ufw` / `iptables` checks that assume you must open 80/443/7400 inbound. It still requires **port 7400 free locally** (no other process binding it) so the node container can use it.

## Prerequisites

1. **Server** meeting [**Server requirements**](server-requirements.md) (certified **Ubuntu 24.04 LTS** on Linux, or **Windows 11** + **WSL 2** + **Ubuntu 24.04 LTS**; disk, RAM), with **root access**.
2. The **FQDN** string shown in the wizard after generation (same value the one-liner expects; hostname pattern is network-specific).
3. **Node private key** (64 hex chars) from the wizard or your own.

## One-line command

The wizard shows **Linux / WSL** and **macOS** tabs. Use the line that matches where Docker runs. `<network>` is `mainnet` or `testnet`. `<FQDN>` is the COTI-assigned hostname; `<PRIVATE_KEY>` may include or omit the `0x` prefix.

**Linux / WSL (Ubuntu 24.04)** — `install_coti-full-node.sh` at `https://fullnode.<network>.coti.io/install-linux`; run as **root**:

```bash
curl -sL https://fullnode.<network>.coti.io/install-linux | sudo bash -s -- "<PRIVATE_KEY>" "<FQDN>" --with-frp
```

**macOS** — `install_coti-full-node-mac.sh` at `https://fullnode.<network>.coti.io/install-mac`; **do not** use `sudo`:

```bash
curl -sL https://fullnode.<network>.coti.io/install-mac | bash -s -- "<PRIVATE_KEY>" "<FQDN>" --with-frp
```

**Windows 11:** use **WSL 2** + **Ubuntu 24.04 LTS** and the **Linux** command above. There is no separate Windows installer path.

## What the installer does (this flow)

Driven by `install_coti-full-node.sh` (`https://fullnode.<network>.coti.io/install-linux`) on Linux/WSL, or `install_coti-full-node-mac.sh` (`https://fullnode.<network>.coti.io/install-mac`) on macOS:

1. **OS and inputs** — Certified Ubuntu version check, root, valid hex key and hostname (non-24.04 may prompt; see [**Server requirements → Windows 11 with WSL 2**](server-requirements.md#windows-11-with-wsl-2)).
2. **Pre-checks** — Writable install dir, disk space; **no** inbound 80/443/7400 firewall enforcement; **7400** must not already be in use locally.
3. **Packages** — Docker, Compose, `curl`, `git`, `jq`, `dnsutils` (**no** `certbot` — host Nginx/Let’s Encrypt are not used in this flow).
4. **Clone** — `coti-full-node` into the current directory (must be empty).
5. **Config** — `.env` (host: `NETWORK`, image tag, FQDN, `FRPC_ENABLED`, FRPS hosts), chain defaults from **`networks/<network>.env`**, `nodekey`, **FRPC** `frpc-*.toml`, and internal **`nginx/frpc-gateway.conf`** when `--with-frp` is set.
6. **Host Nginx / Certbot** — **Skipped**; TLS is at COTI’s edge. An internal HTTP-only Nginx gateway runs inside Docker for path rewrite.
7. **Launch** — `./start_coti-full-node.sh` starts the node, **nginx-frpc-gateway**, and two regional **frpc** containers (`frpc-1`, `frpc-2`; defaults in `networks/<network>.env`). Each `frpc` tunnel terminates at the internal gateway on port **8080**, which rewrites `/rpc`, `/ws`, `/metrics`, and `/operator/` to the node and operator dashboard.

## After the command finishes

The script prints a summary (FRPC gateways, custom domain, logs). The node syncs; the wizard advances when peer discovery sees your node. Warm-up / hot / NFT rules are in the [Glossary](ui-guide/glossary.md).

### Operator status page (local)

After `./start_coti-full-node.sh`, a small **local** dashboard shows whether the node is running, has peers, is syncing, and (when configured) whether DNS or the FRPC gateway look healthy. On the machine where Docker runs, open [http://127.0.0.1:8090](http://127.0.0.1:8090) (localhost only; auto-refreshes about every 15 seconds). Over SSH: `ssh -L 8090:127.0.0.1:8090 user@your-node`, then open the same URL in your desktop browser.

With the tunnel, the same page is also exposed at **`https://<your-coti-fqdn>/operator/`** through the edge (path rewrite via the internal Nginx gateway).

{% hint style="warning" %}
**Rewards need a reachable public RPC name.** Monitoring uses your **COTI-assigned** hostname and edge TLS. If DNS or the tunnel is wrong, uptime may not accrue. See [Glossary](ui-guide/glossary.md) and [**Server requirements**](server-requirements.md).
{% endhint %}

## Flags relevant to this flow

| Flag | Purpose |
|------|---------|
| **`--with-frp`** | Enables FRPC + internal Nginx gateway (no host TLS/certs), relaxes inbound 80/443/7400 firewall checks (this guide). |
| **`--with-nginx`** | Own-domain path instead — see [Own domain (Nginx)](installation-own-domain.md). |
| **`--testnet`**, **`--mainnet`** | Select chain profile (image, bootnodes, FRPS regional defaults, disk requirement). Piped wizard installs infer network from the FQDN; local script runs require an explicit flag. |
| **`--frpc-custom-domain=`**, **`--frpc-auth-token=`** | Optional FRPC edge hostname and auth token when COTI assigns them separately from the node FQDN. |
| **`--frps-server-addr-1=`**, **`--frps-server-addr-2=`**, **`--frps-server-port=`** | Override regional FRPS gateways (defaults in `networks/<network>.env`). |

FRPC is **off by default**; use **`--with-frp`** to enable it. Do not pass **`--with-frp`** and **`--with-nginx`** on the same install.

## Troubleshooting

* **FRPC / tunnel** — Confirm `docker ps` shows `nginx-frpc-gateway`, `frpc-1`, `frpc-2`, and the full-node container; the COTI hostname resolves and reaches the edge. Check logs: `docker logs` on the frpc containers and `coti-<network>-full-node`. Test RPC locally via the gateway: `docker exec coti-<network>-nginx-frpc-gateway wget -qO- --post-data='{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' --header='Content-Type: application/json' http://127.0.0.1:8080/rpc`.
* **Port 7400 in use** — Another process is bound to 7400; free it before re-running.
* **Dirty directory** — Installer needs an empty folder; move or remove an old `coti-full-node` clone.
