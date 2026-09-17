# VLAN Planning (draft — Phase 1)

Status: draft strawman, not yet finalized or implemented. Nothing here is live.

## Why VLANs at all

A flat network means every device — a Docker host running half-finished lab experiments, a phone, a smart plug, a laptop with your real accounts logged in — can talk to every other device by default. A VLAN is a way to split one physical network into multiple separate broadcast domains at Layer 2, so devices in different VLANs *can't* reach each other unless something that can route (the firewall) explicitly allows it. The goal isn't "VLANs" as a checkbox — it's **blast radius reduction**: if something in the lab gets compromised or misconfigured, it shouldn't be able to reach your personal devices or your management interfaces by default.

## Confirmed / assumed hardware capabilities

| Device | Role | VLAN capability |
|---|---|---|
| Xfinity XB8-T | ISP gateway | **Confirmed:** supports bridge mode. Will be switched to bridge mode so the new firewall becomes the "real" router — clean single-NAT setup, no VLAN/routing duties left on the ISP gateway. |
| TP-Link TL-SG108E | Managed switch | Supports 802.1Q VLAN tagging — this is the device that will actually carry multiple VLANs on the wire |
| TP-Link Deco 6E | Wi-Fi mesh | Consumer mesh gear generally does **not** support per-SSID VLAN tagging — assume wireless clients land on a single VLAN until/unless this is confirmed otherwise or the AP is upgraded |
| Firewall (pfSense/OPNsense, hardware TBD per ADR-0002) | Inter-VLAN routing + firewall rules | Full VLAN + routing + firewall support — this is the piece that makes segmentation actually enforced rather than just cosmetic |

## Resolved: Xfinity gateway bridge mode

Confirmed the XB8-T supports bridge mode. Once bridged, the gateway becomes a dumb modem — it stops doing NAT/DHCP/routing/Wi-Fi entirely, and the new OPNsense firewall (ADR-0002, ADR-0003) takes over all of that: WAN handshake with the ISP, NAT, DHCP, and inter-VLAN routing for everything downstream. This avoids double-NAT entirely, which is the clean outcome — no extra ADR needed for that trade-off.

**Heads up before flipping bridge mode on:** once bridged, the XB8-T's own Wi-Fi and admin features stop working, and the firewall becomes the only thing standing between the house and the internet. Don't flip it until OPNsense is installed, configured, and its WAN interface is ready to take over — otherwise there's a window with no working router at all. Sequenced in `docs/network/02-firewall-build.md`.

## Strawman VLAN scheme

Not final — a starting point to react to.

| VLAN ID | Name | Purpose | Notes |
|---|---|---|---|
| 10 | Management | Switch, firewall, and (future) AP admin interfaces only | Most locked-down segment; nothing else should be able to reach it |
| 20 | Trusted | Workstation, personal daily-driver devices | Broad outbound access, no unsolicited inbound from other VLANs |
| 30 | Home Lab | The Docker/Ubuntu VM host and anything else spun up for experiments | Treated as "assume it might get popped" — outbound restricted, no access to Trusted or Management |
| 40 | IoT | Smart plugs, cameras, etc., if/when any exist | Internet-out only, isolated from everything else |
| 50 | Guest | Visitor Wi-Fi | Internet-out only, isolated from everything else |
| 99 | Wi-Fi (interim) | Placeholder if the Deco can't split SSIDs by VLAN | All wireless clients here until confirmed otherwise |

## Next steps for this phase

1. ~~Confirm Xfinity XB8-T bridge-mode support.~~ Done — confirmed supported.
2. Confirm (or rule out) Deco 6E per-SSID VLAN tagging.
3. Finalize the VLAN table above.
4. ~~Finalize ADR-0002 (firewall hardware) based on those answers.~~ Done — Accepted, spare Windows 10 PC repurposed bare-metal.
5. Confirm ADR-0003 (OPNsense vs pfSense) and finalize spare-PC specs (NIC count, form factor) in `docs/network/02-firewall-build.md`.
