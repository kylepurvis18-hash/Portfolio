# Changelog

All notable changes to the lab itself (not the docs) are logged here, oldest section at the bottom. Format loosely follows [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

### Decided
- ADR-0002 **Accepted**: firewall runs bare-metal on a repurposed spare Windows 10 PC (wiped), not virtualized on the lab host.
- ADR-0003 **Proposed**: OPNsense as the firewall OS over pfSense CE.
- Xfinity XB8-T confirmed to support bridge mode — firewall will be the sole router (no double-NAT).

### Added
- Repo scaffolding: README, roadmap, ADR process, changelog.
- Baseline documentation of current environment (single-host hypervisor, Ubuntu+Docker VM, flat network behind Xfinity XB8-T / TP-Link TL-SG108E / TP-Link Deco 6E).

### Added
- Draft VLAN scheme and network-capability assessment (`docs/network/01-vlan-planning.md`).
- ADR-0002 (proposed): firewall runs on dedicated hardware, not virtualized on the lab host.

### Decided
- Documentation approach: Architecture Decision Records (ADRs) for anything that changes the design, plain changelog entries for routine changes. See [ADR-0001](docs/adr/0001-record-architecture-decisions.md).
