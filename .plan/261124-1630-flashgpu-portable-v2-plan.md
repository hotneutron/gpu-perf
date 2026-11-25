# FlashGPU-Sim portable_core v2 validation plan

## User request

```text
validate FlashGPU-Sim using H100 (this machine) and ~/wk/amora portable_core v2 kernels and shapes.
```

The earlier target request, superseded by the local-machine clarification:

```text
validate FlashGPU-Sim using H100 "ssh h100sjc0" and ~/wk/amora portable_core v2 kernels and shapes.
```

## Scope and frozen subjects

Use physical H100 GPU 7, with `CUDA_VISIBLE_DEVICES=7` and logical CUDA device 0.
The selection instruction is preserved verbatim: `use the free GPU gpu7...`.
Stock FlashGPU-Sim is
`01bdef86caf4b9b6432abcbe3c04b38b86675cdc`; primary preset
`SM90_H100`. Report any configuration/front-end experiments separately.

Amora is currently on `tpu` at
`56749f4ed228ce1daece388195a882caf101083f`. Read its portable_core README,
v2 schema/execution/oracle/coverage contracts and CUDA+TPU proposal before
implementing the adapter. Keep `expanded/v2/*.py` unchanged so source-pinned
CPU snapshots retain their identity.

V2 currently supplies CPU mathematical programs, not a qualified CUDA executable.
For this simulator comparison, introduce a separately named standalone CUDA
realization with explicit node/storage boundaries. The proposed eager Torch CUDA
lane remains a different realization. Current validation authorization covers
the necessary CUDA driver and H100/FlashGPU measurements; it does not authorize
TPU work or changes to portable mathematical semantics.

## Corpus contract

Freeze every source-available v2 suite/profile and retain all aliases when
deduplicating identical case IDs/hashes. The main source-available release matrix
has 1,141 cases: Gate-v2 reference 720, development-source controls 288,
H3 semantic controls 40, H3 program controls 84, and diagnostic controls 9.
Include additional smoke shapes as an independently identified pilot/panel.
Compute the exact unique count before scheduling execution.

Exact H3 reference profiles remain `blocked_missing_source`; their development
controls do not qualify unavailable historical references. Record those rows
explicitly. Preserve canonical program/case/upstream hashes, typed ports,
unroll/state edges, physical input strides/offsets and deterministic input bytes.
The half-scale contraction is an identity-matrix fixture and may lower to scale;
it does not establish dense GEMM/tensor-core coverage.

## Execution sequence

| Work | Required evidence |
| --- | --- |
| Inventory and freeze | Exact corpus, shape/operator counts, source hashes, snapshots and initial tracker |
| CPU baseline | Existing v2 contract tests and required source-available CPU parity; no accelerator inference |
| CUDA realization | Separate adapter/source/protocol identity, exact dtype/store/layout/index/tuple semantics |
| Native correctness pilot | H100 placement and per-node CPU-oracle comparisons for all operator witnesses |
| Native full panel | Per-case supported/failed/unsupported accounting on the frozen source-available corpus |
| Native timing | Accepted correctness, preparation-excluded program spans, warmup and raw repeated samples |
| FlashGPU pilot | Same executable and exact inputs; initial PTX/SASS admission and correctness checks |
| FlashGPU full panel | Execute all scheduled source-available cases with bounded runs; keep failures/unsupported forms and timeouts visible |
| Analysis and closeout | Shape/operator/graph breakdowns, comparable timing differences, scope limits and reproducible report |

## Realization and evidence discipline

Use explicit FP32 working operations with FP16 rounding at declared storage and
temporary boundaries; integer/tie/discrete results are exact. Preserve strided
and offset input storage rather than silently packing it. Scatter controls must
be deterministic. Execute each already-unrolled program node once in topological
order; do not repeat the graph again from its unroll metadata.

CUDA numerical checks use the existing independently versioned oracle and
per-boundary tolerances. Native timing rows require successful output checks
before and after measurement. CUDA-event program spans, kernel times, simulation
cycles and simulator event spans keep separate labels and estimands. Record
free-running hardware clocks versus stock model frequency. Predeclare timing
quality checks and keep short/noisy spans inconclusive.

Build/profiling preparation and host/device transfers stay outside the measured
program body. Trace/capture variants must retain their realization identity.
Do not fix or retune the simulator to make this validation pass.

## Ownership and deliverables

Reusable code lives under Amora's `amora/benchmarking/portable_v2/`,
`amora/backends/nvidia/` and the existing `amora/backends/flashgpu_sim/`.
This project owns the plan, tracker and decisions under `.plan/`, and the
report under `reports/` with figures in `reports/assets/`.
Raw snapshots, inputs, generated sources/binaries, logs and results live under
Git-ignored `local_artifacts/flashgpu-portable-v2/`.

- [Execution tracker](tracker/261124-1630-flashgpu-portable-v2.md).
- [Validation report](../reports/261124-1630-flashgpu-portable-v2-report.md).
- Full case-level coverage ledger, including blocked H3 reference scope.

## Execution result

The source-available plan is complete. All 1,155 unique cases pass native H100
GPU 7 in three runs and both stock PTX lanes. The 35-case SASS pilot is fully
accounted: 12 pass and 23 exact-form support failures. Every required PTX cycle
case has an outcome; all 30 interrupted cases passed singleton recovery.

The final report includes shape/operator/graph summaries and 1,041 qualified
native/PTX timing pairs, with median span ratio 1.630 and median absolute
difference 63.0%. The remaining 114 native cases stay timing inconclusive.
Source, executable, corpus, raw-result and archived-output integrity checks pass;
all 51 regression tests pass. No runtime task or continuation remains pending.
Exact H3 historical reference profiles remain blocked by missing source under
the original corpus contract.

## Revision History

- 2026-11-24 16:30 -0800: Created the validation plan before CUDA implementation; preserved both requests and adopted the local H100 correction.
- 2026-11-24 16:46 -0800: Recorded explicit GPU 7 selection and `continue` steering; exact corpus is 1,155 cases, 18 shapes and 30 operators. CPU release checks passed 1,505/1,505.

- 2026-11-24 19:35 -0800: Recorded completion against the frozen scope, all numerical/cycle outcomes, declared timing exclusions, final breakdowns and checked evidence; retained source-blocked references.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
