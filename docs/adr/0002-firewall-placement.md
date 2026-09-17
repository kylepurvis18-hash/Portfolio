# ADR-0002: Firewall/router runs on dedicated hardware, not virtualized on the lab host

**Status:** Proposed — pending confirmation
**Date:** 2026-09-17

## Context

VLANs need something to route between them and enforce firewall rules — the switch (TL-SG108E) can only tag/separate traffic, and the Xfinity XB8-T isn't expected to do inter-VLAN routing. That job falls to pfSense or OPNsense (both free). The question is where that firewall runs: virtualized as a VM on the existing home lab host (VMware Workstation/Player), or on separate, dedicated hardware.

Current host: single physical machine, VMware Workstation/Player, one Ubuntu+Docker VM today.

## Options considered

1. **Virtualize the firewall on the existing host (as a second VM under VMware Workstation).**
   - Pro: no new hardware, no added cost, fastest to stand up.
   - Con: VMware Workstation's bridged networking has to be configured carefully (promiscuous mode on the virtual switch, ideally a NIC dedicated to the trunk) for 802.1Q-tagged frames to reach the VM intact — it's workable, but fiddly and host/driver-dependent.
   - Con (the bigger one): the firewall's uptime becomes tied to this one physical machine. Every host reboot, VMware update, or lab experiment that requires a restart takes the *entire network's routing* down with it — including your own workstation and Wi-Fi, which have nothing to do with whatever you're testing in the lab.

2. **Dedicated hardware for the firewall** (a repurposed old PC, a mini-PC, or a low-power SFF box — used hardware in this category is typically inexpensive).
   - Pro: separates "core infrastructure that must always be up" from "the lab, which is expected to be rebuilt, broken, and experimented on." Rebooting or nuking the lab host never takes your internet down.
   - Pro: avoids the virtual-switch VLAN-tag fragility entirely — the physical NIC(s) on a dedicated box see real 802.1Q trunk traffic with no hypervisor layer in between.
   - Con: costs money and adds a physical device to manage/document.

## Decision (proposed)

Run the firewall (pfSense or OPNsense — tool choice gets its own ADR) on dedicated hardware, separate from the VMware Workstation host.

## Why

The core reasoning is the separation of concerns: the whole point of this rebuild is to be able to break, rebuild, and experiment on the lab freely. If the firewall lives on the same box as the thing being experimented on, every experiment risks taking down the network for the rest of the house — which either makes you cautious about touching the lab (defeating the purpose) or annoying to live with (defeating domestic peace). Dedicated hardware for core network infrastructure is also the standard pattern in real environments, so it's the more representative thing to document and speak to.

This is marked **Proposed** rather than **Accepted** because it has a real cost implication that's your call, not mine — confirm or override and I'll flip the status.

## Consequences

- Requires sourcing a small second machine (old PC, mini-PC, or similar) with at least one, ideally two, NICs.
- Slightly more to document (one more device in the topology) but a cleaner, more standard architecture.
- If budget or availability becomes a blocker, the fallback is Option 1 (virtualized on the VMware host) with the promiscuous-mode/dedicated-NIC caveats documented as an accepted trade-off — this ADR would then be superseded.
