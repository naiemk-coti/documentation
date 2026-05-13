# Own domain (Nginx + Let’s Encrypt + `--nginx`)

Use this flow when you **own a DNS name** and want **HTTPS on your server** via **Nginx** and **Let’s Encrypt**. The wizard’s command includes **`--nginx`**. In **`/setup`**, on **Setup FQDN**, choose **Bring your own FQDN**, enter your hostname in **Node FQDN**, configure **A or CNAME** at your provider as the in-wizard notice describes, then confirm with **I have completed my FQDN settings** before **Next**.

← Back to [**Installation overview**](installation.md) · Related: [**Wizard tunnel**](installation-wizard-tunnel.md) · [**Manual full node setup**](manual-full-node.md)

## Prerequisites

1. **Environment** meeting [**Server requirements**](server-requirements.md) (certified **Ubuntu 24.04 LTS** on Linux, or **Windows 11** + **WSL 2** + **Ubuntu 26.04 LTS**), with **root access**.
2. **Ports 80 and 443** free on the host (ACME HTTP-01 + HTTPS).
3. **Port 7400 (TCP + UDP)** free and **allowed** through host firewall and cloud security groups — see [**Manual full node setup → Network configuration**](manual-full-node.md#network-configuration) for the port table.
4. **FQDN** (e.g. `node1.example.com`) with an **A record** to your server’s public IP, propagated **before** install.
5. **Node private key** (64 hex) from the wizard or your own.

{% hint style="warning" %}
**FQDN is a reward prerequisite.** The installer obtains a certificate for that name; the ecosystem probes `https://<fqdn>/rpc` for uptime. Misconfigured DNS or blocked 80/443 prevents rewards. See [Glossary](ui-guide/glossary.md).
{% endhint %}

## One-line command

```bash
curl -sL https://fullnode.<network>.coti.io | sudo bash -s -- "<PRIVATE_KEY>" "<FQDN>" --nginx
```

`<network>` is `mainnet` or `testnet`.

## What the installer does (this flow)

1. **OS and inputs** — Certified Ubuntu version check, root, valid key and FQDN (non-24.04 may prompt; see [**Server requirements → Windows 11 with WSL 2**](server-requirements.md#windows-11-with-wsl-2)).
2. **Pre-checks** — Disk space; ports **80**, **443**, and **7400** free; `ufw` / `iptables` must not block them when those checks apply.
3. **Packages** — Docker, Compose, **`certbot`**, plus `curl`, `git`, `jq`, `dnsutils`.
4. **Clone** — `coti-full-node` into an empty directory.
5. **Config** — `.env`, `nodekey` (FRPC off unless you add other flags).
6. **HTTPS** — Temporary Nginx on :80, **Certbot** for your FQDN, then full Nginx config for `/rpc`, `/ws`, `/metrics` with TLS.
7. **Launch** — `./start_coti-full-node.sh` starts the stack.

Public RPC is **`https://<your-fqdn>/rpc`** — that is what monitoring uses.

## After the command finishes

The script prints success with your HTTPS URL. The node syncs; the wizard waits on peer discovery. Warm-up / hot / NFT: [Glossary](ui-guide/glossary.md).

## Flags relevant to this flow

| Flag | Purpose |
|------|---------|
| **`--nginx`** | Nginx + Let’s Encrypt on the host (this guide). |
| `--staging` | Let’s Encrypt **staging** CA (for dry runs; browsers won’t trust the cert). |
| `--without-nginx` | Skip TLS on host — usually **not** suitable for reward eligibility with a BYO domain. |
| `--with-frp` | If you meant the COTI tunnel path instead, see [**Wizard tunnel**](installation-wizard-tunnel.md). |

**Dry-run example:**

```bash
curl -sL https://fullnode.mainnet.coti.io | sudo bash -s -- "0x..." "node1.example.com" --nginx --staging
```

## Troubleshooting

* **Certbot failed** — Check `dig <fqdn>`, wait for DNS, confirm **80/443** reachable from the internet.
* **Port in use** — Free 80, 443, or 7400 (old Nginx, Apache, another COTI install).
* **Wizard does not see the node** — `docker ps`, `docker logs -f coti-<network>-full-node`, confirm FQDN **A** record matches the server’s public IP.
