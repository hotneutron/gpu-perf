# Stock FlashGPU-Sim versus H100 tracker

## User request

```text
I mean the report for "stock flashgpu-sim code " against the H100 we have here
```

## Scope and identity

Evidence-placement instruction, preserved verbatim:

```text
move the .plan/flashgpu-validation and .plan/stock-flashgpu-h100 to ./local_artifacts, which should be git ignored.
```

- Stock FlashGPU-Sim: `01bdef86caf4b9b6432abcbe3c04b38b86675cdc`.
- Primary preset: unchanged `SM90_H100`.
- Separate SASS preset: unchanged `SM90_H100_SASS_FRONTEND`.
- Hardware: local GPU 0, H100 80GB HBM3,
  `GPU-c80fe196-e3ba-82ff-7233-d9804f701864`.
- [Plan](../261120-2307-stock-flashgpu-h100-plan.md).
- [Report](../../reports/261120-2307-stock-flashgpu-h100-report.md).
- Status: completed with findings.
- Completed: 2026-11-20 23:21 -0800.

## Work ledger

| Work | Status | Result |
| --- | --- | --- |
| Stock source/environment | completed | Clean stock source; H100 132 SMs, driver and native CUDA attributes captured |
| Existing matching H100 pairs | completed | Thirteen previous runs verified; includes exact-binary native DSM reference |
| MMA/mbarrier/TMA H100 pairs | completed | Same SM90a binaries: stock H100 MMA 29/29, mbarrier 23/23, TMA 38/38 pass |
| Default DSM admission | completed | Stock H100 preset rejects a two-CTA cluster launch with CUDA error 912 before atomic execution |
| Fresh timing samples | completed with findings | Fifteen native WGMMA and nine transfer repetitions; six WGMMA and six transfer profile checks completed. SASS cp.async standalone driver aborts on LD.E.STRONG.SYS |
| Resource limits | completed | Native H100: 2048 threads/32 CTAs per SM; PTX H100 preset: 1536 threads/24 CTAs |
| Evidence relocation | completed | Both raw evidence trees are Git ignored under local_artifacts; temporary redirect removed |
| Final report/evidence | completed | Report and all run/provenance checks completed; stock source/presets unchanged |

## Execution events

- 2026-11-20 23:07 -0800: Re-scoped report around unchanged simulator code and the actual local H100; removed H200/SM120 evidence from H100 qualification.
- 2026-11-20 23:11 -0800: Completed matching MMA/mbarrier comparisons, native timing repetitions, stock DSM admission and resource inspection. Relocated both evidence trees as requested; continue remaining model/transfer runs.
- 2026-11-20 23:19 -0800: Finished all timing points and wrote the H100 report. Stock PTX WGMMA region differences range from -3.40% to +66.81% in the measured panel; clock policy remains free-running hardware versus unchanged 1500 MHz model. Full TMA suite is progressing through final multidimensional cases.
- 2026-11-20 23:21 -0800: Full TMA completed 38/38. Removed the relocation redirect, verified current adapter wheel contents and finalized all planned H100 comparison work.
- 2026-11-20 23:23 -0800: Final verification passed for 43 new receipts, 13 reused XML records, no timeouts, source/config identities, links and ignored evidence directories.

## Revision History

- 2026-11-20 23:07 -0800: Created dedicated stock-code/H100 tracker.
- 2026-11-20 23:11 -0800: Recorded progress and the requested ignored evidence location.
- 2026-11-20 23:19 -0800: Recorded completed timing, profile-specific failures and draft report; only full TMA completion and evidence closeout remain.
- 2026-11-20 23:21 -0800: Closed the H100 tracker with all runs complete and findings preserved.
- 2026-11-20 23:23 -0800: Recorded final evidence and artifact verification.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
