# Pitfalls Research

**Domain:** Open-source bitcoin-mining hardware platform (verification-first)
**Researched:** 2026-03-28
**Confidence:** MEDIUM

## Critical Pitfalls

### Pitfall 1: Endianness and Header-Serialization Bugs

**What goes wrong:**
The RTL appears to hash correctly, but it is hashing the wrong byte order, wrong header layout, or wrong target representation, so the "working" design is unusable for real mining.

**Why it happens:**
Bitcoin block headers mix internal byte order hashes with little-endian scalar fields, and those details are easy to blur when moving from software examples to hardware datapaths.

**How to avoid:**
Use official block-header rules as the canonical source, keep 80-byte golden fixtures in the repo, and compare intermediate as well as final results against a software model.

**Warning signs:**
Only self-generated vectors pass; the design lacks official or historical block-header fixtures; target-compare tests never exercise edge cases near the threshold.

**Phase to address:**
Phase 1

---

### Pitfall 2: Optimizing for Synthesis Before Proving Correctness

**What goes wrong:**
The project starts chasing MHz, area, or floorplanning before the mining behavior is locked down, so later bug fixes become expensive and confidence stays low.

**Why it happens:**
ASIC and FPGA work feels more tangible than writing tests, especially in hardware projects.

**How to avoid:**
Make lint, known-answer tests, and focused formal checks mandatory gates before any "optimization" work is treated as real progress.

**Warning signs:**
Commits add pipelining or target-specific tweaks without new regression coverage; synthesis reports are celebrated while protocol edge cases remain untested.

**Phase to address:**
Phase 1

---

### Pitfall 3: Relying on Unsupported or Differently Supported Language Features

**What goes wrong:**
The design simulates in one tool but fails in synthesis or another flow because the code relied on a construct that the open toolchain only partially supports.

**Why it happens:**
Open-source tools have different coverage across modern SystemVerilog, timing constructs, four-state behavior, and advanced verification features.

**How to avoid:**
Stay close to the synthesizable subset, validate early with both simulation and synthesis, and isolate any tool-specific workarounds behind clear comments and tests.

**Warning signs:**
The code depends on classes, complex timing semantics, or X-state assumptions; Verilator and synthesis tools disagree on behavior.

**Phase to address:**
Phase 1

---

### Pitfall 4: Choosing the Wrong First FPGA Target

**What goes wrong:**
The project picks a device family whose open-source flow is experimental or weak, and hardware validation stalls for tooling reasons rather than design reasons.

**Why it happens:**
Contributors often choose based on whichever board they already own, not on backend maturity.

**How to avoid:**
Standardize the first bring-up around iCE40 or ECP5, where the open flow is mature enough to support serious validation.

**Warning signs:**
The initial board plan centers on an experimental backend; build scripts depend on vendor-only binaries earlier than expected.

**Phase to address:**
Phase 2

---

### Pitfall 5: Treating Open SKY130 as Production Truth

**What goes wrong:**
The repo over-commits to a fabrication story that the current open SKY130 release does not actually justify.

**Why it happens:**
Open PDK availability creates the impression that the path is production-ready when the docs explicitly say otherwise.

**How to avoid:**
Frame SKY130 as a learning, test-chip, and design-verification path; keep later fabrication options revisitable until the project truly needs them.

**Warning signs:**
Roadmap language promises production ASIC outcomes too early; contributors assume passing OpenLane is equivalent to fab readiness.

**Phase to address:**
Phase 3

---

### Pitfall 6: Toolchain and PDK Drift

**What goes wrong:**
The same design produces different results across machines or across weeks because tools and PDKs were left floating.

**Why it happens:**
Open EDA ecosystems move quickly and many tools are distributed as rolling bundles or branch-based installs.

**How to avoid:**
Pin OSS CAD Suite by dated release, pin PDKs by exact `volare` hash, and record those pins in repo-visible config.

**Warning signs:**
CI and local runs disagree; synthesis timing or area changes with no RTL diff; onboarding docs say "install latest."

**Phase to address:**
Phase 1

---

### Pitfall 7: Unclear Ownership of Software vs Hardware Responsibilities

**What goes wrong:**
Pool/job logic, host scheduling, and transport semantics leak into the RTL core, making the hardware harder to verify and reuse.

**Why it happens:**
Mining systems span software and hardware, and the boundary is easy to blur when designing from scratch.

**How to avoid:**
Document a strict host/core contract early: software prepares work and job updates; hardware owns hashing, nonce search, and result signaling.

**Warning signs:**
The RTL starts parsing protocol payloads or embedding pool assumptions; the software harness cannot be replaced cleanly.

**Phase to address:**
Phase 1

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Hand-maintained file lists in many scripts | Fast initial setup | Drift across sim / fpga / asic targets | Only for throwaway spikes |
| Testing only final digests, not control behavior | Fewer tests to write | Bugs in nonce stepping, validity signaling, or target compare survive | Never for the main core |
| Hard-coding one board's constraints into top-level RTL | Quick first bring-up | Makes reuse and ASIC retargeting painful | Never in reusable source |
| Floating PDK / tool versions | Less setup effort | Irreproducible regressions and misleading reports | Never once CI exists |

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Bitcoin header fixtures | Using ad hoc byte ordering from blog posts or memory | Use the official block-header reference as the source of truth |
| Pool / job interface | Letting protocol transport leak into the core | Keep transport in software and expose a clean work-unit interface |
| FPGA flows | Starting with an experimental backend because a board is on hand | Choose mature open-supported families first |
| ASIC flows / PDKs | Referring to "latest SKY130" as if it were stable production input | Pin exact revisions and document the experimental status |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Running full ASIC flows on every small commit | CI becomes too slow to use | Split fast verification from slower implementation jobs | As soon as physical-design steps enter CI |
| Keeping all wave dumps on by default | Huge artifacts, slow regressions | Gate waveforms behind debug targets or failure-only collection | Once the test matrix grows |
| Monolithic end-to-end tests only | Bugs are hard to localize | Pair broad regression tests with focused module-level checks | Once the core has multiple submodules |

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Pulling in third-party RTL or scripts without provenance tracking | License contamination or malicious logic | Use REUSE / SPDX metadata and review imports like dependencies |
| Assuming "open source" means "verified" | Hidden correctness failures in reused blocks | Require local tests and explicit trust decisions for imported IP |
| Publishing unverifiable performance claims | Community trust damage | Publish the reports and test setup that support each claim |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| No single entry point for running verification | Contributors bounce off the repo | Provide one obvious "run the evidence" command or target |
| Ambiguous success criteria per milestone | The project feels forever exploratory | Define milestone outputs as artifacts, reports, and passing checks |
| Mixing experimental and stable paths without labels | Contributors waste time on the wrong flow | Label directories and docs as `stable`, `experimental`, or `future` |

## "Looks Done But Isn't" Checklist

- [ ] **SHA-256 core:** Often missing official Bitcoin fixtures - verify against real block-header cases, not toy strings alone.
- [ ] **Simulation flow:** Often missing failure diagnostics - verify tests produce actionable output, not just pass/fail.
- [ ] **FPGA target:** Often missing timing closure evidence - verify routed timing and bitstream generation, not just synthesis.
- [ ] **ASIC experiment:** Often missing DRC/LVS/timing context - verify which checks actually ran and under which pinned PDK.
- [ ] **Open repo hygiene:** Often missing per-file provenance - verify REUSE / SPDX coverage before the asset mix expands.

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Endianness / serialization bug | MEDIUM | Freeze new optimization work, add canonical fixtures, compare intermediate states, then re-baseline reports |
| Wrong initial FPGA target | MEDIUM | Move board-specific glue under target adapters and re-home the bring-up path onto iCE40/ECP5 |
| Tool / PDK drift | LOW to MEDIUM | Re-pin versions, regenerate evidence artifacts, and record the change explicitly |
| Premature ASIC promises | HIGH | Re-scope roadmap language, reclassify ASIC work as experimental, and align requirements with what the flow can prove |

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Endianness and header serialization bugs | Phase 1 | Official fixtures and differential tests pass |
| Optimize-before-correctness | Phase 1 | CI gates block merges without evidence jobs |
| Unsupported language features | Phase 1 | Sim and synthesis both pass on the same source set |
| Wrong first FPGA target | Phase 2 | Bitstream and timing reports exist on an open-supported family |
| Treating SKY130 as production truth | Phase 3 | Docs and roadmap label the ASIC path as experimental until stronger validation exists |
| Toolchain / PDK drift | Phase 1 | Tool and PDK pins are checked into the repo and used in CI |
| Blurry software / hardware boundary | Phase 1 | A documented host/core contract exists and tests target that boundary |

## Sources

- https://developer.bitcoin.org/reference/block_chain.html - source for header serialization, target threshold, and byte-order pitfalls.
- https://developer.bitcoin.org/devguide/mining.html - source for the software/hardware mining boundary and extra nonce behavior.
- https://verilator.org/guide/latest/languages.html - source for simulator language limitations, timing behavior, and two-state caveats.
- https://github.com/YosysHQ/nextpnr - source for supported and experimental FPGA backends.
- https://github.com/YosysHQ/arachne-pnr - source for explicit deprecation in favor of `nextpnr`.
- https://openlane2.readthedocs.io/en/latest/ - source for current OpenLane 2 scope and extensibility.
- https://skywater-pdk.readthedocs.io/en/main/status.html - source for the experimental-preview warning on the open SKY130 release.
- https://github.com/chipfoundry/volare - source for exact PDK revision pinning.
- https://reuse.software/specifications/ - source for current REUSE compliance guidance.

---
*Pitfalls research for: open-source bitcoin-mining hardware platform*
*Researched: 2026-03-28*
