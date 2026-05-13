# Server requirements (wizard and manual)

This page lists the **certified operating system**, **tested software stack**, and **hardware sizing** that apply to **both**:

* the [web app wizard / Installation](installation.md) flow (`curl | sudo bash` from the official installer host), and
* [manual full node setup](manual-full-node.md) (Git clone, Docker Compose, and scripts on your own).

Path-specific steps (for example DNS, ports 80/443 for Let’s Encrypt when using the default installer) stay in [Installation](installation.md) or [manual full node setup](manual-full-node.md).

## Certified operating system

The COTI full node stack is **certified on Ubuntu 24.04 LTS**. It is the only operating system COTI officially tests end-to-end.

* **Wizard installer:** other Debian-family distributions *may* run the script — the installer will warn and prompt for confirmation — but COTI does **not** guarantee compatibility and does **not** support those targets. When in doubt, deploy a fresh **Ubuntu 24.04 LTS** server.
* **Manual path:** use **Ubuntu 24.04.x** to stay aligned with what COTI certifies.

## Software stack

These versions reflect **what we have certified and tested at the time this page was written**. Newer **patch and minor** releases of the same software (Ubuntu LTS point releases, Docker Engine, Compose) **usually work** without changes; if you use a newer stack, validate on **Testnet** before relying on it for **Mainnet**.

* **Docker:** version **28.0.1** (tested)
* **Docker Compose:** version **2.29.1** (tested)

See also Docker’s [**Linux system requirements**](https://docs.docker.com/desktop/setup/install/linux/#general-system-requirements) for kernel, cgroup, and storage-driver expectations on Ubuntu.

## Hardware

CPU and memory targets are the **same on Testnet and Mainnet**. **Disk size depends on the network** — Mainnet chain data and traffic require substantially more storage than Testnet.

| Specification    | Minimum | Recommended | Professional |
| ---------------- | ------- | ----------- | ------------ |
| **vCPUs**        | 2       | 4           | 8            |
| **Memory (GiB)** | 8       | 16          | 64           |

### Storage by network

Size the disk for the **network you join** (Testnet vs Mainnet). Figures are planning guides; leave headroom for chain growth, snapshots, and logs.

| Network     | Minimum | Recommended | Professional (extra headroom) |
| ----------- | ------- | ----------- | ----------------------------- |
| **Testnet** | 100 GB  | 200 GB      | 500 GB                        |
| **Mainnet** | 700 GB  | 1 TB        | 1.5 TB                        |

These bands align with the [**Networks**](README.md#networks) table (≥ 100 GB Testnet, ≥ 700 GB Mainnet minimum for ecosystem guidance).

The **wizard installer** checks that the **current working directory’s partition** has enough free space for the selected network before cloning — use the **Minimum** column above as the floor you must have **free** at install time.

In addition to the above, a **reliable, high-bandwidth internet connection** is recommended.

### Recommended minimum hosted configuration

* AWS: **m7a.large** (2 vCPUs, 8 GiB memory)
* OVH: **b2-15** (4 vCPUs, 15 GiB memory)

_**Disclaimer:** The above configuration has been certified on **Testnet**. **Mainnet** typically needs **larger disk** (see [Storage by network](#storage-by-network)); higher transaction volumes on Mainnet may also require increased CPU and memory._

### Recommended optimal hosted configuration

* AWS: **r5n.2xlarge** (8 vCPUs, 64 GiB memory)
* OVH: **r2-120** (8 vCPUs, 120 GiB memory)

_**Disclaimer:** Size attached volumes using [Storage by network](#storage-by-network) for Testnet vs Mainnet; these instance types emphasize CPU and RAM._

## Related documentation

* [Installation](installation.md) — wizard prerequisites (FQDN, ports 80/443/7400, one-line command, what the installer does).
* [Manual full node setup](manual-full-node.md) — Git clone, Compose, open ports table, restart/stop, FAQ.
