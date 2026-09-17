# Roadmap

This is the order the rebuild is happening in, and why it's this order and not some other order. Each phase gets its own ADRs and docs as it's worked; this file just tracks sequencing and status.

## Why this order

The tempting order is "VLANs first, then logging" because segmentation feels like the foundation. But two things push logging-and-visibility earlier than you'd expect, and push a *network reality check* even earlier than that:

1. **You can't segment what you can't see.** Standing up logging first means every later change (adding a VLAN, moving a device, opening a firewall rule) shows up as an observable event from day one. If logging comes last, the whole VLAN build-out happens with no record of what actually happened on the wire — which defeats a lot of the point of doing this for a security portfolio.
2. **The current gear can't do VLANs the "right" way yet, and that has to be settled before any switch config happens.** See Phase 1 — this isn't a nice-to-have design step, it's a hard blocker.
3. **Docs scaffolding has to exist before step one, or step one doesn't get documented.** Hence Phase 0 first, trivially.

## Phases

### Phase 0 — Repo & baseline documentation (in progress)
Set up this repo, document the environment exactly as it stands today (before anything changes), establish the ADR process.

### Phase 1 — Network reality check & VLAN design
Confirm what the current hardware (Xfinity XB8-T, TP-Link TL-SG108E, TP-Link Deco 6E) can and can't do for VLAN tagging and inter-VLAN routing. This determines whether a router/firewall needs to be added before any switch configuration is useful. Output: an ADR documenting the decision, plus a VLAN scheme (segments, subnets, purpose of each).

### Phase 2 — Router/firewall for inter-VLAN routing
Stand up pfSense or OPNsense (free, either virtualized or on dedicated hardware depending on the Phase 1 decision) to do VLAN trunking, inter-VLAN routing, and firewall rules between segments.

### Phase 3 — VLAN configuration on the switch
Configure 802.1Q VLANs on the TL-SG108E, tag the trunk to the router, assign access ports, and verify segmentation actually works (a device on VLAN A cannot reach a device on VLAN B unless a firewall rule allows it).

### Phase 4 — Log collection
Deploy a free log collector/SIEM (Wazuh is the leading candidate — see ADR when written) via Docker on the existing Ubuntu host. Wire up log sources: the firewall, the Docker host itself, and anything else running in the lab.

### Phase 5 — Alerting rules
Write custom detection rules against the collected logs (e.g., failed SSH logins, port scans, new admin users, unexpected inter-VLAN traffic) and route them to a real notification (email, or a webhook to something like Discord/Slack).

### Phase 6 — Documentation polish
Full network diagrams (current + target state), a narrative README pass, and a portfolio-ready write-up of the whole project.

## Status

| Phase | Status |
|---|---|
| 0 — Repo & baseline docs | Done |
| 1 — Network reality check & VLAN design | Nearly done — bridge mode confirmed, ADR-0002 accepted; remaining: confirm ADR-0003, finalize VLAN table |
| 2 — Router/firewall | Starting — see `network/02-firewall-build.md` (pending spare-PC specs) |
| 3 — VLAN config | Not started |
| 4 — Log collection | Not started |
| 5 — Alerting rules | Not started |
| 6 — Documentation polish | Not started |
