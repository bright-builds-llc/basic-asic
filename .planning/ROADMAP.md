# Roadmap: Basic ASIC

## Overview

This roadmap takes Basic ASIC from project setup into a credible open hardware foundation for bitcoin mining. The sequence is deliberate: define the contract and reproducible tooling first, prove reusable hashing behavior second, integrate the mining-specific core and edge-case verification third, then validate the same design through an open FPGA path before attempting an experimental ASIC flow.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Foundation Contracts** - Establish repo structure, contributor workflow, CI, openness metadata, and the host/core contract.
- [ ] **Phase 2: Verified Hash Library** - Build reusable SHA-256 RTL and Bitcoin-aware differential tests against golden vectors.
- [ ] **Phase 3: Mining Core Integration** - Assemble the mining wrapper, nonce/target behavior, and formal verification for critical control logic.
- [ ] **Phase 4: FPGA Validation Path** - Prove the design maps cleanly onto one open-supported FPGA family with reproducible artifacts.
- [ ] **Phase 5: ASIC Experimentation Path** - Add a pinned, reviewable experimental ASIC flow around the same verified RTL.

## Phase Details

### Phase 1: Foundation Contracts
**Goal**: Make the repo reproducible and contributor-ready before deeper hardware work begins.
**Depends on**: Nothing (first phase)
**Requirements**: SPEC-01, VERF-03, DOCS-01, DOCS-02
**Success Criteria** (what must be TRUE):
1. Contributor can read a written host/core contract and understand the first mining core's boundary without extra explanation.
2. Contributor can set up the pinned toolchain and run the documented verification entry point locally.
3. Pull requests that touch RTL or verification assets automatically run lint and simulation checks.
4. The initial repo asset set includes machine-readable licensing and provenance metadata.
**Plans**: 3 plans

Plans:
- [ ] 01-01: Create repo layout, pinned toolchain entry points, and contributor-facing setup docs.
- [ ] 01-02: Add CI workflow for lint and simulation gates on RTL and verification changes.
- [ ] 01-03: Write the host/core contract and establish licensing / provenance metadata coverage.

### Phase 2: Verified Hash Library
**Goal**: Produce reusable SHA-256 RTL with Bitcoin-aware golden vectors and differential simulation evidence.
**Depends on**: Phase 1
**Requirements**: SPEC-02, CORE-01, VERF-01
**Success Criteria** (what must be TRUE):
1. Contributor can inspect a reusable SHA-256 RTL library intended for mining-core integration.
2. Contributor can run checked-in Bitcoin-aware golden vectors and see RTL outputs match a software reference.
3. Failed verification runs provide actionable diagnostics rather than opaque pass/fail output.
**Plans**: 2 plans

Plans:
- [ ] 02-01: Implement reusable SHA-256 library modules and package them for reuse.
- [ ] 02-02: Add golden vectors, software reference checks, and differential simulation harnesses.

### Phase 3: Mining Core Integration
**Goal**: Turn the reusable hash library into a mining-specific core with nonce search, target comparison, and deeper verification.
**Depends on**: Phase 2
**Requirements**: CORE-02, CORE-03, VERF-02, VERF-04
**Success Criteria** (what must be TRUE):
1. Contributor can simulate a top-level mining core that accepts defined work inputs and iterates nonce search.
2. Contributor can see passing regression coverage for byte order, target-threshold handling, and nonce/control edge cases.
3. Contributor can run at least one focused formal verification target proving a critical control or safety property.
4. Contributor can observe candidate-result signaling when a tested nonce meets the supplied target threshold.
**Plans**: 3 plans

Plans:
- [ ] 03-01: Build the mining wrapper and nonce-search control path around the reusable hash library.
- [ ] 03-02: Implement target comparison and edge-case regression coverage for mining-specific behavior.
- [ ] 03-03: Add formal properties and focused formal jobs for core control / safety guarantees.

### Phase 4: FPGA Validation Path
**Goal**: Validate the verified mining core on one open-supported FPGA family without contaminating reusable RTL boundaries.
**Depends on**: Phase 3
**Requirements**: FPGA-01, FPGA-02, FPGA-03
**Success Criteria** (what must be TRUE):
1. Contributor can synthesize the mining core for one documented open-supported FPGA target using pinned tooling.
2. Contributor can inspect reproducible synthesis / place-route artifacts or reports from that flow.
3. Board-specific glue and constraints live outside reusable mining-core RTL.
**Plans**: 3 plans

Plans:
- [ ] 04-01: Select the first FPGA target family and define the target-specific interface / constraints boundary.
- [ ] 04-02: Implement the documented FPGA build flow and generate reproducible reports or artifacts.
- [ ] 04-03: Harden the target adapter so board-specific logic stays isolated from reusable core RTL.

### Phase 5: ASIC Experimentation Path
**Goal**: Establish an experimental open ASIC implementation path that reuses the validated mining core and pinned open tooling.
**Depends on**: Phase 4
**Requirements**: ASIC-01, ASIC-02
**Success Criteria** (what must be TRUE):
1. Contributor can run a documented, pinned experimental ASIC flow against the same verified mining-core RTL.
2. Contributor can inspect implementation reports or explicit failure diagnostics produced by that flow.
3. Contributor can identify the exact tool and PDK revisions used for the ASIC experiment.
**Plans**: 2 plans

Plans:
- [ ] 05-01: Add pinned OpenLane / OpenROAD / PDK configuration for the first mining-core experiment.
- [ ] 05-02: Publish ASIC flow outputs and diagnostics in a reviewable, reproducible format.

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Foundation Contracts | 0/3 | Not started | - |
| 2. Verified Hash Library | 0/2 | Not started | - |
| 3. Mining Core Integration | 0/3 | Not started | - |
| 4. FPGA Validation Path | 0/3 | Not started | - |
| 5. ASIC Experimentation Path | 0/2 | Not started | - |
