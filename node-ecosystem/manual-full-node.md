# Manual full node setup (without the wizard)

← [**Installation**](installation.md) (this page is the last subpage under Installation)

This page is the **operator-managed** path: you install and run a COTI full node using the [`coti-full-node`](https://github.com/coti-io/coti-full-node) repository and Docker on your own server — **not** through the Nodes web app wizard, one-liner installer host, or guided spin-up UI.

> **New to running a node?** Use the **wizard** first — [**Installation**](installation.md) and [**UI guide**](ui-guide/README.md). It is the quickest path for most people; return here only if you intentionally skip the web app.

{% hint style="success" %}
**Web app wizard (recommended for most operators):** follow [**Installation**](installation.md), [**Server requirements**](server-requirements.md) (OS and sizing), and the [**UI guide**](ui-guide/README.md). You get the guided flow, installer from the [Networks](./#networks) table, HTTPS, and monitoring hooks from the product.

**This page (manual path):** you clone the repo, configure the host, and run `start` / `stop` scripts yourself. Reward **eligibility is the same** as the wizard path when your node satisfies ecosystem rules (FQDN, JSON-RPC reachability, license / holdings, uptime thresholds, etc.) — see [**Node Ecosystem overview**](./) and [**Installation**](installation.md) for authoritative requirements.
{% endhint %}

For **what a COTI node is** and **why operators run one**, see [**What is a COTI node?**](./#what-is-a-coti-node) and [**Why run a node?**](./#why-run-a-node).

***

### Requirements

COTI full node software is published as a Docker image.

{% hint style="info" %}
**Disclaimer:** Successfully operating, troubleshooting, and maintaining a node requires technical proficiency. Familiarity with tools such as Linux, Docker, and Git is assumed. Users not familiar with this technology stack should consider the [**Installation**](installation.md) / [**UI guide**](ui-guide/README.md) flow or assigning their license to an existing operator.
{% endhint %}

**Certified operating system, tested Docker/Compose versions, and hardware** (CPU, memory, disk by network, example cloud SKUs) are **identical** for the [wizard](installation.md) and this manual path — see [**Server requirements**](server-requirements.md).

***

### Network Configuration

#### Open Ports: Protocol and Purpose

You should open the following ports in your host firewall (e.g., UFW) **and** in your cloud provider’s security groups to permit inbound traffic.\
Be aware that **different ports use different protocols (TCP/UDP)** depending on their purpose.

<table><thead><tr><th width="80">Port</th><th width="104.5">Protocol</th><th width="275">Purpose</th><th>Notes</th></tr></thead><tbody><tr><td>7400</td><td>TCP</td><td>Peer-to-Peer (P2P) Communication</td><td>Data layer used for establishing a connection, exchanging blocks, and synchronizing blockchain data with other nodes.</td></tr><tr><td>7400</td><td>UDP</td><td>Node Discovery (Discv4/Discv5)</td><td>Discovery layer used to quickly find the addresses of other nodes on the network, including the bootnodes and all other peers.</td></tr><tr><td>8545</td><td>TCP</td><td>HTTP-RPC API</td><td>Used for external applications to query chain data and submit transactions over HTTP.</td></tr><tr><td>8546</td><td>TCP</td><td>WebSocket-RPC API</td><td>Used for real-time communication, allowing external applications to receive live updates and subscribe to blockchain events.<br></td></tr></tbody></table>

* **Static IP**: Required to ensure stable RPC access, enabling continuous health monitoring.

***

### Setting up Your Node Environment

COTI full nodes are run using docker. Docker provides a way for everyone to run battle-tested, reliable images, known to work with the network.

#### Prerequisites

<table data-header-hidden><thead><tr><th width="257.44818115234375"></th><th></th></tr></thead><tbody><tr><td><ol><li>Git</li></ol></td><td>See <a href="https://git-scm.com/book/en/v2/Getting-Started-Installing-Git"><strong>git-scm.com/book/en/v2/Getting-Started-Installing-Git</strong></a></td></tr><tr><td><ol start="2"><li>Docker</li></ol></td><td>See <a href="https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository"><strong>docs.docker.com/engine/install/ubuntu</strong></a></td></tr><tr><td><ol start="3"><li>Docker Compose</li></ol></td><td>See <a href="https://docs.docker.com/compose/install/standalone/"><strong>docs.docker.com/compose/install/standalone</strong></a></td></tr></tbody></table>

Target the **Docker Engine** and **Compose** versions listed under [**Server requirements → Software stack**](server-requirements.md#software-stack) so your stack matches what COTI certifies.

### Installation Steps

{% hint style="info" %}
The following recommended steps reflect best practices but should be performed carefully, as they may significantly impact the operating system.
{% endhint %}

1. **Recommended steps:**
   1.  Set host name (where `<name>`is your chosen node name)

       \{% code fullWidth="false" %\}

       ```bash
       sudo hostnamectl set-hostname <name>-full-node
       ```

       \{% endcode %\}
   2.  Update package lists

       \{% code fullWidth="false" %\}

       ```bash
       sudo apt update
       sudo apt upgrade
       ```

       \{% endcode %\}
   3.  Reboot system

       \{% code fullWidth="false" %\}

       ```bash
       sudo reboot
       ```

       \{% endcode %\}
   4.  Update OS

       \{% code fullWidth="false" %\}

       ```bash
       sudo do-release-upgrade
       ```

       \{% endcode %\}
2. **Configure Docker**
   *   Add your user to the `docker` group:

       \{% code fullWidth="false" %\}

       ```bash
       sudo usermod -aG docker $( whoami )
       ```

       \{% endcode %\}
   *   Logout and re-login

       \{% code fullWidth="false" %\}

       ```bash
       logout
       ```

       \{% endcode %\}
3.  **Clone the COTI Full Node project**

    \{% code fullWidth="false" %\}

    ```bash
    git clone https://github.com/coti-io/coti-full-node.git
    ```

    \{% endcode %\}
4.  **Checkout the stable release tag (Recommended):**

    * NOTE: Replace `v1.1.4-mainnet` with the actual latest stable tag (e.g., `v1.1.4-discovery` for Testnet).

    ```bash
    git checkout tags/<latest_stable_tag>
    # Example for Mainnet: git checkout tags/v1.1.4-mainnet
    ```
5. **Start Your Node**
   1.  Navigate to the newly created "coti-full-node" directory

       \{% code fullWidth="false" %\}

       ```bash
       cd coti-full-node
       ```

       \{% endcode %\}
   2.  Execute node start script

       ```
       ./start_coti-full-node.sh
       ```
   3.  Once the docker-compose has started the node, liveliness check will be executed

       ```
       ./liveness_coti-full-node.sh
       ```

       \
       Output example:

       ```
       Initial block number: 208539
       Check 1: Block number is 208540
       Block number has progressed. Node is syncing.
       ```

{% hint style="info" %}
If liveliness check passed locally it means that your node is syncing with the other nodes in the network.
{% endhint %}

5.  **To Check Node Logs**

    ```
    docker logs -f coti-<network>-full-node
    ```

### Restarting Your Node

To restart your node follow these steps:

1.  Stop your node

    \{% code fullWidth="false" %\}

    ```bash
    ./stop_coti-full-node.sh
    ```

    \{% endcode %\}
2.  Start your node

    \{% code fullWidth="false" %\}

    ```bash
    ./start_coti-full-node.sh
    ```

    \{% endcode %\}

### Node Configuration

If you are running a node without a license, no further configuration of the node is required. Simply ensure you are connected to the network.

For **your own domain** with **HTTPS on the host** (what the wizard does with **`--with-nginx`**), set at least:

```bash
FULLNODE_FQDN=node1.example.com
NGINX_ENABLED=true
FRPC_ENABLED=false
```

in **`.env`** (see [`.env.example`](https://github.com/coti-io/coti-full-node/blob/main/.env.example)). Do **not** enable host Nginx and **FRPC** on the same install — the automated installer rejects that combination. For a **COTI-managed tunnel** instead, see [**Wizard tunnel**](installation-wizard-tunnel.md) (`--with-frp`); skip this section.

### Nginx and Let's Encrypt (HTTPS on your domain)

Use this when the ecosystem must reach your node at **`https://<your-fqdn>/rpc`** (own DNS + TLS on your server). The wizard path is documented in [**Own domain (Nginx + TLS)**](installation-own-domain.md); here you perform the same steps by hand after cloning the repo.

**Authoritative command-line reference:** [`install_coti-full-node.sh`](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh) — search for **`SSL AND NGINX SETUP`** ([lines 534–630](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh#L534-L630)). The examples below mirror that script; substitute your **FQDN** and match **upstream** service names to your checked-out `docker-compose.yml` (e.g. `coti-testnet-full-node` on testnet).

#### Prerequisites

1. **DNS** — an **A record** for your FQDN pointing at this server’s public IP, propagated **before** you request a certificate (see [**Own domain**](installation-own-domain.md)).
2. **Ports** — **80** and **443** free on the host and allowed through firewall / cloud security groups (in addition to **7400** from [Network configuration](#network-configuration)).
3. **Certbot on the host** — the installer installs it with `apt` when Nginx is enabled:

   ```bash
   sudo apt update
   sudo apt install -y certbot
   ```

4. **`.env`** — `FULLNODE_FQDN`, `NGINX_ENABLED=true`, `FRPC_ENABLED=false` (see [Node configuration](#node-configuration) above).

Run these steps from the **`coti-full-node`** clone root. Prefer doing them **before** your first `./start_coti-full-node.sh` with `NGINX_ENABLED=true`, or stop the stack (`./stop_coti-full-node.sh`), configure TLS, then start again.

#### Step 1 — Prepare Nginx directories

```bash
mkdir -p ./nginx/certbot ./nginx/sites-enabled
```

The repo already ships `nginx/nginx.conf`, `nginx/default.conf`, and `nginx/nginx-init_default.conf` for the ACME-only container.

#### Step 2 — Temporary Nginx for the ACME HTTP-01 challenge

Start the **`setup`** profile container (listens on host port **80** only):

```bash
docker compose --profile setup up -d nginx-init
# If you use the standalone compose binary:
# docker-compose --profile setup up -d nginx-init
```

This matches the installer’s `$DC --profile setup up -d nginx-init`.

#### Step 3 — Obtain a certificate with Certbot (webroot)

Production certificate:

```bash
certbot certonly --webroot \
  -w "$(pwd)/nginx/certbot" \
  -d "$FULLNODE_FQDN" \
  --register-unsafely-without-email \
  --agree-tos \
  --non-interactive
```

**Dry run** (Let's Encrypt staging — browsers will not trust the cert; same as installer flag **`--staging`**):

```bash
certbot certonly --webroot \
  -w "$(pwd)/nginx/certbot" \
  -d "$FULLNODE_FQDN" \
  --staging \
  --register-unsafely-without-email \
  --agree-tos \
  --non-interactive
```

Certificates are written under **`/etc/letsencrypt/live/<fqdn>/`** on the host. The main Nginx container mounts **`/etc/letsencrypt`** read-only (see `docker-compose.yml`).

#### Step 4 — Stop the temporary Nginx

```bash
docker compose --profile setup down
```

Port **80** must be free for the production Nginx container.

#### Step 5 — Write the TLS reverse-proxy config

Create **`./nginx/sites-enabled/fullnode.conf`** with the same structure the installer writes: HTTPS on **443** proxying **`/rpc`**, **`/ws`**, **`/metrics`**, and **`/operator/`** to the Docker services, plus HTTP on **80** for `/.well-known/acme-challenge/` and redirect to HTTPS.

Copy the `server { ... }` blocks from [`install_coti-full-node.sh` (lines 559–629)](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh#L559-L629), replacing:

* `$FQDN` with your hostname (e.g. `node1.example.com`);
* `$NETWORK` in upstream names with your network label if your compose file uses a different prefix (must match service names in `docker-compose.yml`, e.g. `coti-testnet-full-node:8545`).

Public RPC for monitoring and rewards: **`https://<your-fqdn>/rpc`**.

#### Step 6 — Start the stack with Nginx enabled

Ensure **`NGINX_ENABLED=true`** in **`.env`**, then:

```bash
./start_coti-full-node.sh
```

`start_coti-full-node.sh` adds **`--profile proxy-nginx`** when `NGINX_ENABLED` is true, which starts the **`nginx`** service on ports **80** and **443**.

#### Renewal

Schedule renewal on the host (example monthly cron). Use the same webroot path as issuance:

```bash
certbot renew --webroot -w "$(pwd)/nginx/certbot"
docker compose exec nginx nginx -s reload
```

Adjust the reload command if your Nginx container name differs.

#### Troubleshooting (Nginx / TLS)

* **Certbot failed** — confirm `dig <fqdn>` resolves to this server; wait for DNS; ensure port **80** is reachable from the internet while `nginx-init` is up.
* **Port in use** — stop other services on **80** / **443** (including a leftover `nginx-init` after a failed run).
* **502 / bad gateway** — upstream names in `fullnode.conf` must match running compose service names; ensure the full node container is healthy (`docker ps`, `docker logs`).

If you prefer not to maintain this by hand, use the [**own-domain wizard one-liner**](installation-own-domain.md) or run the installer script from the repo with **`--with-nginx`** after reviewing [`install_coti-full-node.sh`](https://github.com/coti-io/coti-full-node/blob/main/install_coti-full-node.sh).

### Verifying Node Functionality

* Metrics: Visit [**uptime.coti.io**](https://uptime.coti.io) to track performance and status.

{% hint style="info" %}
Public status pages are available for both networks — use the URLs listed in [Networks](./#networks).
{% endhint %}

* Node availability is crucial for the smooth operation of the network.\
  \
  To evaluate node availability, COTI leverages a monitoring platform that publishes this data. A node is considered available if it successfully responds to the `eth_blockNumber` request. Using this request ensures the node is actively synchronized with the network and functioning correctly.

### Incentives

Validation rewards are governed by the [**Node Ecosystem**](./) program (uptime, FQDN reachability, license and holdings rules, epoch cadence, and other thresholds). Exact requirements can change — use the ecosystem documentation, web app, and the **Node Economy** section of the [litepaper](coti-node-ecosystem-litepaper.md) as the source of truth.

Licensed full nodes have commonly been expected to sustain **high uptime** (for example **≥ 98%** over an epoch of roughly **103 hours**) to remain eligible for validation rewards; confirm the current bar in the Node Ecosystem pages.

Following **this manual guide** instead of the ecosystem installer **does not change** those rules: you can still earn rewards when your deployment meets the **same** eligibility conditions the ecosystem enforces.

### Maintenance & Monitoring

1. **Regular Updates**: Keep your node software updated to the latest version. This ensures you receive security patches and new features.
2. **Resource Usage**: Monitor CPU, RAM, and disk space to ensure uninterrupted operation.
3. **Uptime**: Use a process manager (like `systemd`) or Docker auto-restart policies to keep your node running if it crashes unexpectedly.

### Troubleshooting

#### Common Issues:

* Peer Connection Errors: Ensure your ports are open and your firewall allows inbound connections.
* High Resource Usage: Upgrade your hardware or adjust configuration settings to reduce overhead.

Where to Get Help:

* [**COTI Discord**](https://discord.coti.io/)
* [**COTI Support**](mailto:support@coti.io)

### FAQ

1. How many nodes can I run?
   1. There’s no set limit, but each node requires its own resources. Running multiple nodes can help decentralize the network but comes with higher operational costs.
2. Can I run a node on a VPS or cloud platform?
   1. Absolutely. Just ensure the service meets the hardware, OS, and networking requirements.
3. Do I earn more rewards by running a more powerful node?
   1. Generally, consistently high uptime can lead to more consistent rewards, however, the only measure to qualify for rewards is uptime.
4. Is it mandatory to purchase a node license to run a node?
   1. No. A node license simply allows you to earn rewards for helping decentralize the network, however, it is not necessary to run a COTI node.

### Next Steps

Congratulations on setting up your COTI node using the **manual** path. Reward eligibility still follows the **Node Ecosystem** rules linked below.

The following related sections may be helpful:

* [coti-node-ecosystem-litepaper.md](coti-node-ecosystem-litepaper.md "mention")
* [**Node Ecosystem overview**](./) — eligibility, thresholds, and the managed experience at [testnet.nodes.coti.io](https://testnet.nodes.coti.io) / [nodes.coti.io](https://nodes.coti.io), including the guided [installer](installation.md), [UI walkthrough](ui-guide/README.md), and [glossary](ui-guide/glossary.md).

{% hint style="warning" %}
**Rewards require a valid FQDN and reachable JSON-RPC.** The Node Ecosystem measures uptime by calling your node’s JSON-RPC through the domain you register. A node that syncs locally but is **not** publicly reachable on a valid FQDN will **not** accrue credited uptime and will **not** be eligible for rewards — whether you installed via this manual guide or via the web app wizard. See [Installation](installation.md).
{% endhint %}
