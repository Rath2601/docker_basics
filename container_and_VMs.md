# Containers & Virtualization — Personal Notes

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
