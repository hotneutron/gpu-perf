# FlashGPU-Sim feature validation plan

## User request

```text
investigate ~/wk/FLashGPU-Sim, validate the claims on
GPU features: TMA, TMEM, DSM, tcgen05, mbarrier, mma, wgmma, ldmatrix/stmatrix, etc.
persist the validation plan in .plan/ and execute it with a tracker.
```

Subsequent naming instruction, preserved verbatim:

```text
naming convention for the markdowns: {YYMMDD}-{HHMM}-*.md
```

Subsequent code-layout instructions, preserved verbatim:

```text
re-org the code layout. or better, add the code to ~/wk/amora/
```

```text
read amora's readme and docs for the best code layout
```

## Objective and pinned subject

Evidence-placement instruction, preserved verbatim:

```text
move the .plan/flashgpu-validation and .plan/stock-flashgpu-h100 to ./local_artifacts, which should be git ignored.
```

Validate the GPU-feature claim in FlashGPU-Sim's README against implementation,
NVIDIA's ISA documentation, and reproducible execution. The requested path resolves
to `/home/cliu/wk/FlashGPU-Sim`; inspect branch `flash`, commit
`01bdef86caf4b9b6432abcbe3c04b38b86675cdc`. Project-owned plan, tracker, report,
commands, and compact evidence stay in this gpu-perf workspace. Reusable code
lives in Amora's documented backend/probe layout: execution helpers in
`amora/backends/flashgpu_sim/`, NVIDIA semantic sources and reference extraction
in `amora/probes/nvidia/feature_validation/`. Builds and test scratch may use
ignored directories in the subject checkout or caller-selected build roots.

## Evidence contract

A feature name alone is insufficient. Record separately:

- Frontend and architecture admission, including exact shapes, types, and variants.
- Functional semantics: numerical outputs, memory movement, synchronization state,
  collective participation, and rejection of unsupported variants.
- Timing mechanics: dependencies, queues, resource contention, completion, and
  configured versus calibrated constants.
- Silicon accuracy: fresh hardware comparison when possible; distinguish it from
  unit tests and previously recorded measurements.

Verdicts are `validated subset`, `partial`, `contradicted`, or
`unverified`, with an explicit scope and evidence. Passing a unit test validates
its asserted contract, not the complete ISA or general cycle accuracy.

## Execution sequence

| Work item | Action | Completion evidence |
| --- | --- | --- |
| Inventory | Pin source and environment; extract README and module claims; identify PTX/SASS and preset boundaries | Source citations and feature matrix |
| ISA review | Fetch authoritative NVIDIA PTX/CUDA/CUTLASS references; review semantics and target restrictions | URLs, document identity, relevant excerpts |
| Build | Build simulator and existing C++ feature/unit suites using available CUDA toolkit and bounded CPU parallelism | Commands, exit codes, build output |
| Feature tests | Execute existing TMEM/tcgen05, TMA, mbarrier, DSM, MMA, WGMMA, matrix-transfer and async-copy tests; inspect negative coverage | Per-suite counts and failure details |
| Differential checks | Run focused Hopper kernels on available H100 and simulator; use offline Blackwell tests where no matching hardware exists | Outputs, frontend/preset, and comparisons |
| Claim audit | Compare README wording with implemented scope, test coverage and timing evidence | Per-feature verdicts and actionable limitations |
| Closeout | Finalize report and tracker; verify artifacts and subject checkout state | Completed ledger and reproducible evidence |

Use existing focused tests before adding independent probes. New probes are
justified only by a material untested contract. A failed build or test gets
diagnosed and, when possible, an alternate execution path; it remains visible.
Do not alter the subject's tracked code to make its validation pass.

## Feature coverage

| Feature | Required inspection and checks |
| --- | --- |
| TMA | Tensor-map rank/type/stride/swizzle, bounds, shared/global directions, multicast, reduction, mbarrier bytes and bulk groups |
| TMEM | Allocation constraints and capacity, address/lane layout, load/store packing, deallocation, bounds and contention |
| DSM | Cluster launch and rank mapping, remote load/store/atomic/barrier endpoints, multicast fanout, fabric backpressure, enabled presets |
| tcgen05 | MMA shape/type/group restrictions, A/B descriptors and TMEM accumulation, async ordering, commit signaling, unsupported variants, timing |
| mbarrier | Arrival and transaction counts, phase/parity, completion, invalidate, local versus cluster operation, polling/backoff timing |
| mma | Supported shapes/types/layouts, nonuniform numerical inputs and accumulators, fragment mapping, timing |
| wgmma | Four-warp participation, shared/register operands, shapes/types, fence/commit/wait ordering, async timing |
| ldmatrix/stmatrix | Shapes, x1/x2/x4 and transpose admission, lane mapping, load/store round trip, shared-memory timing |
| Related mechanisms | cp.async and bulk-group ordering, cluster barriers and launch APIs, proxy fences, register allocation where they affect feature claims |

## Runtime and accuracy boundaries

Eight H100 GPUs and CUDA toolkits 12.8/12.9/13.3 are present. Verify tool versions
and select a toolkit compatible with both the source and local driver. No B200 or
RTX 5090 is present, so fresh Blackwell numerical/timing comparisons cannot be
claimed. Unit timing assertions and archived NCU measurements must retain their
own provenance. Bounded test execution and timeouts keep a hang reviewable.

## Deliverables

- This plan.
- [Execution tracker](tracker/261120-2136-flashgpu-feature-validation.md).
- [Validation report](../reports/261120-2157-flashgpu-feature-validation-report.md).
- Compact raw results and command receipts under `../local_artifacts/flashgpu-validation/`.
- Reusable audit code in Amora, with the documented `amora flashgpu_sim` CLI.

## Revision History

- 2026-11-20 21:36 -0800: Created plan before build and test execution; preserved the request verbatim.
- 2026-11-20 21:39 -0800: Applied the requested timestamp naming convention and recorded the naming instruction verbatim.
- 2026-11-20 21:59 -0800: Completed execution; preserved both layout prompts, moved reusable code to Amora after reading its README/development docs, and linked the final report. The investigation is complete with supported subsets and reproduced failures recorded.
- 2026-11-20 23:11 -0800: Moved raw validation evidence to Git-ignored local_artifacts and updated links; historical command paths remain in receipts.
- 2026-11-20 23:11 -0800: Recorded the relocation instruction verbatim.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
