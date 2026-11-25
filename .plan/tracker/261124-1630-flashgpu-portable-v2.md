# FlashGPU-Sim portable_core v2 validation tracker

## User request

```text
validate FlashGPU-Sim using H100 (this machine) and ~/wk/amora portable_core v2 kernels and shapes.
```

Superseded SSH-target request:

```text
validate FlashGPU-Sim using H100 "ssh h100sjc0" and ~/wk/amora portable_core v2 kernels and shapes.
```

## Identity and scope

- Target: physical H100 GPU 7, `CUDA_VISIBLE_DEVICES=7`, logical CUDA device 0; UUID `GPU-b356e84d-b220-a33c-dd68-3ea01ec109ea`.
- Simulator: stock `01bdef86caf4b9b6432abcbe3c04b38b86675cdc`, `SM90_H100`.
- Amora: `56749f4ed228ce1daece388195a882caf101083f`; portable_core v2.
- [Plan](../261124-1630-flashgpu-portable-v2-plan.md).
- [Report](../../reports/261124-1630-flashgpu-portable-v2-report.md).
- Raw evidence: `../../local_artifacts/flashgpu-portable-v2/`.
- Status: completed for the source-available corpus; exact H3 references remain source blocked.

## Work ledger

| Work | Status | Evidence / next step |
| --- | --- | --- |
| Local target and source inventory | completed | Eight local H100 GPUs idle; stock build present; SSH request superseded |
| Corpus freeze / exact unique count | completed | 1,155 cases, 18 shapes, 30 operators; frozen profiles, aliases and 2,418 hashed input/oracle banks |
| CPU qualification | completed | 1,505/1,505 required checks pass with Torch 2.8.0 and NumPy 2.3.3; CPU source unchanged |
| Standalone CUDA adapter | completed | Explicit-boundary standalone CUDA realization compiled; integrity/schema/CLI tests pass |
| Native H100 pilot / full correctness | completed | GPU 7: pilot 35/35 and all 1,155 cases pass in each of three full runs; all node ports and unchanged inputs checked |
| Native timing | completed | Three independent 30-sample runs per case; 1,041 meet declared span/MAD quality, 114 remain timing inconclusive |
| Stock FlashGPU pilot / full panel | completed | PTX cycle 1,155/1,155 pass, including 100 checkpoints and 30 successful singleton recoveries; SASS 12/35 pass with 23 exact-form/projection failures accounted |
| Full PTX functional numerical lane | completed | All 1,155 cases pass; functional-mode zero spans are discarded and never treated as timing |
| Comparison/report/closeout | completed | All 1,041 admitted timing pairs analyzed; complete shape/operator/graph tables; receipt/source/archive audit PASS; no pending execution |

## Events

- 2026-11-24 16:30 -0800: Read v2 suite/schema/oracle contracts and the existing CUDA/TPU proposal. Confirmed source-only CPU execution, 1,141 main-panel cases and two unavailable exact H3 reference profiles.
- 2026-11-24 16:30 -0800: Applied user correction to local H100. SSH hostname resolution is no longer relevant to this validation.
- 2026-11-24 16:46 -0800: User steering preserved verbatim: `use the free GPU gpu7...`; `continue`. GPU 7 is selected. CPU release matrix passed all 1,505 required checks after correcting the initial PYTHONPATH invocation.
- 2026-11-24 17:12 -0800: Native pilot 35/35 and full corpus 1,155/1,155 pass in three runs. SASS pilot 12 pass/23 unsupported forms (FCHK, FRND.FLOOR, MUFU.RSQ/SIN, FSET.BF.GT.AND). PTX slow shards were stopped after preserving 100 completed observations; remaining 1,055 cases resumed with 16 workers and 8 CPU threads each. Simulator source/presets and native executable remain unchanged.
- 2026-11-24 17:34 -0800: Full functional simulation passed 1,155/1,155. Added active report separating completed numerical support from pending cycle timing. All native runs used physical GPU 7; automatic continuation will monitor and close remaining cycle work without user input.
- 2026-11-24 17:44 -0800: Automatic continuation verified the live 16-worker cycle panel. At refresh, 370 cases have closed successful cycle observations and 261 have eligible native timing comparisons. One 16-case batch timed out after 14 completions; its two unfinished cases remain scheduled for singleton retry after queued shards. No duplicate jobs or simulator changes were introduced.
- 2026-11-24 17:54 -0800: Continued existing cycle jobs without duplication. Verified 418 successful cycle cases and 309 eligible timing comparisons; 20 fast shards closed, with one partial timeout awaiting the existing singleton-retry stage. Native/functional coverage remains 1,155/1,155. Closed verbose logs are being losslessly compressed; active output files are unchanged.
- 2026-11-24 18:08 -0800: Verified 620 closed successful cycle cases and 511 eligible comparisons. Nine batches have timed out after partial completion; their 25 unfinished cases remain in the existing singleton-retry queue. Other initial shards are active/queued. Native and functional full coverage remains complete; no timing completion or numerical failure is inferred from a batch timeout.
- 2026-11-24 18:22 -0800: Verified 715 successful closed cycle cases and 605 eligible timing comparisons. Forty initial shards have closed; nine partial-timeout batches retain 25 queued singleton cases. The existing controller remains live. Executable/CUDA source and 69 archived output hashes were checked; no duplicate work or simulator changes were introduced.

- 2026-11-24 19:35 -0800: Controller completed all 66 initial shards and 30 singleton retries. PTX cycle and functional coverage are each 1,155/1,155 pass; no unresolved cases. Native timing admits 1,041 pairs with median sim/native 1.630, MdAPE 63.0% and p95 APE 100.7%. Audited 163 command outputs, 69 archives, 2,418 banks, executable/source/config identities and GPU 7 placement; 51 regression tests pass. Completed shape/operator/graph report, closed recovery ledger and cancelled continuation `60b13676`.

## Revision History

- 2026-11-24 16:30 -0800: Created tracker and initialized execution ledger.
- 2026-11-24 16:46 -0800: Recorded GPU 7, CPU release evidence and CUDA implementation progress.
- 2026-11-24 17:12 -0800: Recorded full native correctness/timing and SASS scope; retained interruption evidence and updated PTX execution policy.
- 2026-11-24 17:34 -0800: Recorded complete functional numerical coverage and the active comparison report; cycle scope remains in progress.
- 2026-11-24 17:44 -0800: Recorded verified cycle progress and pending batch-timeout recovery; full timing/report scope remains active.
- 2026-11-24 17:54 -0800: Updated verified cycle counts and archived closed outputs without changing pending work or qualification scope.
- 2026-11-24 18:08 -0800: Recorded cycle progress and explicit pending timeout recovery; final timing/report scope remains active.
- 2026-11-24 18:22 -0800: Updated cycle progress and verified preserved executable/archive identities; initial and singleton stages remain active.

- 2026-11-24 19:35 -0800: Marked source-available validation complete after all required cycle outcomes, timing breakdowns and integrity checks; exact unavailable H3 references and SASS boundaries remain explicit.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
