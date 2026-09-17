# Firewall Build Plan (Phase 2)

Status: planned, not yet executed. This is the step-by-step for turning the spare Windows 10 desktop into the OPNsense firewall. Assumes ADR-0002 (Accepted) and ADR-0003 (Proposed — OPNsense).

## Hardware check

OPNsense's own minimum spec is a 1 GHz dual-core CPU, 3 GB RAM, and 4 GB of storage; recommended is 1.5 GHz multi-core, 8 GB RAM, and a 120 GB SSD ([OPNsense hardware sizing docs](https://docs.opnsense.org/manual/hardware.html)). Any Windows 10–capable desktop from the last several years clears this easily — the only real gap is NICs.

**Parts needed:**
- The spare desktop tower (confirmed: 1 onboard NIC).
- **One PCIe Gigabit NIC, Intel chipset** (e.g. an Intel i210-based single-port card). OPNsense's own docs specifically recommend Intel NICs over alternatives (Realtek, etc.) for reliability and lower CPU overhead — this matters more on a firewall than almost any other box, since every packet on the network passes through it. Since this is a tower with a free expansion slot, a PCIe card is the sturdier choice over a USB adapter (no USB bus contention, better driver support in FreeBSD/OPNsense). Budget: roughly $15–30.

Once that card is in, the box has two NICs — one becomes WAN, one becomes the LAN-side VLAN trunk.

## Why the sequencing below matters

The Xfinity XB8-T currently does routing, NAT, DHCP, and Wi-Fi for the whole house. The moment it's switched to bridge mode, all of that stops — the network has no working router until OPNsense's WAN side is live. So OPNsense gets fully installed and configured *first*, with the XB8-T left exactly as it is, and the bridge-mode cutover happens last, as a deliberate, short step — not something to do first "to get it out of the way."

## Steps

1. **Back up anything on the Windows 10 box you want to keep.** The install wipes the drive. Box is mostly unused, so a quick manual copy is enough — no need for full disk-imaging software:
   - Plug in an external USB drive.
   - In File Explorer, go to `C:\Users\<your username>\` and copy whatever has content across Desktop, Documents, Downloads, Pictures — drag them into a folder on the USB drive (e.g. `spare-pc-backup`).
   - If the browser on this machine has saved passwords/bookmarks that only live locally (not synced to a Microsoft/Google/Firefox account), export them before wiping: browser Settings → Passwords/Bookmarks → Export. Treat the exported passwords file as sensitive — move it off the USB drive and delete it once imported elsewhere, don't leave it sitting around in plaintext.
   - Skim the Desktop for loose files that aren't in the folders above.
   - Once copied, verify the files actually opened/copied correctly from the USB drive before wiping — don't trust the copy dialog alone.
2. **Install the second NIC** in the free PCIe slot.
3. **Download the OPNsense installer image** from the official [Get Started / download page](https://opnsense.org/get-started/) and write it to a USB drive (Rufus on Windows, or balenaEtcher).
4. **Boot the spare PC from the USB drive** and run through the OPNsense installer — this wipes Windows and installs OPNsense directly on the hardware (bare-metal, per ADR-0002).
5. **Assign interfaces.** OPNsense's console wizard will ask which physical NIC is WAN and which is LAN — it identifies them by MAC address, so plug in one cable at a time if unsure which port is which. Physically it doesn't matter which port ends up as which role, only that the assignment in OPNsense matches how it's cabled.
6. **Leave WAN on DHCP for now** (it'll get a real config once the XB8-T is bridged) and set the LAN interface's IP temporarily so the OPNsense web UI is reachable from a laptop plugged into it directly.
7. **In the OPNsense web UI, create the VLANs** (Interfaces → Other Types → VLAN) on the LAN-side NIC, one per row in the VLAN table in `01-vlan-planning.md` (10, 20, 30, 40, 50, and 99 if still needed once Deco capability is confirmed).
8. **Assign each VLAN as its own OPNsense interface**, give it its subnet, and enable a DHCP server scope for it.
9. **Set default firewall rules to deny inter-VLAN traffic**, then add explicit allow rules only where a real reason exists (e.g., your workstation on Trusted needing to SSH into the lab host on Home Lab). Default-deny-then-allow, not the other way around — this is the entire point of segmenting in the first place.
10. **Cut over the WAN side:** put the XB8-T into bridge mode, then configure OPNsense's WAN interface to match what the ISP now hands directly to it (DHCP in most Xfinity bridge-mode setups, but confirm). Verify OPNsense gets a public IP and the network has internet again.
11. **Configure the switch trunk port** (TL-SG108E) to carry the tagged VLANs between the switch and OPNsense's LAN NIC — this is Phase 3 and gets its own doc, since it's switch-side config rather than firewall-side.
12. **Verify segmentation**: confirm a device on one VLAN cannot reach a device on another unless a rule explicitly allows it.

## Open items before starting

- Confirm ADR-0003 (OPNsense vs. pfSense) — this plan assumes OPNsense.
- Order the PCIe NIC.
