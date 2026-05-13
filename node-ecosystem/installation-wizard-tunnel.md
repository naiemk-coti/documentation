# Wizard tunnel (COTI subdomain + `--with-frp`)

This is the **simplest** wizard path: you complete the [**UI guide**](ui-guide/README.md), get a **COTI-assigned hostname** (for example under `fullnode.<network>.coti.io`), generate a key, and run the installer with **`--with-frp`**.

← Back to [**Installation overview**](installation.md) · Related: [**Own domain (Nginx)**](installation-own-domain.md) · [**Manual full node setup**](manual-full-node.md)

## What COTI provides

* **No third-party domain to buy** — DNS for that hostname is operated by COTI.
* **No TLS certificate on your server** — HTTPS terminates at COTI’s edge; **FRP (frps)** and DNS route traffic to **frpc** on your machine, which forwards to the full node’s JSON-RPC inside Docker.

## What you skip on the host

* **Inbound firewall rules for 80, 443, and 7400 from the public internet** are not required for this mode as designed: RPC over HTTPS reaches you through the tunnel; P2P can work with normal outbound connectivity.

The installer enables the **FRPC** Compose profile, keeps **Nginx + Let’s Encrypt off**, and **skips** `ufw` / `iptables` checks that assume you must open 80/443/7400 inbound. It still requires **port 7400 free locally** (no other process binding it) so the node container can use it.

## Prerequisites

1. **Server** meeting [**Server requirements**](server-requirements.md) (Ubuntu 24.04 LTS, disk, RAM), with **root access**.
2. The **FQDN** string issued by the wizard (`*.fullnode.<network>.coti.io`-style).
3. **Node private key** (64 hex chars) from the wizard or your own.

## One-line command

```bash
curl -sL https://fullnode.<network>.coti.io | sudo bash -s -- "<PRIVATE_KEY>" "<FQDN>" --with-frp
```

`<network>` is `mainnet` or `testnet`. `<FQDN>` is the COTI-assigned hostname; `<PRIVATE_KEY>` may include or omit the `0x` prefix.

## What the installer does (this flow)

Driven by [`install_coti-full-node.sh`](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh):

1. **OS and inputs** — Ubuntu check, root, valid hex key and hostname.
2. **Pre-checks** — Writable install dir, disk space; **no** inbound 80/443/7400 firewall enforcement; **7400** must not already be in use locally.
3. **Packages** — Docker, Compose, `curl`, `git`, `jq`, `dnsutils` (**no** `certbot` when Nginx is off).
4. **Clone** — `coti-full-node` into the current directory (must be empty).
5. **Config** — `.env`, `nodekey`, **FRPC** `frpc-*.toml` files, `FRPC_ENABLED=true`.
6. **Nginx / Certbot** — **Skipped**; TLS is at COTI’s edge.
7. **Launch** — `./start_coti-full-node.sh` starts the node and **FRPC** containers.

## After the command finishes

The script prints a summary (FRPC gateways, custom domain, logs). The node syncs; the wizard advances when peer discovery sees your node. Warm-up / hot / NFT rules are in the [Glossary](ui-guide/glossary.md).

{% hint style="warning" %}
**Rewards need a reachable public RPC name.** Monitoring uses your **COTI-assigned** hostname and edge TLS. If DNS or the tunnel is wrong, uptime may not accrue. See [Glossary](ui-guide/glossary.md) and [**Server requirements**](server-requirements.md).
{% endhint %}

## Flags relevant to this flow

| Flag | Purpose |
|------|---------|
| **`--with-frp`** | Enables FRPC, disables Nginx, relaxes inbound 80/443/7400 firewall checks. |
| `--frpc` | FRPC only, **without** tunnel relaxations (advanced). |
| `--no-frpc` | Disables FRPC (also clears tunnel mode). |
| `--nginx` | If passed **after** `--with-frp`, switches to the [own-domain](installation-own-domain.md) behavior. |

## Troubleshooting

* **FRPC / tunnel** — Confirm `docker ps` shows `frpc` containers, and the COTI hostname resolves and reaches the edge. Check logs: `docker logs` on the frpc containers and `coti-<network>-full-node`.
* **Port 7400 in use** — Another process is bound to 7400; free it before re-running.
* **Dirty directory** — Installer needs an empty folder; move or remove an old `coti-full-node` clone.
