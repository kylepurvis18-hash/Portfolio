# Home Lab Rebuild

A ground-up rebuild of my home lab, documented as I go: network segmentation (VLANs), centralized logging, and detection/alerting — built to be secure, understandable, and defensible in an interview.

## Why this repo exists

Two goals, equally weighted:

1. **A working, segmented, monitored home lab.**
2. **A paper trail of every decision** — not just the end state, but *why* it ended up that way. Screenshots of a finished dashboard prove you can follow a tutorial. A changelog of decisions, trade-offs, and mistakes proves you can reason about a system. The second one is what this repo is optimized for.

## How this repo is organized

| Path | What lives here |
|---|---|
| `docs/roadmap.md` | The phased plan, and the reasoning for the order the phases are in |
| `docs/adr/` | Architecture Decision Records — one file per significant decision: the context, the options considered, what was chosen, and why |
| `docs/network/` | Network topology docs — current state, target state, VLAN design |
| `CHANGELOG.md` | Dated, human-readable log of what changed in the lab itself |

## Current status

Phase 0 — repo scaffolding and baseline documentation. See [`docs/roadmap.md`](docs/roadmap.md) for the full plan.

## Environment at a glance

- **Compute:** Single physical host running a type-2 hypervisor, hosting an Ubuntu Linux VM with Docker installed.
- **Network:** Xfinity gateway (XB8-T) → TP-Link TL-SG108E managed switch → TP-Link Deco 6E (Wi-Fi mesh). Workstation and home lab host are both wired directly to the managed switch.
- **Current segmentation:** None — flat network. This is the first thing the rebuild addresses.

Full details: [`docs/network/00-current-state.md`](docs/network/00-current-state.md).
