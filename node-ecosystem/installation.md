# Installation

This section documents the **automated** installer from the [**`/setup`** wizard](ui-guide/README.md) — the `curl | sudo bash` flow. It is the destination for **"Learn more about installation"** in the wizard. See [Networks](README.md#networks) for testnet and mainnet URLs.

{% hint style="info" %}
**OS and hardware** are the same for every path — see [**Server requirements**](server-requirements.md). For **Git clone + Docker Compose** without the wizard, see [**Manual full node setup**](manual-full-node.md).
{% endhint %}

## Certified operating system and hardware

The **same** certified OS and server sizing apply to both wizard flows below and to [manual full node setup](manual-full-node.md). Full detail is on [**Server requirements**](server-requirements.md).

## Choose your installer flow

The wizard produces a one-liner that runs [`install_coti-full-node.sh`](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh). Pick the guide that matches the **flags** the wizard gives you:

| Flow | When | Guide |
| ---- | ---- | ----- |
| **COTI-managed tunnel** | COTI-assigned subdomain, **`--with-frp`**, no Nginx/Let’s Encrypt on your VM | [**Wizard tunnel (COTI subdomain)**](installation-wizard-tunnel.md) |
| **Your own domain** | Your DNS name, **`--nginx`**, Let’s Encrypt + Nginx on the host | [**Own domain (Nginx + TLS)**](installation-own-domain.md) |

A **third** path — clone the repo and operate the stack yourself — is [**Manual full node setup**](manual-full-node.md).

{% hint style="danger" %}
Piping `curl` into `sudo bash` runs a remote script as **root**. Only use commands from the official wizard, served from `https://fullnode.testnet.coti.io` or `https://fullnode.mainnet.coti.io`. When in doubt, review the script in the [`coti-full-node`](https://github.com/coti-io/coti-full-node) repository.
{% endhint %}

## After any wizard install

* The node syncs from peers; the browser wizard polls peer discovery until your node appears.
* You enter the **warm-up** window before the node becomes **hot** and an NFT is minted — see the [Glossary](ui-guide/glossary.md).

For **restart / stop / logs** when you manage the repo yourself, see [**Manual full node setup → Restarting your node**](manual-full-node.md#restarting-your-node).

## Optional flags (overview)

Both subpages document their main flags in full. Quick reference:

| Flag | Typical use |
|------|-------------|
| **`--with-frp`** | Tunnel flow — see [Wizard tunnel](installation-wizard-tunnel.md). |
| **`--nginx`** | Own domain + TLS on host — see [Own domain](installation-own-domain.md). |
| `--no-nginx` | Skip Nginx (advanced; often not reward-suitable with a BYO domain). |
| `--frpc` / `--no-frpc` | FRPC without tunnel relaxations — see tunnel page. |
| `--staging` | Let’s Encrypt staging — only with `--nginx`. |
