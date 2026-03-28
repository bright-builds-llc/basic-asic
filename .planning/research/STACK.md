# Stack Research

**Domain:** Open-source bitcoin-mining hardware platform (RTL-first, FPGA-to-ASIC path)
**Researched:** 2026-03-28
**Confidence:** MEDIUM

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| SystemVerilog RTL | IEEE 1800-2023 subset | Primary design language for the mining core and wrappers | Verilator's current docs target modern SystemVerilog, but the project should stay in the synthesizable subset so the same source remains usable in open FPGA and ASIC flows. |
| Verilator | 5.047 docs current | Fast simulation, linting, waveform generation, CI smoke/regression runs | Verilator is the strongest open-source choice for high-speed regression and makes it practical to run mining-core verification on every commit. |
| cocotb | 2.0.1 | Python testbench framework and differential testing harness | cocotb makes it easy to compare RTL behavior against software SHA-256 reference models and Bitcoin block-header test vectors. |
| Yosys | 0.63 | Open-source synthesis and logic transformation | Yosys is the standard synthesis front-end in the open digital hardware stack and plugs into both FPGA (`nextpnr`) and ASIC (`OpenROAD` / `OpenLane 2`) paths. |
| OpenLane 2 | 2.0.0b16 (pre-release) | Reproducible ASIC flow orchestration | OpenLane 2 provides a configurable open infrastructure around synthesis, floorplanning, routing, and signoff-style checks, which is the right way to experiment with an ASIC path without hand-wiring every step. |
| OpenROAD | v0.9.0-beta | Digital physical design from RTL toward GDSII | OpenROAD is the core open digital implementation engine behind the modern open ASIC flow and is strong enough for rapid design exploration. |
| SkyWater SKY130 PDK | Pin by `volare` hash, not by name alone | First open PDK target for ASIC experimentation and test-chip readiness | SKY130 has the deepest public ecosystem and the most examples, making it the best first open PDK even though its open release is still explicitly not a production guarantee. |

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| FuseSoC | 2.4.5 | Core packaging, target definitions, reusable hardware build metadata | Use from the beginning if the repo will host multiple IP blocks, verification targets, and future FPGA/ASIC variants in one monorepo. |
| SymbiYosys (`sby`) | Current stable docs, no tagged release stream | Formal property runs on focused modules and protocol boundaries | Use for invariants that should never break, such as control-state safety, nonce sequencing, and target-compare logic. |
| `volare` | Current repo, pin exact PDK commit | Open PDK version manager | Use for any ASIC experimentation so PDK drift does not silently break reproducibility. |
| `nextpnr` | Prefer OSS CAD Suite pinned by date | Open FPGA place-and-route | Use for FPGA bring-up on supported open families such as Lattice iCE40 and ECP5. |
| REUSE / SPDX | REUSE spec 3.3 | Machine-readable licensing and provenance metadata | Use from day one because this project aims to be maximally open and will likely mix RTL, docs, scripts, CAD, and mechanical files. |

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| OSS CAD Suite | Bundled open EDA environment | Use a pinned dated release in CI and local setup so Yosys, nextpnr, Verilator, GTKWave, and related tools stay consistent across contributors. |
| GitHub Actions | Matrix CI for lint, simulation, formal, and synthesis sanity checks | Use matrix jobs to test multiple simulators and flow targets without duplicating workflow YAML. |
| GTKWave | Waveform inspection | Keep it as a debugging tool, but do not make waveform inspection a substitute for executable assertions and test checks. |
| Nix or containerized tooling | Reproducible dev environment | Strongly recommended once the first toolchain is working, because open EDA stacks drift quickly across host machines. |

## Installation

```bash
# Core Python tooling
python3 -m pip install --user cocotb fusesoc volare

# CI / local open EDA bundle
# Pin a dated OSS CAD Suite release in repo docs or scripts.

# Optional ASIC flow orchestration
python3 -m pip install --user openlane
```

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|-------------------------|
| Verilator | Icarus Verilog | Use Icarus as a compatibility backstop for simpler test cases or when debugging simulator-specific behavior. |
| SKY130 via OpenLane 2 | IHP SG13G2 open PDK | Evaluate IHP later if the project needs a different device/library mix or a smoother ready-to-use PDK experience. |
| `nextpnr` on iCE40 / ECP5 | Vendor FPGA flows | Use vendor tools only when the chosen FPGA family is not realistically supported by the open stack yet. |
| FuseSoC-managed targets | Ad hoc shell scripts | Use raw scripts only for tiny experiments; once there are multiple cores and flows, the script sprawl becomes technical debt fast. |

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| `arachne-pnr` | The upstream repo explicitly says it is no longer maintained | `nextpnr` |
| Experimental Xilinx-first bring-up | `nextpnr` still labels Xilinx 7-series support as experimental | Lattice iCE40 or ECP5 first |
| Floating tool installs | Unpinned EDA tools and PDKs make regressions impossible to explain | Pin OSS CAD Suite by date and PDKs by exact `volare` hash |
| Treating the open SKY130 release as production-ready | Official SKY130 docs call the open release an experimental preview and not for production use yet | Use SKY130 for test chips and initial verification; revisit production choices later |

## Stack Patterns by Variant

**If the goal is fastest proof of correctness:**
- Use `SystemVerilog RTL + Verilator + cocotb + sby + GitHub Actions`.
- Because the fastest route to confidence is executable verification, not physical-design setup.

**If the goal is first open FPGA bring-up:**
- Add `FuseSoC + Yosys + nextpnr + Project IceStorm/Trellis-backed boards`.
- Because iCE40 and ECP5 are the cleanest open-source-supported hardware targets.

**If the goal is early ASIC experimentation:**
- Add `OpenLane 2 + OpenROAD + volare + SKY130`.
- Because this is the most documented open digital ASIC path, but it should remain secondary to functional verification at first.

## Version Compatibility

| Package A | Compatible With | Notes |
|-----------|-----------------|-------|
| `cocotb 2.0.1` | `Verilator 5.x` | Good fit for CI-driven differential testing; still pin simulator options in repo scripts. |
| `Yosys 0.63` | `nextpnr` and `sby` via OSS CAD Suite | Prefer the suite over assembling versions by hand. |
| `OpenLane 2.0.0b16` | `OpenROAD v0.9.0-beta` and `volare`-managed PDKs | Treat the flow as a pinned stack, not a mix-and-match set of latest tools. |
| `FuseSoC 2.4.5` | Multi-target monorepo flows | Especially useful once simulation, FPGA, and ASIC targets coexist. |

## Sources

- https://verilator.org/guide/latest/overview.html - current Verilator docs identify the active documentation stream as 5.047 and describe its SystemVerilog-to-C++ model flow.
- https://verilator.org/guide/latest/languages.html - verified supported SystemVerilog language surface and synthesis-subset caveats.
- https://github.com/cocotb/cocotb/releases/tag/v2.0.1 - verified cocotb current tagged release.
- https://github.com/YosysHQ/yosys/releases - verified Yosys 0.63 current release.
- https://github.com/olofk/fusesoc/releases - verified FuseSoC 2.4.5 current release.
- https://github.com/YosysHQ/setup-oss-cad-suite - verified that the official GitHub Action sets up the latest OSS CAD Suite release and positions it as the bundle for up-to-date tools.
- https://github.com/YosysHQ/oss-cad-suite-build/releases - verified dated release cadence and current latest bundle pattern.
- https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/run-job-variations - verified GitHub Actions matrix-job support for multi-target CI.
- https://github.com/YosysHQ/nextpnr - verified supported FPGA families and experimental status of some backends.
- https://github.com/YosysHQ/arachne-pnr - verified deprecation notice in favor of `nextpnr`.
- https://openlane2.readthedocs.io/en/latest/ - verified OpenLane 2 positioning and supported open PDK families.
- https://github.com/chipfoundry/openlane2/releases - verified current public release stream.
- https://openroad.readthedocs.io/en/latest/main/README2.html - verified OpenROAD scope and RTL-to-GDSII positioning.
- https://github.com/The-OpenROAD-Project/OpenROAD/releases - verified current public release stream.
- https://skywater-pdk.readthedocs.io/en/main/status.html - verified that the open SKY130 release is still documented as an experimental preview not intended for production.
- https://github.com/chipfoundry/volare - verified `volare` usage for pinning specific open PDK revisions.
- https://ihp-open-pdk-docs.readthedocs.io/en/main/install/installation.html - reviewed IHP open PDK as a later alternative path.
- https://reuse.software/specifications/ - verified current REUSE specification lineage for machine-readable licensing.

---
*Stack research for: open-source bitcoin-mining hardware platform*
*Researched: 2026-03-28*
