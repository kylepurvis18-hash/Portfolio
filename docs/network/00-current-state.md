# Current State (baseline, before rebuild)

Captured 2026-09-17, before any network or lab changes. This is the "before" picture — nothing here has VLANs, and the network is flat (single broadcast domain).

## Topology

```
Internet
   │
   ▼
Xfinity Gateway (XB8-T)         ← ISP-provided modem/router/Wi-Fi combo unit
   │
   ▼
TP-Link TL-SG108E                ← 8-port "smart" managed switch, 802.1Q VLAN capable
   │
   ├──► TP-Link Deco 6E          ← Wi-Fi mesh AP (wired uplink from switch)
   ├──► Workstation (this PC)    ← wired
   └──► Home lab host            ← wired; single physical machine
             │
             └─ Type-2 hypervisor
                   └─ Ubuntu Linux VM
                         └─ Docker (installed, running)
```

## Notes on each component

- **Xfinity XB8-T** — combined modem + router + Wi-Fi. Acting as the network's only router/DHCP server/firewall today. Whether it can be put into bridge mode (so a downstream device becomes the "real" router) is an open question for Phase 1 — this matters a lot for whether VLANs can actually be enforced, not just tagged.
- **TP-Link TL-SG108E** — an "easy smart" managed switch. Supports port-based and 802.1Q VLAN tagging, which is exactly what's needed to carry multiple VLANs on the wired side. It is **not** a router — it can separate traffic into VLANs but cannot route between them or apply firewall rules between them. That job has to live somewhere else (Phase 2).
- **TP-Link Deco 6E** — consumer mesh Wi-Fi. Consumer mesh systems generally do not support mapping individual SSIDs to individual VLANs the way a business-grade AP (UniFi, Aruba, Omada, etc.) does. This is flagged as a likely constraint to confirm in Phase 1 — it may mean wireless clients stay on a single VLAN for now, with wired VLAN segmentation coming first.
- **Home lab host** — single physical machine, type-2 hypervisor (e.g., VirtualBox / VMware Workstation), currently one Ubuntu Linux VM with Docker.

## Known gaps going into Phase 1

1. No device on the network currently does inter-VLAN routing + firewalling between segments. The Xfinity gateway is not expected to support this.
2. Wi-Fi VLAN tagging support on the Deco 6E is unconfirmed and likely unsupported.
3. No logging or monitoring exists anywhere on the network today.
