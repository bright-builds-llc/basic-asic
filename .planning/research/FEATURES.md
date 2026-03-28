# Feature Research

**Domain:** Open-source bitcoin-mining hardware platform (RTL-first, verification-first)
**Researched:** 2026-03-28
**Confidence:** MEDIUM

## Feature Landscape

This file treats "features" as the deliverables and capabilities users will expect from an open mining-hardware platform. There is no single canonical product template for this niche, so prioritization here is an inference from the approved `PROJECT.md`, Bitcoin mining protocol documentation, and the practical constraints of open FPGA/ASIC tooling.

### Table Stakes (Users Expect These)

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Reproducible SHA-256 mining core RTL | Without a real core, the project is only a concept repo | MEDIUM | Must be explicitly tied to Bitcoin block-header hashing semantics, not generic SHA-256 examples alone. |
| Known-answer tests against Bitcoin block-header and target rules | Mining hardware is worthless if byte order, target parsing, or double-hash behavior is wrong | MEDIUM | Use official block-header field rules and difficulty/target semantics as golden references. |
| Automated simulation and CI regression runs | Open hardware contributors expect proof that changes do not silently break behavior | MEDIUM | CI should run on every push for lint, fast sim, and targeted formal checks. |
| Clear host-to-core work boundary | Mining hardware needs an explicit contract for what comes from software and what the core computes | MEDIUM | The host side typically constructs block headers / job data and the core iterates nonce space. |
| FPGA-oriented synthesis path | An FPGA path is the easiest way to validate the design on real hardware before serious ASIC steps | MEDIUM | Choose open-supported families first so hardware validation is not blocked on proprietary tools. |
| Contributor-facing docs and licensing metadata | Open-source hardware without contribution and licensing clarity does not scale into a real community project | LOW | REUSE / SPDX and flow docs matter early here. |

### Differentiators (Competitive Advantage)

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Fully open verification artifacts | Lets contributors trust the project and extend it without black boxes | MEDIUM | Public waveforms, reports, test vectors, and formal properties are a major differentiator versus closed mining hardware. |
| Open ASIC path, not just FPGA demos | Moves the repo beyond educational toy status toward serious silicon exploration | HIGH | Keep it experimental early, but keep the path visible in the architecture from day one. |
| Home-scale miner focus | Aligns the project with decentralization rather than farm optimization | MEDIUM | Favors understandability, reproducibility, and extensibility over peak industrial efficiency. |
| Standards-friendly work interface | Makes future controllers, pool adapters, and community tools easier to build | MEDIUM | Keep boundaries compatible with Bitcoin header rules now and Stratum V2 style job orchestration later. |
| Monorepo for hardware plus supporting assets | Makes it possible to grow into boards, docs, software, and mechanical assets without fragmentation | MEDIUM | Only valuable if boundaries stay modular and the first milestone remains narrow. |

### Anti-Features (Commonly Requested, Often Problematic)

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Full miner appliance stack in v1 | Feels like "real product" progress | It diverts effort from proving the hardware design and verification pipeline | Keep the host interface minimal and defer controller software |
| Chasing cutting-edge industrial efficiency immediately | Sounds ambitious and market-relevant | It pushes the project toward closed tooling, unsupported nodes, and premature physical-design complexity | Optimize first for correctness, openness, and reproducibility |
| Site/app work before proven hardware | Helps with visibility and polish | It hides the fact that the core technical risk is still unresolved | Defer marketing surfaces until after verified design milestones |
| Unsupported FPGA families as primary target | Contributors may already own those boards | The open flow is weakest where backend support is experimental | Start with iCE40 or ECP5 and add others later |

## Feature Dependencies

```text
Golden Bitcoin vectors
    └──requires──> Correct block-header serialization model
                          └──requires──> Explicit host/core boundary

Cycle-accurate simulation
    └──requires──> Reproducible RTL core
                          └──enhances──> FPGA synthesis path
                                              └──enhances──> ASIC experimentation

Formal safety checks
    └──enhances──> Simulation confidence

Open licensing / provenance
    └──enhances──> Community contributions

Standards-friendly work interface
    └──enables──> Future pool / controller integrations
```

### Dependency Notes

- **Golden vectors require a correct serialization model:** Most mining bugs are not "hash math" bugs, they are byte-order and header-construction bugs.
- **FPGA and ASIC paths should depend on the same verified RTL core:** Do not fork the logic into target-specific implementations too early.
- **Open compliance metadata enhances contributor growth:** This is especially important once the repo contains mixed file types and imported third-party assets.
- **Standards-friendly interfaces should stop at the right boundary:** The mining core should not absorb transport or pool logic that belongs in software.

## MVP Definition

### Launch With (v1)

- [ ] Reproducible RTL implementation of a basic SHA-256 mining core - this is the technical heart of the project.
- [ ] Bitcoin-aware verification suite with known-answer tests - this proves the core is doing mining-relevant work, not generic hashing.
- [ ] CI-backed regression pipeline for lint, simulation, and focused formal checks - this keeps the repo trustworthy as it grows.
- [ ] FPGA-oriented synthesis target on an open-supported family - this creates the first path toward real hardware validation.
- [ ] Monorepo scaffolding and contributor documentation - this keeps later expansion orderly instead of ad hoc.

### Add After Validation (v1.x)

- [ ] OpenLane/OpenROAD ASIC experiment flow - add once the core and tests are stable enough to justify physical-design iteration.
- [ ] Performance / timing / area reporting dashboards - add when the repo has repeatable build artifacts worth comparing.
- [ ] Minimal host-side work generator or reference harness - add when the core boundary is stable and clearly documented.

### Future Consideration (v2+)

- [ ] Real fabricated prototypes - defer until simulation and flow evidence justify spend.
- [ ] Boards, power delivery, and monitoring hardware - defer until the compute core is credible.
- [ ] Enclosures, shells, and 3D-printed accessories - defer until physical hardware dimensions and thermal needs are known.
- [ ] Marketing site and supporting apps/services - defer until the hardware platform has real substance to present.

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Verified SHA-256 RTL core | HIGH | MEDIUM | P1 |
| Bitcoin-aware known-answer tests | HIGH | MEDIUM | P1 |
| CI regression pipeline | HIGH | LOW | P1 |
| FPGA synthesis path | HIGH | MEDIUM | P1 |
| Formal property suite | MEDIUM | MEDIUM | P2 |
| Open ASIC experiment flow | HIGH | HIGH | P2 |
| Reference host-side harness | MEDIUM | MEDIUM | P2 |
| Websites / marketing surfaces | LOW | MEDIUM | P3 |

**Priority key:**
- P1: Must have for launch
- P2: Should have, add when possible
- P3: Nice to have, future consideration

## Competitor Feature Analysis

| Feature | Commercial ASIC miners | Educational SHA-256 / FPGA demos | Our Approach |
|---------|------------------------|----------------------------------|--------------|
| Core hashing logic | Usually closed | Often partial or narrow in scope | Fully open RTL with mining-specific verification |
| Verification evidence | Rarely public | Often limited to demo vectors | CI-visible tests, waveforms, and formal checks |
| Pool / job interface clarity | Usually hidden behind vendor firmware | Often absent | Keep a documented software/hardware boundary with room for later Stratum integration |
| ASIC path openness | Usually proprietary | Usually absent | Keep an explicit open ASIC experimentation path, even if it is not v1-complete |

## Sources

- https://developer.bitcoin.org/reference/block_chain.html - verified block-header serialization, nonce, target threshold, and byte-order rules.
- https://developer.bitcoin.org/devguide/mining.html - verified the practical software-to-hardware mining boundary around 80-byte headers, target thresholds, extra nonce handling, and successful nonce return.
- https://stratumprotocol.org/specification/05-mining-protocol/ - verified that modern mining protocols model explicit job distribution and mining-device channel boundaries.
- https://stratumprotocol.org/ - reviewed Stratum V2 positioning around efficiency, decentralization, and interoperability.
- https://github.com/YosysHQ/nextpnr - verified open FPGA family support for realistic first-hardware targets.
- https://reuse.software/specifications/ - verified current REUSE specification lineage for open contribution and compliance metadata.
- Research synthesis from official open EDA stack sources listed in `STACK.md`.

---
*Feature research for: open-source bitcoin-mining hardware platform*
*Researched: 2026-03-28*
