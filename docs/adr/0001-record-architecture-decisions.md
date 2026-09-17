# ADR-0001: Record architecture decisions as ADRs

**Status:** Accepted
**Date:** 2026-09-17

## Context

This project has two goals: end up with a working, segmented, monitored home lab, and produce documentation that demonstrates the *reasoning* behind it — useful both for my own learning and as portfolio material for cybersecurity roles. A README that only shows the finished state doesn't capture how decisions got made, what alternatives were rejected, or what trade-offs were accepted.

## Options considered

1. **Plain changelog only** — log what changed, when. Simple, but doesn't capture reasoning or alternatives considered.
2. **Architecture Decision Records (ADRs)** — one short, numbered document per significant decision, following a fixed template (context, options, decision, why, consequences).
3. **Inline comments in config files only** — reasoning lives next to the thing it explains, but is scattered and hard to review as a narrative.

## Decision

Use ADRs (`docs/adr/NNNN-title.md`) for any decision that shapes the architecture — hardware choices, tool selection, network design, security trade-offs — and reserve `CHANGELOG.md` for routine, non-architectural changes (config tweaks, version bumps).

## Why

ADRs force the "why" to be written down at the moment the decision is made, when the trade-offs are freshest — not reconstructed later from memory. They're also skimmable: a reviewer (or a future me) can read just the decisions without wading through implementation detail, and each one is small enough to actually get written instead of becoming a someday-task.

## Consequences

Every non-trivial choice from here on gets an ADR before implementation starts, not after. This adds a small amount of friction to each phase (see `docs/roadmap.md`), which is the point — it's a forcing function to actually articulate the reasoning instead of just doing the thing.
