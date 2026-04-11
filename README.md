# basic-asic

<!-- bright-builds-rules-readme-badges:begin -->

<!-- Managed upstream by bright-builds-rules. If this badge block needs a fix, open an upstream PR or issue instead of editing the downstream managed block. Keep repo-local README content outside this managed badge block. -->

[![GitHub Stars](https://img.shields.io/github/stars/bright-builds-llc/basic-asic)](https://github.com/bright-builds-llc/basic-asic)
[![License](https://img.shields.io/github/license/bright-builds-llc/basic-asic?style=flat-square)](./LICENSE)
[![Bright Builds: Rules](https://raw.githubusercontent.com/bright-builds-llc/bright-builds-rules/main/public/badges/bright-builds-rules-flat.svg)](https://github.com/bright-builds-llc/bright-builds-rules)

<!-- bright-builds-rules-readme-badges:end -->

Basic ASIC is an open hardware design platform for decentralized, home-scale Bitcoin
mining. The project begins with a reproducible SHA-256 mining core and a
verification-first workflow so designs are proven in simulation before any
physical fabrication is considered.

The long-term intent is a monorepo that can hold reusable RTL, verification
assets, FPGA validation, experimental ASIC flows, and later supporting
hardware/software surfaces. Today, the repository is still in its foundation
phase.

## Current Status

- Current phase: `Phase 1: Foundation Contracts`
- The repository currently contains planning docs and repo scaffolding.
- Mining RTL, simulation harnesses, FPGA flows, and ASIC experiments are
  planned deliverables, not present artifacts yet.
- The immediate goal is to define the host/core contract, contributor workflow,
  repo structure, and verification expectations before deeper hardware
  implementation begins.

## What This Project Is Trying to Build

- A reproducible, open-source SHA-256 Bitcoin mining core in RTL.
- Bitcoin-aware verification with golden vectors, differential simulation, and
  focused formal checks.
- A monorepo that can grow cleanly into FPGA bring-up, experimental ASIC work,
  boards, mechanical assets, documentation, and supporting software.
- An open-tool-first workflow that favors reproducibility and contributor access
  wherever practical.
- Early design decisions tuned for home-scale, decentralized mining rather than
  industrial mining-farm constraints.

## Near-Term Non-Goals

- Marketing website or secondary apps/services in the first milestone.
- Pool integrations or full miner controller software before the hardware
  boundary is stable.
- Fabrication orders, prototype runs, boards, enclosures, or accessories before
  the design is credibly verified.
- Premature optimization around industrial-scale efficiency at the expense of
  correctness and reproducibility.

## Architecture Snapshot

Basic ASIC is planned around a layered flow:

`specs and golden vectors -> reusable RTL -> mining wrapper -> verification evidence -> FPGA validation -> experimental ASIC flow`

In practical terms, that means:

- Spec and contract docs define the software-to-hardware boundary.
- Reusable SHA-256 library IP stays separate from mining-specific wrapper
  logic.
- Verification assets provide simulation, regression, and focused formal
  evidence.
- FPGA and ASIC work stay in target-specific flows around the same verified
  core.

## Roadmap

1. **Phase 1: Foundation Contracts** - Establish repo structure, contributor
   workflow, openness metadata, CI expectations, and the host/core contract.
2. **Phase 2: Verified Hash Library** - Build reusable SHA-256 RTL and
   Bitcoin-aware golden-vector verification.
3. **Phase 3: Mining Core Integration** - Add nonce search, target comparison,
   and focused formal verification for mining behavior.
4. **Phase 4: FPGA Validation Path** - Map the verified core onto a first
   open-supported FPGA target with reproducible reports and artifacts.
5. **Phase 5: ASIC Experimentation Path** - Add a pinned, reviewable
   experimental ASIC flow around the same verified RTL.

## Planning Docs

- [Project overview](.planning/PROJECT.md) - mission, core value, context,
  constraints, and key decisions.
- [Requirements](.planning/REQUIREMENTS.md) - v1/v2 requirements and
  traceability to roadmap phases.
- [Roadmap](.planning/ROADMAP.md) - phase breakdown, dependencies, and success
  criteria.
- [Current state](.planning/STATE.md) - the latest project position and active
  focus.
- [Research summary](.planning/research/SUMMARY.md) - synthesized findings
  across stack, architecture, features, and pitfalls.
- [Architecture research](.planning/research/ARCHITECTURE.md) - recommended
  repo shape and system layering.
- [Stack research](.planning/research/STACK.md) - planned toolchain and flow
  direction.
- [Feature research](.planning/research/FEATURES.md) - expected capabilities,
  differentiators, and non-goals.
- [Pitfalls research](.planning/research/PITFALLS.md) - major failure modes the
  project is designed to avoid.

## Planned Stack

As implementation lands, the project is intended to center on:

- `SystemVerilog` for mining-core RTL.
- `Verilator` and `cocotb` for simulation and differential testing.
- `Yosys`, `FuseSoC`, and `SymbiYosys` for synthesis, packaging, and formal
  verification.
- `nextpnr` on `iCE40` or `ECP5` as the first open-supported FPGA validation
  path.
- `OpenLane`, `OpenROAD`, and `SKY130` for experimental ASIC exploration, with
  explicit pinning and no production-readiness claims.

## Contributing

Contributor-facing workflow will evolve with Phase 1, but the best starting
points today are:

- [CONTRIBUTING.md](CONTRIBUTING.md)
- the `.planning/` docs linked above
- `AGENTS.md` for repo instructions

Early contributions should optimize for clarity, reproducibility, and
verification rather than polishing future product surfaces.

## License

Basic ASIC is released under the [MIT License](LICENSE).
