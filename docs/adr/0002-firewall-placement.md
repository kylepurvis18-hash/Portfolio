# ADR-0002: Firewall/router runs on dedicated hardware, not virtualized on the lab host

**Status:** Accepted
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

## Decision

Run the firewall (pfSense or OPNsense — tool choice covered in ADR-0003) on dedicated hardware, separate from the VMware Workstation host. Hardware: a spare PC currently running Windows 10, wiped and repurposed to run the firewall OS **bare-metal** (not Windows-hosted, not virtualized) — see "Why bare-metal on this box" below.

## Why

The core reasoning is the separation of concerns: the whole point of this rebuild is to be able to break, rebuild, and experiment on the lab freely. If the firewall lives on the same box as the thing being experimented on, every experiment risks taking down the network for the rest of the house — which either makes you cautious about touching the lab (defeating the purpose) or annoying to live with (defeating domestic peace). Dedicated hardware for core network infrastructure is also the standard pattern in real environments, so it's the more representative thing to document and speak to.

A spare Windows 10 PC is available and solves the cost problem entirely — no purchase needed for the base machine (only possibly a second NIC; see `docs/network/02-firewall-build.md`).

### Why bare-metal, not "install the firewall OS in a VM on that Windows box"

Windows 10 passed end-of-support in October 2025, so leaving it installed and internet-facing is itself a liability — this is a good reason to wipe it outright, not just a convenient excuse. Running the firewall OS as a VM under Windows on this box would also just recreate the exact problem this ADR exists to avoid (a general-purpose OS's updates/reboots/quirks sitting between the network and its own routing), one layer down. Wiping it and installing the firewall OS directly on the hardware removes that layer entirely: fewer moving parts, a smaller attack surface (no Windows to patch or misconfigure), and the box's only job becomes "be the firewall."

## Consequences

- The Windows 10 install and any data on that box's drive is gone after this — confirm nothing on it needs backing up first.
- Needs at least one NIC dedicated to WAN and one to the LAN-side VLAN trunk; most single-NIC desktops need a second NIC added (USB3 Ethernet adapter or a PCIe card if there's a slot free). Finalized in `docs/network/02-firewall-build.md` once the box's specs are confirmed.
- One more physical device in the topology to document and maintain (BIOS/firmware updates, physical placement, power draw) — accepted as the cost of the separation-of-concerns argument above.
