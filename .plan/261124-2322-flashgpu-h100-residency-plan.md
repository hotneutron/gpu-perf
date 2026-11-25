# FlashGPU-Sim H100 residency-aligned reruns

## User request

```text
re-execute the 3 experiments with flashGPU-SIm simulated H100 config aligned with the real H100.
```

## Scope and frozen comparison

Re-execute the H100 simulator work from the three reports in the preceding
answer. Keep the earlier native H100 measurements as fixed references for this
simulator-only sensitivity comparison. Stock and feature references used H100
GPU 0; portable_core v2 used physical GPU 7. No native workloads are launched
and no hardware clocks are changed. Simulator commands keep GPU visibility
restricted to physical GPU 7.

- [Stock H100 probes](../reports/261120-2307-stock-flashgpu-h100-report.md): reproduce each
  stock H100 simulator timing and functional command; include inherited H100
  checks shared with the feature audit (execute once, cross-reference).
- [Feature audit](../reports/261120-2157-flashgpu-feature-validation-report.md): reproduce
  the H100 PTX/SASS integration checks and eight-round WGMMA timing point. H200
  and Blackwell experiments remain outside an H100 configuration rerun; keep
  historical hardware-only evidence unchanged.
- [Portable_core v2](../reports/261124-1630-flashgpu-portable-v2-report.md): execute the
  same 1,155-case PTX cycle and functional panels and the same 35-case SASS
  timing pilot against the frozen executable and corpus. Prioritize the three
  previously matched SASS/PTX timing cases; use those cycle observations in
  the full-panel ledger rather than scheduling them twice.

## Configuration contract

Use clean simulator source `01bdef86caf4b9b6432abcbe3c04b38b86675cdc` and the
respective original `SM90_H100` and `SM90_H100_SASS_FRONTEND` presets. In runner
scratch copies only, change exactly these two settings:

```text
-gpgpu_shader_core_pipeline 2048:32
-gpgpu_shader_cta 32
```

With 32 lanes per warp, this gives 2,048 threads, 64 warps and 32 CTAs per SM,
matching the native driver's residency ceilings. This is **residency alignment**,
not full-chip calibration. Preserve the source, shipped presets, clock domains,
launch, memory/SoC, DSM and other subsystem parameters. In particular, enabling
DSM or replacing H100 with the H200 preset is not part of the change.

## Evidence and execution

Verify the stock preset, CUDA executable, original native reference and corpus
identities; seal the job selection and effective config hashes before execution.
For legacy functional runs without a simulator binary hash, use the matching
native binary recorded in the reuse manifest and original simulator argv.
Rebase output paths only. Similar source names are not a substitute for a hash.

Bound simulator work with a single controller, at most 16 concurrent commands,
8 OpenMP threads per PTX cycle shard, smaller allocations for other lanes and
1,800 seconds per portable cycle shard. Retain per-case successes from
interrupted batches and retry unfinished cases singly once. Preserve exact SASS
errors, avoid PTX fallback and keep functional-mode event spans out of timing.
Keep baseline files intact and write all new raw evidence to Git-ignored
`local_artifacts/flashgpu-h100-residency/`.

Compare the same WGMMA and transfer cycle windows, matched portable native/PTX
program spans, and SASS pilot admission and matched timing. Never pool those
different estimands. Report APE on matched qualified cases with shape/operator
breakdowns for the portable PTX panel; report on the same SASS intersection
separately. The resource cap is not sufficient to predict runtime, and is
nonbinding for one CTA. Portable runs use a fresh sharding schedule and the
three early PTX cases run as singletons rather than in their original batches;
case-level comparisons do not by themselves exclude simulator process-state
or cache carryover effects. Do not retune other parameters to improve errors.

- [Execution tracker](tracker/261124-2322-flashgpu-h100-residency.md).

## Execution result

The source-available rerun is complete: 169 initial commands and 36 singleton
retries have terminal outcomes, with all 36 recoveries passing. Both full PTX
lanes pass 1,155/1,155 cases; the SASS pilot has 12 passes and 23 exact-form
support failures. Stock and feature timing controls are unchanged.

The matched 1,041-pair PTX comparison is 61.39% MdAPE and
96.09% P95 APE, compared with stock 63.01% and
100.72%. All 205 output/configuration identities and 2,381 validation
rows pass closeout audit. Sharding/carryover limits on causal interpretation
remain explicit. Reports and figures live in `reports/` and `reports/assets/`.

[Final report](../reports/261124-2322-flashgpu-h100-residency-report.md).

## Revision History

- 2026-11-24 23:26 -0800: Persisted the follow-up request, three-campaign scope, exact two-knob delta and paired evidence contract before launch.
- 2026-11-24 23:36 -0800: Clarified that portable case estimates retain their changed process sharding as a limitation; no further model parameters are changed.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
- 2026-11-25 01:47 -0800: Recorded completed coverage, matched timing and final evidence audit; no simulation or recovery remains pending.
