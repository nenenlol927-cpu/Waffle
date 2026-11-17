# Waffle Mobile GPU — Concept Design

A conceptual mobile GPU design for a phone SoC. The requested name is interpreted as **ConMediatek 5 Extreme Gen 1**, and the process target as **13 nm**. This repository currently contains a design proposal, not fabricated silicon or a verified RTL implementation.

## Design goals

- Phone-class graphics with a strict power and thermal budget
- 1080p gaming and a 120 Hz display target, subject to workload and thermal limits
- Tile-based rendering to reduce external-memory traffic
- Scalable performance with clock and power gating
- A practical software target: Vulkan 1.1 and OpenGL ES 3.2, pending driver implementation and conformance testing

## Proposed configuration

| Block | Proposal |
| --- | --- |
| Architecture | Tile-based deferred renderer |
| Shader cores | 2 shader clusters, 2 execution cores per cluster |
| Execution lanes | 64 FP32 lanes per execution core (256 total) |
| Texture units | 2 per cluster |
| Raster/output | 1 raster unit and 4 pixel output lanes per cluster |
| Cache | 256 KB shared L2; small per-cluster L1 caches |
| Memory interface | Shared SoC LPDDR interface; compression and cache policy handled in the GPU |
| Target clock | 500–800 MHz, dynamically scaled |
| Feature targets | FP16, FP32, texture filtering, MSAA, ASTC, tile-local memory |
| Power features | Fine-grained clock gating, idle power gating, DVFS, thermal throttling |

These are initial planning values, not benchmarked or guaranteed specifications. Achievable performance depends on the SoC memory system, physical design, cooling, drivers, and workload.

## Rendering and power strategy

The GPU divides each frame into small screen tiles and keeps intermediate color/depth data on-chip where possible. This reduces LPDDR traffic, which is often a major source of mobile power use. Work is scheduled across the four execution cores, with inactive units gated when there is insufficient parallel work. The SoC should expose thermal and power limits so the GPU can reduce frequency smoothly rather than repeatedly overheating.

## Important constraints

A 13 nm process is a manufacturing target, not a feature that can be implemented in software. A real GPU requires RTL, verification, synthesis, physical design, a compatible memory and display subsystem, firmware, drivers, and extensive power/performance validation. The proposed API versions are goals only; claiming support requires a working driver and passing the relevant conformance tests.

## Next steps

1. Decide on performance, die-area, and sustained-power targets for the intended phone.
2. Define the command processor, shader instruction set, memory model, and register interfaces.
3. Implement a small synthesizable RTL prototype and verification tests.
4. Build a software simulator and driver before attempting physical implementation.
5. Measure workloads and revise core count, cache sizes, and clock targets.
