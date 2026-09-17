# ADR-0003: Use OPNsense as the firewall OS

**Status:** Accepted
**Date:** 2026-09-17

## Context

ADR-0002 decided the firewall runs bare-metal on dedicated hardware. The two mainstream free options for the actual firewall OS are pfSense (Community Edition) and OPNsense. Both are FreeBSD-based, both do VLANs/routing/firewalling/NAT, both have a web UI, both are well documented online.

## Options considered

1. **pfSense CE (Community Edition)** — the older, more widely-referenced-in-tutorials option. Netgate (the company behind it) has been steadily steering features and attention toward its paid "Plus" version and its own hardware appliances over the last few years; CE still exists and works, but its long-term feature parity and release cadence are less certain than they used to be.
2. **OPNsense** — a fork of pfSense's predecessor (m0n0wall) with a more actively developed open-source model, a more modern web UI, and a business model (paid support contracts, not a gated "Plus" tier) that doesn't create the same pressure to hold features back from the free version.

## Decision

Use OPNsense.

## Why

For a project meant to be maintained and documented over the long haul, betting on the option with a cleaner fully-open development model is the safer long-term choice — less risk of hitting a feature wall or a licensing shift down the road. OPNsense's UI is also generally considered more approachable for someone learning firewall concepts for the first time, which matters given the explicit goal of understanding *why*, not just clicking through a wizard. Functionally, for what this lab needs (VLANs, inter-VLAN rules, NAT, basic IDS later), both would work fine — this is a "which is the better long-term bet" call more than a "one can't do the job" call.

## Consequences

- Slightly less overlap with older pfSense-specific tutorials, though OPNsense's own documentation and community are large enough that this isn't a real obstacle.
- Interface naming, menu layout, and package names in later docs/guides in this repo will be OPNsense-specific.
