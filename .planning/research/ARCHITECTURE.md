# Architecture Research

**Domain:** Open-source bitcoin-mining hardware platform (monorepo, RTL-first)
**Researched:** 2026-03-28
**Confidence:** MEDIUM

## Standard Architecture

### System Overview

```text
┌──────────────────────────────────────────────────────────────┐
│                  Specs / Protocol Layer                      │
├──────────────────────────────────────────────────────────────┤
│  Bitcoin header rules  Mining job model  Targets / vectors  │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────────────────────────────────────┐
│                    Reusable RTL Layer                        │
├──────────────────────────────────────────────────────────────┤
│  SHA-256 round logic  Double-hash wrapper  Nonce control     │
│  Target compare       Top-level mining core                  │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────────────────────────────────────┐
│                 Verification / Evidence Layer                │
├──────────────────────────────────────────────────────────────┤
│  Golden vectors  cocotb tests  Formal checks  Lint / waves  │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────────────────────────────────────┐
│                Flow / Packaging / Artifact Layer             │
├──────────────────────────────────────────────────────────────┤
│  FuseSoC targets  FPGA synth/P&R  ASIC flow  CI reports      │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────────────────────────────────────┐
│                Future Physical / Software Layer              │
├──────────────────────────────────────────────────────────────┤
│  Boards  Mechanical  Controllers  Docs site  Accessories     │
└──────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| Spec model | Defines what the core must compute and what inputs it consumes | Markdown specs, reference Python helpers, known-answer vectors |
| Reusable hash IP | Owns SHA-256 round and schedule behavior | Small SystemVerilog modules with explicit interfaces |
| Mining wrapper | Owns nonce iteration, header assembly boundary, target compare | Top-level RTL around the reusable hash logic |
| Verification harness | Proves behavior across edge cases and regressions | cocotb, simulator scripts, formal `.sby` jobs |
| Build / flow layer | Packages targets and produces repeatable artifacts | FuseSoC plus pinned sim / FPGA / ASIC flows |
| Artifact / docs layer | Publishes evidence contributors can inspect | CI logs, reports, waveforms, timing summaries, docs |

## Recommended Project Structure

```text
rtl/
├── lib/                  # Reusable SHA-256 and support IP
├── mining/               # Mining-specific wrappers and top-level cores
└── include/              # Shared packages, params, interfaces

verify/
├── vectors/              # Golden test vectors and fixtures
├── cocotb/               # Python simulation tests
├── formal/               # sby jobs and assertions
└── lint/                 # Lint config and waivers

flows/
├── sim/                  # Verilator and optional alternate simulator entry points
├── fpga/
│   ├── ice40/            # First open FPGA target
│   └── ecp5/             # Larger open FPGA target
└── asic/
    └── openlane/         # OpenLane 2 and PDK-pinned configs

cores/                    # FuseSoC core files and target metadata
docs/
├── specs/                # Mining boundary docs and design notes
├── decisions/            # ADR-style records
└── reports/              # Generated or checked-in summary artifacts

software/                 # Future host tooling and control-plane code
boards/                   # Future PCB work
mechanical/               # Future shells and printed accessories
.github/workflows/        # CI orchestration
```

### Structure Rationale

- **`rtl/`:** Keeps reusable logic separated from mining-specific integration so ASIC/FPGA reuse stays clean.
- **`verify/`:** Makes proof artifacts first-class instead of scattering them across scripts.
- **`flows/`:** Prevents simulator, FPGA, and ASIC setup from leaking into the design hierarchy.
- **`cores/`:** Gives the monorepo a durable packaging layer as IP count grows.
- **`docs/`:** Keeps design intent and contributor guidance visible, which matters for an open hardware project.
- **`software/`, `boards/`, `mechanical/`:** Reserved boundaries for later milestones so the repo can expand without a structural reset.

## Architectural Patterns

### Pattern 1: Golden Model Differential Testing

**What:** Compare RTL outputs against a software reference model at multiple checkpoints.
**When to use:** From the first commit of any hashing or control logic.
**Trade-offs:** Slightly more setup work, but dramatically lower risk of false confidence.

**Example:**
```python
# Reference model computes expected double-SHA256 from an 80-byte header.
# cocotb then compares the RTL digest and target-match flag to the reference.
```

### Pattern 2: Small Reusable Cores with Thin Mining Wrappers

**What:** Keep SHA-256 datapath logic separate from nonce scheduling, header plumbing, and target comparison.
**When to use:** Always, unless a one-off experiment is being thrown away.
**Trade-offs:** More modules and interfaces, but much easier reuse across sim, FPGA, and ASIC targets.

### Pattern 3: Flow Adapters per Target

**What:** Define simulation, FPGA, and ASIC entry points as adapters around the same core package.
**When to use:** As soon as there is more than one target environment.
**Trade-offs:** Slightly more project structure, much less duplicated logic and fewer "works only in this script" failures.

## Data Flow

### Request Flow

```text
[Bitcoin header / job fixture]
    ↓
[Reference model + vector loader]
    ↓
[Mining core inputs: header words, target, nonce seed]
    ↓
[RTL core computes digest / candidate nonce]
    ↓
[Checker compares digest, target-match, and control behavior]
    ↓
[CI report, waveform, and optional synthesis artifact]
```

### State Management

```text
[Spec fixtures]
    ↓
[Parameterized test cases]
    ↓
[Per-target execution: sim / formal / fpga / asic]
    ↓
[Reports + pass/fail artifacts]
```

### Key Data Flows

1. **Spec-to-RTL flow:** Bitcoin header rules and golden vectors drive module interfaces and expected results.
2. **RTL-to-verification flow:** Every exported signal or observable state should have a reason to exist in either debug, testing, or integration.
3. **RTL-to-target flow:** The same packaged core should feed simulation, FPGA synthesis, and ASIC experimentation with minimal per-target patching.

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 1-2 active cores | Simple module boundaries and hand-written target configs are acceptable |
| 3-10 reusable IP blocks | Move strongly toward FuseSoC metadata, shared verification libraries, and ADR-style decisions |
| 10+ contributors / multiple deliverable families | Lock interface contracts, publish generated artifacts in CI, and separate stable packages from experimental sandboxes |

### Scaling Priorities

1. **First bottleneck:** Reproducibility - fix with pinned toolchains, packaged targets, and executable docs.
2. **Second bottleneck:** Boundary erosion between core logic and target-specific glue - fix with stricter directory ownership and target adapters.

## Anti-Patterns

### Anti-Pattern 1: One Giant Top Module

**What people do:** Put hashing, nonce control, target compare, and board-specific glue in one file.
**Why it's wrong:** It becomes impossible to verify, reuse, or retarget cleanly.
**Do this instead:** Keep library IP, mining wrapper, and target adapters separate.

### Anti-Pattern 2: Script-Defined Architecture

**What people do:** Let the project structure emerge from random shell scripts and CI YAML.
**Why it's wrong:** Contributors cannot tell what is the source of truth or what is safe to reuse.
**Do this instead:** Make package metadata and directory boundaries explicit.

### Anti-Pattern 3: Board Constraints as Product Definition

**What people do:** Design the core around one development board's quirks.
**Why it's wrong:** It makes later FPGA families and ASIC flow work harder than necessary.
**Do this instead:** Keep board constraints in target adapters under `flows/fpga/`.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| GitHub Actions | Matrix CI jobs by target and toolchain | Best place to publish reproducibility evidence early |
| Bitcoin reference docs / vectors | Checked fixtures and golden models | Keep protocol assumptions explicit and reviewable |
| Stratum V2 or later pool adapters | Software-side boundary, not RTL transport logic | Defer transport code, but preserve a clean work/job contract now |
| Open PDKs | Pinned by exact revision | Never depend on "latest" for ASIC artifacts |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| Spec model <-> RTL core | Structured test vectors / reference helpers | The spec layer defines truth |
| RTL library <-> mining wrapper | Typed module interfaces and parameters | Keep reusable SHA-256 logic clean |
| Core package <-> flows | FuseSoC targets or equivalent package metadata | Avoid duplicated file lists |
| Verification <-> CI | Artifacts and exit codes | Every verification job should fail loudly and explain why |

## Sources

- https://developer.bitcoin.org/reference/block_chain.html - verified the block-header structure that should anchor the core boundary.
- https://developer.bitcoin.org/devguide/mining.html - verified the practical software-to-hardware mining handoff.
- https://stratumprotocol.org/specification/05-mining-protocol/ - reviewed mining job and channel concepts for future interface design.
- https://github.com/YosysHQ/nextpnr - verified target-family boundaries that inform FPGA architecture decisions.
- https://openlane2.readthedocs.io/en/latest/ - reviewed extensible flow architecture and supported open-PDK positioning.
- https://openroad.readthedocs.io/en/latest/main/README2.html - reviewed the digital ASIC flow scope that informs later ASIC directory boundaries.
- `STACK.md` synthesis - architectural recommendations here are informed by the verified open-tool stack rather than a single upstream project template.

---
*Architecture research for: open-source bitcoin-mining hardware platform*
*Researched: 2026-03-28*
