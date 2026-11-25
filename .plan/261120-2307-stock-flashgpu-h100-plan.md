# Stock FlashGPU-Sim versus local H100 validation plan

## User request

```text
I mean the report for "stock flashgpu-sim code " against the H100 we have here
```

Earlier request establishing the investigation:

```text
investigate ~/wk/FLashGPU-Sim, validate the claims on
GPU features: TMA, TMEM, DSM, tcgen05, mbarrier, mma, wgmma, ldmatrix/stmatrix, etc.
persist the validation plan in .plan/ and execute it with a tracker.
```

Naming requirement:

```text
naming convention for the markdowns: {YYMMDD}-{HHMM}-*.md
```

## Comparison contract

Evidence-placement instruction, preserved verbatim:

```text
move the .plan/flashgpu-validation and .plan/stock-flashgpu-h100 to ./local_artifacts, which should be git ignored.
```

The two evidence directories are now under `../local_artifacts/`; this plan,
trackers and reports remain in `.plan/`. `/local_artifacts/` is ignored by Git.

Stock code is the clean FlashGPU-Sim `flash` checkout at
`01bdef86caf4b9b6432abcbe3c04b38b86675cdc`, matching the local
`origin/flash` reference. The primary simulator profile is the shipped,
unmodified `SM90_H100`. The shipped `SM90_H100_SASS_FRONTEND` gets a
separate result column. Never use SM120 or H200 simulation to establish the
H100 profile's behavior. Configuration experiments must be labeled separately.

Compare the same SM90a executable, input cases and CPU assertions against
native CUDA on local GPU 0 (H100 80GB HBM3). Amora supplies external test drivers
and run/evidence capture; simulator tracked source and presets stay unchanged.
TMEM and tcgen05 are Blackwell features and are outside the H100 silicon scope.

## Work ledger

| Work | Status | Evidence |
| --- | --- | --- |
| Pin stock source and H100 environment | completed | Clean source, matching origin/flash and live CUDA attributes verified |
| Reuse matching prior H100 runs | completed | Thirteen previous runs retained with exact config/binary/XML provenance |
| Complete missing H100 pairs | completed | Same SM90a binaries: stock H100 MMA 29/29, mbarrier 23/23, TMA 38/38 |
| Check default DSM admission | completed | Stock profile rejects native two-CTA launch with invalid-cluster-size error 912 |
| Measure H100 timing | completed | Three WGMMA points × five native repetitions; three transfer points × three native repetitions; shipped PTX/SASS comparisons completed |
| Compare resource limits | completed | Both profiles configure 1536 threads/24 CTAs per SM versus native 2048/32 |
| Report and closeout | completed | Dedicated report and ignored evidence; temporary relocation redirect removed |

## Evidence and execution limits

Reuse completed runs when source, binary, configuration and inputs match.
Fresh native timing samples run sequentially on GPU 0, with repetitions and
observed dispersion. Record clock policy; do not convert a close total into
general completion accuracy. Cycles measured inside a kernel and simulated
whole-kernel cycles retain separate definitions.

Use bounded focused timing probes and existing suites. Keep all receipts and
new results under `../local_artifacts/stock-flashgpu-h100/`. The earlier broad-feature report
remains available but is superseded for H100-only conclusions by this report.

## Deliverables

- This plan.
- [Tracker](tracker/261120-2307-stock-flashgpu-h100.md).
- [H100 report](../reports/261120-2307-stock-flashgpu-h100-report.md).
- Source/config/device inventory, same-binary pairs, timing samples and raw
  receipts under `../local_artifacts/stock-flashgpu-h100/`.

## Revision History

- 2026-11-20 23:07 -0800: Created H100-specific execution plan in response to clarified stock-code comparison scope.
- 2026-11-20 23:11 -0800: Preserved the evidence-placement prompt and moved both raw evidence trees to the ignored local_artifacts directory.
- 2026-11-20 23:21 -0800: Completed every planned run and the H100 report, including full TMA execution, repeated timing, exact source/profile comparison and evidence relocation.
- 2026-11-20 23:11 -0800: Moved raw validation evidence to Git-ignored local_artifacts and updated links; historical command paths remain in receipts.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
