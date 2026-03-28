# Project Research Summary

**Project:** Basic ASIC
**Domain:** Open-source bitcoin-mining hardware platform
**Researched:** 2026-03-28
**Confidence:** MEDIUM

## Executive Summary

The strongest current approach for this project is an RTL-first, verification-first open hardware stack that proves Bitcoin-relevant behavior before it touches serious physical design. The open digital toolchain is mature enough for meaningful simulation, formal checks, FPGA bring-up on supported families, and exploratory ASIC implementation. The weak point is not logic design; it is the transition from "passes my tests" to "credible hardware evidence," especially around byte order, target semantics, and toolchain reproducibility.

For that reason, the repo should start by becoming a trustworthy SHA-256 mining-core platform with explicit Bitcoin fixtures, known-answer tests, CI evidence, and a clean software-to-hardware boundary. FPGA bring-up should come before heavy ASIC ambition, and the ASIC path should be framed as an experimental extension through OpenLane 2 / OpenROAD / SKY130 rather than as a near-term production promise.

The main risks are avoidable if they are treated as first-class concerns: endianness mistakes, premature optimization, unsupported tool features, drifting toolchains, and blurring protocol logic into hardware. The recommended roadmap therefore starts with specification and verification discipline, then adds FPGA reality, then expands into ASIC experimentation and broader platform growth.

## Key Findings

### Recommended Stack

The right foundation is `SystemVerilog RTL + Verilator + cocotb + Yosys + sby + GitHub Actions`, with FuseSoC added early because this repo is explicitly headed toward a multi-target monorepo. For hardware validation, the first practical open FPGA targets are Lattice iCE40 or ECP5 via `nextpnr`. For ASIC experimentation, the modern open path is `OpenLane 2 + OpenROAD + volare + SKY130`, with the explicit caveat that the open SKY130 release is still documented as an experimental preview rather than a production guarantee.

**Core technologies:**
- `SystemVerilog RTL`: primary design language - modern enough for clean module design, but keep to the synthesizable subset.
- `Verilator + cocotb`: simulation and differential testing - fastest route to trustworthy CI evidence.
- `Yosys + nextpnr / OpenROAD`: open synthesis and implementation - enables both FPGA and ASIC paths from the same core.

### Expected Features

The table-stakes capabilities for this project are not websites or management tools. They are the hardware-platform basics: a real mining core, Bitcoin-aware verification, automated CI, a documented host/core boundary, and at least one open FPGA validation path. The differentiators are what make this repo matter: fully public verification artifacts, a visible open ASIC path, and a home-scale decentralization focus rather than closed industrial optimization.

**Must have (table stakes):**
- Verified SHA-256 mining-core RTL - users expect the repo to do real mining-relevant work.
- Known-answer tests and CI evidence - correctness has to be inspectable and repeatable.
- Open-supported FPGA path - needed for real hardware confidence before fabrication spending.

**Should have (competitive):**
- Open ASIC experimentation path - distinguishes the project from simple FPGA demos.
- Standards-friendly work interface - keeps future controller and protocol integration realistic.
- Strong open provenance / licensing hygiene - critical for long-term community contribution.

**Defer (v2+):**
- Marketing site and secondary apps - not core to proving the hardware platform.
- Physical boards, enclosures, and accessories - depend on credible core evidence first.
- Prototype fabrication orders - should come after the design and flow evidence justifies them.

### Architecture Approach

The recommended architecture is layered: protocol/spec fixtures at the top, reusable RTL in the middle, verification as a first-class parallel layer, and target-specific flow adapters below that. The SHA-256 datapath, mining wrapper, verification harness, and target flows should all be separate modules of responsibility. That structure keeps the same core reusable across simulation, FPGA, and ASIC work without letting one target contaminate the others.

**Major components:**
1. Spec and golden-model layer - defines truth from Bitcoin header rules and reference vectors.
2. Reusable RTL layer - owns SHA-256 logic, nonce control, and target comparison.
3. Verification / flow layer - turns the design into evidence across sim, formal, FPGA, and later ASIC paths.

### Critical Pitfalls

1. **Endianness and header-serialization mistakes** - avoid with official fixtures and differential testing.
2. **Optimizing before correctness is proven** - avoid by making tests and formal checks merge gates.
3. **Using unsupported language or target features** - avoid by sticking to the open-stack sweet spot early.
4. **Toolchain and PDK drift** - avoid by pinning the dated EDA bundle and exact PDK revisions.
5. **Treating open SKY130 as production-ready** - avoid by framing ASIC work as experimental until stronger evidence exists.

## Implications for Roadmap

Based on research, suggested phase structure:

### Phase 1: Spec and Verification Foundation
**Rationale:** This comes first because most early mining-hardware failure modes are semantic, not physical.
**Delivers:** Repo structure, Bitcoin-aware golden vectors, verified host/core contract, fast CI simulation, and formal/lint scaffolding.
**Addresses:** Verified core behavior, reproducibility, contributor trust.
**Avoids:** Endianness bugs, tool drift, optimize-before-correctness.

### Phase 2: Mining Core and FPGA Readiness
**Rationale:** Once the evidence loop is trustworthy, the core can mature into a reusable target for real hardware.
**Delivers:** Stable SHA-256 mining wrapper, nonce/target behavior, open FPGA target and timing-aware build artifacts.
**Uses:** Verilator, cocotb, Yosys, nextpnr, FuseSoC.
**Implements:** Reusable RTL modules and target adapters.

### Phase 3: ASIC Experimentation Path
**Rationale:** ASIC flow work should build on a core that is already proven functionally and validated on FPGA-oriented targets.
**Delivers:** OpenLane/OpenROAD flow configs, PDK pinning, and exploratory physical-design reports.
**Uses:** OpenLane 2, OpenROAD, volare, SKY130.
**Avoids:** Overstating ASIC maturity before the flow evidence exists.

### Phase 4: Platform Expansion
**Rationale:** Broader monorepo surfaces only make sense after the core compute path is credible.
**Delivers:** Optional host tooling, docs expansion, and later hardware/software boundaries for boards and supporting services.

### Phase Ordering Rationale

- Verification comes before performance because protocol mistakes are more dangerous than slow designs.
- FPGA validation comes before serious ASIC effort because the open FPGA path offers faster, cheaper physical reality checks.
- Broader platform work comes last because the repo should first become trustworthy at its technical center.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 2:** Exact first-board choice and constraints strategy for open-supported FPGA bring-up.
- **Phase 3:** PDK choice, signoff expectations, and what "prototype-ready" should mean for this project.

Phases with standard patterns (skip research-phase):
- **Phase 1:** Simulation, differential testing, linting, and CI structure are all well-covered by the current open stack.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | The open digital verification and FPGA stack is well-documented by upstream sources. |
| Features | MEDIUM | The niche does not have one canonical open-hardware product shape, so feature prioritization includes informed inference. |
| Architecture | MEDIUM | The layering is robust, but exact repo boundaries should still follow the implementation that emerges. |
| Pitfalls | HIGH | The highest-risk mistakes are well understood from protocol docs and tool documentation. |

**Overall confidence:** MEDIUM

### Gaps to Address

- **First FPGA board family choice:** decide during phase planning based on cost, availability, and contributor accessibility.
- **ASIC milestone definition:** decide what evidence counts as meaningful ASIC progress before writing those requirements.
- **Performance target framing:** avoid inventing GH/s or efficiency goals before the verification baseline exists.

## Sources

### Primary (HIGH confidence)

- https://developer.bitcoin.org/reference/block_chain.html - block-header serialization and target rules.
- https://developer.bitcoin.org/devguide/mining.html - mining software / hardware boundary and nonce flow.
- https://stratumprotocol.org/specification/05-mining-protocol/ - future-facing mining job model.
- https://verilator.org/guide/latest/overview.html - Verilator scope and role.
- https://verilator.org/guide/latest/languages.html - supported language surface and limitations.
- https://github.com/cocotb/cocotb/releases/tag/v2.0.1 - cocotb current release.
- https://github.com/YosysHQ/yosys/releases - Yosys current release.
- https://github.com/olofk/fusesoc/releases - FuseSoC current release.
- https://github.com/YosysHQ/nextpnr - supported FPGA families and experimental boundaries.
- https://openlane2.readthedocs.io/en/latest/ - OpenLane 2 infrastructure positioning.
- https://openroad.readthedocs.io/en/latest/main/README2.html - OpenROAD scope.
- https://skywater-pdk.readthedocs.io/en/main/status.html - SKY130 experimental-preview status.

### Secondary (MEDIUM confidence)

- https://github.com/YosysHQ/setup-oss-cad-suite - OSS CAD Suite setup pattern for CI.
- https://github.com/YosysHQ/oss-cad-suite-build/releases - dated bundle release cadence.
- https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/run-job-variations - GitHub Actions matrix-job support for multi-target CI.
- https://github.com/chipfoundry/volare - exact PDK revision management.
- https://ihp-open-pdk-docs.readthedocs.io/en/main/install/installation.html - alternative open-PDK path for later evaluation.
- https://reuse.software/specifications/ - open licensing / provenance compliance guidance.

### Tertiary (LOW confidence)

- Inferences in `FEATURES.md` and `ARCHITECTURE.md` about the exact long-term monorepo shape - useful for planning, but still to be validated by implementation.

---
*Research completed: 2026-03-28*
*Ready for roadmap: yes*
