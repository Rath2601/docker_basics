# Virtualization — Compact Reference
> **Core idea:** Software layer abstracts physical hardware → run multiple isolated VMs on one machine
---
## Core Concepts
- **Abstraction** → hiding physical hardware behind virtual interfaces (CPU → vCPU | RAM → vRAM | Disk → virtual disk)
- **Partitioning** → dividing resources among VMs
- **Isolation** → ensuring VMs don't interfere (Security boundary - this is huge in multi-tenant clouds)
- **Encapsulation** → packaging VM as portable files (pre-configured OS disk image + software config | easy to port)

### Vendor features
- **Live Migration** → Move a running VM without downtime (Needs shared storage or replication)
- **Snapshotting**  → Freeze VM state and roll back later
- **High Availability (HA)** → If host dies → VM restarts elsewhere.
- **Distributed Resource Scheduling (DRS)** → Auto-balances VMs across hosts.
---
## Why It Exists
- **Underutilization** → pool resources across workloads
- **Manual config errors** → template (pre-configured OS disk image + software config) + clone infrastructure consistently
- **Slow DR** → snapshot VMs, restore in minutes (not hours)
- **Cost** → fewer physical servers, less power/cooling
---
## Types + Real-World Mapping
| Type | What It Does | Used As |
|---|---|---|
| **Server** | Multiple VMs on one physical server | AWS EC2, Azure VMs, GCE |
| **Network** | Abstracts switches/routers into software overlays | AWS VPC, VMware NSX, Azure VNet |
| **Storage** | Pools physical disks into one virtual resource | AWS EBS/EFS, NetApp ONTAP |
| **Desktop (VDI)** | Centralized desktops streamed to endpoints | Citrix DaaS, VMware Horizon, AWS WorkSpaces |
| **Application** | Packages app + deps, streams without local install | Microsoft App-V, Citrix Virtual Apps |
| **Data** | Unified query layer over siloed data sources | Denodo, AWS Glue, Databricks federation |
---
## Hypervisors (VMM)
```
Hypervisor
├── Type 1 — Bare-Metal (on hardware, no host OS)
│   ├── Performance: near-native, direct hardware access
│   ├── Use case: production, enterprise, cloud infra
│   └── Examples: VMware ESXi · Hyper-V · KVM · Xen (AWS)
│
└── Type 2 — Hosted (on top of host OS)
    ├── Performance: lower (host OS overhead)
    ├── Use case: dev workstations, testing, labs
    └── Examples: VirtualBox · VMware Workstation · Parallels
```
---
## Hyper-V (Windows)
**Normal flow:**
```
Apps → OS → Hardware
```
**Hyper-V enabled:**
```
(Apps → OS)  ← root partition
      ↓
   Hyper-V
      ↓
  Hardware
```
> Hyper-V turns **your own Windows** into the main (root) VM → VMs you create become **child VMs**
---
## VMs vs. Containers vs. WSL 2
| | **Container (Docker)** | **VM — Type 1** | **VM — Type 2** | **WSL 2** |
|---|---|---|---|---|
| **Size** | MBs (no OS) | GBs (full OS) | GBs (full OS) | Minimal |
| **Startup** | Seconds | Minutes | Minutes | Seconds |
| **Isolation** | Process-level (shared kernel) | Strong (HW-enforced) | Strong (OS-enforced) | Dev tool — not a security boundary |
| **OS flexibility** | Same kernel only | Any OS per VM | Limited by host OS | Linux on Windows only |
| **Security** | Weaker (shared kernel) | Highest | High | Not for prod |
| **Use case** | Microservices, CI/CD, cloud-native | Multi-tenant prod workloads | Multi-OS dev/test | Linux toolchain on Windows |
| **Orchestration** | Kubernetes, ECS, Docker Swarm | vSphere, OpenStack | VirtualBox GUI, Workstation | Native Windows terminal |

> **Production reality:** Containers run *inside* VMs — combines hypervisor isolation with container agility. Standard on AWS EKS, GKE, AKS.
---
## Decision Guide
| Scenario | Use |
|---|---|
| Cloud prod workload | Type 1 VM (EC2, Azure VM) |
| Microservices / cloud-native (software systems that fully takes advantage of cloud computing environments)| Docker + Kubernetes |
| Multi-OS testing locally | Type 2 VM (VirtualBox, VMware Workstation) |
| Linux dev tools on Windows | WSL 2 |
| Remote desktops at scale | VDI (Citrix, Horizon, WorkSpaces) |
| Enterprise app packaging | App-V / Citrix Virtual Apps |
| Cross-source analytics | Data virtualization (Denodo, Glue) |
---

## Containers & Virtualization — Personal Notes
---
## Why Containers?
- Isolated and Self-contained
- No need to download/install → just run
- Easy version switching
- Easy to port across machines
---
## OS Architecture
```
Applications
    ↓
OS (user space: shell, libs, tools)
    ↓
Kernel  ← CPU scheduling, memory mgmt, hardware control
    ↓
Hardware
```
> **OS** = Kernel + user-space tools (shell, GUI)
> **All Linux distros share the same kernel**
---
## WSL1 vs WSL2
| Feature | WSL1 — The Translator | WSL2 — The Secret VM |
|---|---|---|
| **Kernel** | Shared Windows Kernel | Real Linux Kernel |
| **Architecture** | Translation layer (Linux syscalls → Windows) | Hyper-V lightweight "Utility VM" |
| **Performance** | Faster for Windows files | Much faster for Linux files |
| **Compatibility** | Limited (missing some syscalls) | 100% (it's real Linux) |

> **WSL2 sits directly on Hyper-V** → disable Hyper-V in Windows Features = WSL2 stops working
---
## WSL2 vs Hyper-V VM
| Feature | WSL2 | Hyper-V VM |
|---|---|---|
| **Boot time** | Near instant | 30+ seconds |
| **RAM** | Dynamic (grows/shrinks) | Static (reserved upfront) |
| **Kernel** | Microsoft-tuned Linux kernel | Any Linux kernel you provide |
| **Isolation** | Low (shares files/network) | High (fully boxed off) |
| **Snapshots** | ✗ Not supported | ✓ Save state anytime |
---
## Creating VMs & Containers — Per OS
### Windows
```
VMs
├── Type 1 → Hyper-V (Windows runs as root partition)
└── Type 2 → VMware Workstation, VirtualBox (may depend on Hyper-V if enabled)
Containers
└── Docker Desktop → WSL2 (Linux VM) → Hyper-V → Hardware
Windows (WSL2)
  Hypervisor → Windows + Linux VM (side by side, deeply integrated) → Containers
```
### macOS
```
VMs
└── Type 2 → Parallels · VMware Fusion · UTM · VirtualBox
             (all use Apple Virtualization Framework internally)
Containers
└── Docker Desktop → Linux VM → macOS Virtualization Framework → Hardware
```
### Linux
```
VMs
└── KVM (built into kernel → Linux behaves like Type 1 hypervisor)
Containers
└── Docker → directly on host (namespaces + cgroups) → no VM required
```
---
