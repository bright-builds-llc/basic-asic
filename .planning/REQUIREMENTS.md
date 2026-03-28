# Requirements: Basic ASIC

**Defined:** 2026-03-28
**Core Value:** Create a credibly open, reproducible bitcoin-mining hardware foundation that proves designs in simulation before any physical fabrication.

## v1 Requirements

### Specifications

- [ ] **SPEC-01**: Contributor can find a written host/core contract that defines the first mining core's inputs, outputs, and nonce-search responsibilities.
- [ ] **SPEC-02**: Contributor can run checked-in Bitcoin-aware golden vectors that define expected digest and target-compare behavior for the first mining core.

### Core RTL

- [ ] **CORE-01**: Contributor can inspect a synthesizable RTL implementation of reusable SHA-256 logic intended for mining-core integration.
- [ ] **CORE-02**: Contributor can build a top-level mining core that accepts defined work inputs and iterates nonce search according to the project contract.
- [ ] **CORE-03**: The mining core produces candidate result signals that indicate whether a tested nonce meets a supplied target threshold.

### Verification

- [ ] **VERF-01**: Contributor can run automated simulation that compares RTL output against a software reference for known inputs.
- [ ] **VERF-02**: Regression tests cover byte-order, target-threshold, and nonce/control edge cases relevant to Bitcoin mining.
- [ ] **VERF-03**: CI runs lint and simulation checks on every change to core RTL or verification assets.
- [ ] **VERF-04**: The repo contains at least one focused formal verification target for critical control or safety properties in the first mining core.

### FPGA Path

- [ ] **FPGA-01**: Contributor can synthesize the first mining core for one open-supported FPGA family using a documented flow.
- [ ] **FPGA-02**: The FPGA flow emits reproducible build artifacts or reports that show the design maps successfully with pinned tools.
- [ ] **FPGA-03**: Board-specific constraints and glue logic are isolated from reusable mining-core RTL.

### ASIC Experimentation

- [ ] **ASIC-01**: Contributor can run a documented, pinned experimental ASIC flow for the first mining core against an open PDK.
- [ ] **ASIC-02**: The ASIC experimentation path produces reviewable implementation reports or explicit failing diagnostics tied to the same verified RTL.

### Documentation and Openness

- [ ] **DOCS-01**: Contributor can follow repo documentation to set up the pinned toolchain and run the project's verification flow.
- [ ] **DOCS-02**: Source files and imported assets include machine-readable licensing and provenance metadata suitable for an open-source hardware project.

## v2 Requirements

### Protocol and Software

- **SOFT-01**: Contributor can run a reference host-side work generator or controller harness against the mining core interface.
- **SOFT-02**: Contributor can integrate the platform with a standards-friendly mining job interface such as a future Stratum V2 adapter.

### Additional Hardware

- **FPGA-04**: Contributor can target a second FPGA family beyond the first open-supported bring-up path.
- **ASIC-03**: Contributor can evaluate alternate open-PDK or fabrication paths beyond the initial experimental flow.
- **HARD-01**: Contributor can inspect open board, power, or monitoring hardware that supports the mining core.
- **MECH-01**: Contributor can inspect enclosure, shell, or 3D-print assets matched to real hardware dimensions.

### Product Surfaces

- **SITE-01**: User can browse a project site that explains the hardware platform, evidence artifacts, and contribution path.
- **SITE-02**: User can access supporting apps or services around the hardware platform.

## Out of Scope

| Feature | Reason |
|---------|--------|
| Marketing website in the first milestone | Hardware correctness and verification are the main unresolved risks |
| Pool integrations and full controller software in early phases | They would blur the hardware/software boundary before the core is stable |
| Prototype fabrication orders in v1 | Physical spend should follow simulation and flow evidence |
| Boards, enclosures, and accessories in v1 | They depend on a stable and credible compute core |
| Chasing industrial-scale efficiency targets immediately | The project is optimizing first for openness, reproducibility, and home-scale decentralization |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| SPEC-01 | TBD | Pending |
| SPEC-02 | TBD | Pending |
| CORE-01 | TBD | Pending |
| CORE-02 | TBD | Pending |
| CORE-03 | TBD | Pending |
| VERF-01 | TBD | Pending |
| VERF-02 | TBD | Pending |
| VERF-03 | TBD | Pending |
| VERF-04 | TBD | Pending |
| FPGA-01 | TBD | Pending |
| FPGA-02 | TBD | Pending |
| FPGA-03 | TBD | Pending |
| ASIC-01 | TBD | Pending |
| ASIC-02 | TBD | Pending |
| DOCS-01 | TBD | Pending |
| DOCS-02 | TBD | Pending |

**Coverage:**
- v1 requirements: 16 total
- Mapped to phases: 0
- Unmapped: 16

---
*Requirements defined: 2026-03-28*
*Last updated: 2026-03-28 after initial definition draft*
