# Basic ASIC

## What This Is

Basic ASIC is an open hardware design platform for decentralized, home-scale bitcoin mining. The project starts with a reproducible, open-source SHA-256 mining core in RTL, backed by strong simulation and verification, and is intended to grow into a monorepo for FPGA, ASIC, board, mechanical, documentation, and supporting software assets. It is being built first for the author's own use and learning, with the explicit goal of enabling a broader open-source hardware community to extend it.

## Core Value

Create a credibly open, reproducible bitcoin-mining hardware foundation that proves designs in simulation before any physical fabrication.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Deliver a reproducible RTL implementation of a basic SHA-256 bitcoin-mining core.
- [ ] Build robust simulation, test bench, and CI verification flows that demonstrate the design behaves correctly before fabrication.
- [ ] Structure the repository as a monorepo that can expand cleanly into FPGA bring-up, ASIC collateral, hardware designs, mechanical assets, documentation, and supporting apps/services.
- [ ] Prefer open-source standards, tooling, design files, and interfaces wherever practical, while keeping the system extensible toward more serious fabrication later.
- [ ] Optimize early project decisions for home-scale, decentralized mining use cases rather than industrial mining-farm requirements.

### Out of Scope

- Marketing website and secondary web/apps/services for the initial milestone — the hardware design and verification foundation comes first.
- Pool integrations, custom miner control software, and farm-operations features for early phases — they would dilute focus before the core design is proven.
- Physical prototype orders, fabrication runs, enclosures, shells, and 3D-printed accessories in the first milestone — those depend on credible simulation and test evidence first.

## Context

The project exists because the current bitcoin mining ecosystem is seen as closed, under-innovated, and optimized for centralized mining farms rather than open, home-scale participation. The author is an electrical engineer with a stronger day-to-day software engineering background and wants active guidance through the hardware, verification, and fabrication process. The intended long-term shape is intentionally broad: open chip and fabrication designs, FPGA and ASIC pathways, related hardware and shells, 3D-printed accessories, and eventually web properties and other supporting services. The near-term focus is narrower: establish a trustworthy technical foundation with designs, simulations, and durable tests that can later justify real-world prototype fabrication.

## Constraints

- **Openness**: Prefer as close to 100% open source as practical — the project is meant to interoperate with the free and open-source ecosystem.
- **Verification**: Simulation and test evidence must come before fabrication — design claims need proof, not aspiration.
- **Audience**: Build for the author first, but keep contributor participation in mind — the repo should be understandable and welcoming to an open community.
- **Scope control**: Defer peripheral apps, websites, and physical accessories until the core mining design path is credible — early sprawl would slow the main objective.
- **Extensibility**: Favor interfaces and structure that can accommodate industry standards and more serious fabrication later — early choices should not corner the project into a hobby-only path.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Organize the repo as an open hardware design platform for bitcoin mining | The project needs a unifying boundary broad enough for hardware and software, but clear enough to prioritize decisions | — Pending |
| Start with a basic SHA-256 mining core in RTL plus reproducible verification | This creates the fastest path to provable technical progress before fabrication | — Pending |
| Run ASIC and FPGA paths in parallel only where that helps produce early proofs faster | The project should prioritize whichever path yields credible tests and simulations first | — Pending |
| Optimize early phases for home-scale decentralization while staying compatible with future industry-grade fabrication | The mission is decentralization, but the design should remain extendable | — Pending |
| Prefer open-source tooling and artifacts, allowing non-open fallbacks only when no realistic open option exists yet | This matches the project's values without blocking progress on day one | — Pending |

---
*Last updated: 2026-03-28 after initialization*
