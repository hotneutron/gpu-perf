# FlashGPU-Sim H100 residency rerun tracker

## User request

```text
re-execute the 3 experiments with flashGPU-SIm simulated H100 config aligned with the real H100.
```

Status request preserved verbatim:

```text
status?
```

## Scope and execution

- [Plan](../261124-2322-flashgpu-h100-residency-plan.md).
- Evidence: `../../local_artifacts/flashgpu-h100-residency/`.
- Stock inputs remain unchanged. Simulated residency overrides: `2048:32`
  threads/warp lanes, `32` CTAs; alternate H100 settings are not modified.

| Work | Status | Evidence / next step |
| --- | --- | --- |
| Freeze exact config and replay admission | Completed | Two overrides only; sealed pinned selections and binaries |
| Stock H100 probes | Completed | All 16 commands accounted for; shared tests linked via feature outcomes |
| Feature audit H100 lanes | Completed | All six H100 simulator commands accounted for |
| Portable_core v2 | Completed | Full PTX lanes and SASS pilot have terminal per-case outcomes |
| Comparison and closeout | Completed | Matched-scope report and hashes written; failures and timeouts retained |

- [Final report](../../reports/261124-2322-flashgpu-h100-residency-report.md).

### Initial checkpoint

- [Sealed admission](../../local_artifacts/flashgpu-h100-residency/admission.json).
- [Machine-readable progress](../../local_artifacts/flashgpu-h100-residency/progress.json) and [controller log](../../local_artifacts/flashgpu-h100-residency/controller.log).
- Controller started 23:29 Pacific, PID `1034700`, background task `51205`.
- At 23:35, 72/169 initial commands closed with no controller errors: 15/16
  stock, 6/6 feature, 51 portable. Portable functional: 416 closed passes;
  cycle: 3 closed passes; SASS pilot: all 35 accounted. Known stock DSM launch
  rejection (912) and SASS cp.async abort (`LD.E.STRONG.SYS`) reproduced.
- Matched trio only: PTX MdAPE/P95 changed from 25.56%/51.34% to
  19.88%/46.22%; SASS remained 18.91%/20.58% for those two summary metrics.
  The stock trio PTX observations were in batches, the aligned trio used
  separate processes, and the full-panel result is still pending.

### Latest verified status

- Completed 2026-11-25 01:47 -0800: all 169 initial commands and 36 singleton retries have
  outcomes; every singleton passes. No controller or simulator remains active.
- Both full portable PTX lanes pass 1,155/1,155 cases. There are zero pending
  cycle indices, 1,041 qualified timing pairs and 114 native timing exclusions.
- PTX stock versus aligned MdAPE: 63.01% → 61.39%;
  P95 APE: 100.72% → 96.09%. The report preserves
  the changed process-grouping limitation.
- SASS remains 12 pass/23 unsupported with a three-pair timing intersection.
  The stock and feature cycle windows are unchanged.
- Audited all 205 command identities, 2,381 validation rows and 2,418 banks.
  Final figures are under `reports/assets/`; reports are under `reports/`.
- [Final identity audit](../../local_artifacts/flashgpu-h100-residency/identity-audit.json).
- [Final report](../../reports/261124-2322-flashgpu-h100-residency-report.md).

## Revision History

- 2026-11-24 23:26 -0800: Created separate tracker before simulator execution; preserved stock and hardware references.
- 2026-11-24 23:37 -0800: Recorded sealed admission, unchanged stock timing windows and feature WGMMA timing, complete SASS pilot outcomes, and the first matched portable timing comparison. At this refresh 77/169 commands had closed, including 576 portable functional passes. Full PTX and stock TMA functional execution remain active.
- 2026-11-25 00:39 -0800: Refreshed live receipts: stock and feature panels complete, full functional PTX complete, 545 closed cycle passes and 18 initial timeout cases pending controller-owned recovery.
- 2026-11-25 01:10 -0800: Continued the original controller without duplication; verified 880 closed cycle passes, 757 provisional timing pairs and 35 recovery indices. Added final matched comparison figure generation using relocated report assets.
- 2026-11-25 01:12 -0800: Refreshed the same controller, 912 successful cycle outcomes/804 admitted pairs and 35 pending recovery indices; final report and figures remain gated on all outcomes.
- 2026-11-25 01:42 -0800: Closed simulator execution, comparison and report with every scheduled outcome accounted for.
- 2026-11-25 01:47 -0800: Verified complete recovery and final audited figures/report, updated all report statuses, and closed the plan with no pending work.
