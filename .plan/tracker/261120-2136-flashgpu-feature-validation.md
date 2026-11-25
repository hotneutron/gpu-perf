# FlashGPU-Sim feature validation tracker

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

## Run identity

- Source: `/home/cliu/wk/FlashGPU-Sim`, `flash`,
  `01bdef86caf4b9b6432abcbe3c04b38b86675cdc`.
- Plan: [validation plan](../261120-2136-flashgpu-feature-validation-plan.md).
- Report: [validation report](../../reports/261120-2157-flashgpu-feature-validation-report.md).
- Started: 2026-11-20 21:36 -0800.
- Overall status: completed with findings.
- Completed: 2026-11-20 21:59 -0800.

## Work ledger

| Work item | Status | Evidence and next action |
| --- | --- | --- |
| Source/environment inventory | completed | Source pinned; eight H100 GPUs, CUDA 13.3.73, GCC 11.3.0 identified |
| Authoritative ISA review | completed | NVIDIA PTX ISA 9.5 fetched; 11 exact sections and HTML hash in nvidia-ptx-excerpts.json |
| Simulator and test build | completed | Simulator and seven selected binaries built; initial setup failures retained in commands.jsonl |
| Feature unit suites | completed | SM100: 186/186; SM120: 537/537 after honoring repository-relative fixture lookup |
| Hopper differential runs | completed | WGMMA 25/25 and matrix/cp.async 16/16 pass in both; native MMA 29/29, mbarrier 23/23. Native TMA: 37 unique passes and one device-created-map edge failure; reduction context contamination isolated |
| Blackwell offline checks | completed with findings | TMEM transfer passes after guide override; independent MMA admission abort/crash reproduced. Capacity probe confirms absent finite TMEM allocation/reuse; no native Blackwell device |
| Claim and timing assessment | completed | Per-feature scope and limitations in report; one matched WGMMA region has +3.45% total cycles but +43.85% exposed wait |
| Amora code integration | completed | Read README, RULES and development docs; used backend/probe split; CLI, 15 passing targeted checks, wheel source packaging and relocated runs verified |
| Report and artifact closeout | completed | Final report, source/tool hashes, command receipts, XML results and lossless compressed evidence index |

## Feature ledger

| Feature | Status | Provisional observation |
| --- | --- | --- |
| TMA | validated subset | PTX 38/38, multicast 11/11; native 37 unique passes and one edge failure. Reduction/multicast/proxy timing remains bounded |
| TMEM | partial; capacity contradicted | 27 component passes, raw transfer works; 1024 live columns and no reuse violate finite allocation contract; banking/contention absent |
| DSM | partial; atomic form contradicted | Existing 40 cases and 44 fabric/endpoint cases pass; independent native 5/5 versus model 2/5. Atomic-only failure persists without guidance |
| tcgen05 | partial; admission failed | Eight timing cases pass; default guide abort and isolated control-flow preprocessing SIGSEGV. Descriptor strides/swizzles ignored; single-CTA dense execution scope only |
| mbarrier | validated subset | PTX 22 pass/1 skip, native 23 pass, standard remote arrival/owner wait passes; wait policy and module-doc limits recorded |
| mma | validated subset | PTX/native 29/29 each; supported shapes/types and numerical approximations recorded |
| wgmma | validated subset | PTX/native 25/25 each; strict SASS 9/9, detailed SASS 2/2; scoped timing discrepancy recorded |
| ldmatrix/stmatrix | validated subset | Matrix/cp.async groups 16/16 in native/PTX; strict SASS x4 2/2; x2/x4 transpose and new Blackwell shapes unqualified |
| Related mechanisms | selected checks validated | cp.async, bulk groups and cluster launch/rank checks pass; proxy, multicast transport and shared-atomic timing limits retained |

## Execution events

- 2026-11-20 21:36 -0800: Resolved capitalization discrepancy and pinned checkout; created plan and tracker before execution.
- 2026-11-20 21:39 -0800: Applied requested Markdown naming convention. Initial parallel test build failed because GoogleTest headers were not available until setup completed; retried after setup without changing tracked source.
- 2026-11-20 21:42 -0800: Configured build succeeded. The second build attempt omitted setup_environment and lacked generated-header paths; this was an invocation error. Initial unit failures came from scratch outside repository ancestry, not feature failures. Rerunning in ignored subject scratch passed 723/723 component cases. Native Hopper WGMMA and matrix/cp.async passed 41/41 cases.
- 2026-11-20 21:42 -0800: TMEM probe allocated 1024 live columns, read column 512 and row 128, and returned base 1024 after deallocation. Source also explicitly linearizes tcgen05 shared descriptors without swizzle, leading or stride mapping; include these limits in the final verdict.
- 2026-11-20 21:49 -0800: Broad TMA suite completed 38/38; existing H200 cluster suite completed 40/40. Isolated independent DSM atomic tests fail, including explicit cluster fence and atomic-only. Native TMA edge failure reproduced; reduction tests pass in a fresh process.
- 2026-11-20 21:54 -0800: Diagnosed tcgen05 SIGSEGV in function_info::connect_basic_blocks at ptx_ir.cc:597 with GDB; reproduced when MMA is selected alone and both reorder flags are disabled. Default preset separately aborts on missing tcgen05.st SASS guidance.
- 2026-11-20 21:57 -0800: Integrated Amora code under documented backend/probe paths; 15 backend/CLI checks pass, all three CUDA/C++ sources included in wheel, relocated DSM native tests 5/5, TMEM and reference extraction reproduced. WGMMA timing CSVs show 957 versus 990 total cycles and 520 versus 748 wait cycles.
- 2026-11-20 21:59 -0800: Closed investigation with final report and reproducible evidence; subject tracked source remains clean. Existing Amora changes were preserved. Failure repair is recommended follow-up, not required to complete this investigation.

## Evidence and reproduction

- [Structured per-case results](../../local_artifacts/flashgpu-validation/results.json).
- [Initial command receipts](../../local_artifacts/flashgpu-validation/commands.jsonl); final Amora runs have individual receipt JSON files in the same directory.
- [Source/toolchain inventory](../../local_artifacts/flashgpu-validation/source-inventory.json).
- [NVIDIA PTX excerpts](../../local_artifacts/flashgpu-validation/nvidia-ptx-excerpts.json).
- [Lossless evidence index](../../local_artifacts/flashgpu-validation/evidence-index.json).
- [Reusable CLI guide](../../../amora/docs/development/261120-2155-flashgpu-feature-validation.md).

Do not aggregate repeated runs into unique feature coverage. Original setup/fixture
failures, default-preset aborts, SIGSEGV runs and CUDA context contamination are
retained with their corrective or diagnostic disposition in the report.

## Revision History

- 2026-11-20 21:36 -0800: Created tracker and initialized validation ledger.
- 2026-11-20 21:39 -0800: Renamed tracker, updated links, preserved naming instruction, and recorded the first build attempt.
- 2026-11-20 21:42 -0800: Recorded completed builds, 723 passing unit cases, native Hopper results, and substantive TMEM/tcgen05 findings.
- 2026-11-20 21:59 -0800: Completed all ledgers; preserved both Amora prompts, final failures/diagnostics, code layout decision, timing observation and evidence links.
- 2026-11-20 23:11 -0800: Moved raw validation evidence to Git-ignored local_artifacts and updated links; historical command paths remain in receipts.
- 2026-11-25 00:55 -0800: Updated report/figure links for the reports/ and reports/assets/ layout.
