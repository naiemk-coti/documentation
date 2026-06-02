# Server requirements (wizard and manual)

This page lists the **certified operating system**, **tested software stack**, and **hardware sizing** that apply to **both**:

* the **wizard** one-liner from [**Installation**](installation.md) (`curl | sudo bash` from the official installer host), and
* **self-managed** install from [**Manual full node setup**](manual-full-node.md) (Git clone, Docker Compose, and scripts on your own) — also under Installation.

Path-specific steps (DNS, ports, tunnel vs Nginx) live on the [**Installation**](installation.md) subpages linked above.

## Certified operating system

### Linux servers (primary)

The COTI full node stack is **certified on Ubuntu 24.04 LTS** for normal **Linux** deployments (cloud VMs, bare metal, on-prem). This is the primary baseline COTI tests end-to-end.

* **Wizard installer:** other Debian-family distributions *may* run the script — the installer will warn and prompt for confirmation — but COTI does **not** guarantee compatibility and does **not** support those targets. When in doubt, deploy a fresh **Ubuntu 24.04 LTS** server.
* **Manual path:** use **Ubuntu 24.04.x** to stay aligned with this baseline.

### Windows 11 with WSL 2

Installation is **also certified** on **Microsoft Windows 11** when you use **WSL 2** and an **Ubuntu 24.04 LTS** distribution as the WSL guest. Run Docker Engine and the node stack **inside** that Ubuntu environment (same expectations as on a native Linux host: root-capable install, disk and RAM for the network you join, and a supported Docker setup for WSL). Follow current **Microsoft WSL** and **Docker** documentation for kernel, storage, and networking limits on Windows hosts.

The one-liner installer is designed for **Ubuntu 24.04 LTS** and should run without the unsupported-OS confirmation prompt on this certified WSL setup.

Other Windows versions, WSL 1, or non-certified distros under WSL are **not** treated as supported targets unless listed here.

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

### Recommended optimal hosted configuration

* AWS: **r5n.2xlarge** (8 vCPUs, 64 GiB memory)
* OVH: **r2-120** (8 vCPUs, 120 GiB memory)

## Related documentation

* [**Installation**](installation.md) — hub; subpages [**Wizard tunnel**](installation-wizard-tunnel.md), [**Own domain**](installation-own-domain.md), [**Manual full node setup**](manual-full-node.md).
