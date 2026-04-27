# Virtualization — Compact Reference

> **Core idea:** Software layer abstracts physical hardware → run multiple isolated VMs on one machine

---

## Why It Exists
- **Underutilization** → pool resources across workloads
- **Manual config errors** → template + clone infrastructure consistently
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

## Core Concepts (3-word definitions)

- **Partitioning** → split one resource across many VMs
- **Isolation** → VMs/containers can't touch each other
- **Encapsulation** → entire VM packed into portable file (`.vmdk`, `.ova`, container image)
- **Live Migration** → move running VM between hosts, zero downtime (VMware vMotion)
- **Snapshotting** → capture VM state → instant rollback for DR/patch testing

---

## Decision Guide

| Scenario | Use |
|---|---|
| Cloud prod workload | Type 1 VM (EC2, Azure VM) |
| Microservices / cloud-native | Docker + Kubernetes |
| Multi-OS testing locally | Type 2 VM (VirtualBox, VMware Workstation) |
| Linux dev tools on Windows | WSL 2 |
| Remote desktops at scale | VDI (Citrix, Horizon, WorkSpaces) |
| Enterprise app packaging | App-V / Citrix Virtual Apps |
| Cross-source analytics | Data virtualization (Denodo, Glue) |

---
