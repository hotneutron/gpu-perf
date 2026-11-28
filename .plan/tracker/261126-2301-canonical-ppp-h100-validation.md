# Canonical PPP H100 validation tracker

## User request

```text
Work on the Canonical PPP kernel results for both simulators.
```

```text
canonical kernels results for FlashGPU-sim and GCoM?
```

```text
why the 2,500 case study is incomplete?
```

```text
What's blocking the 2,500 case run, then?
```

```text
I have another high priority task that might be using the GPUs, wait for my approval before launching GPU jobs.
```

```text
launch the 2500 CPU continuation work.
```

```text
check the status every hour, reporting ETA of completion.
```

```text
meanwhile, commit and puash the changes.
```

## Work ledger

| Work | Status |
| --- | --- |
| Inventory/manifest/plan | Completed: 2,500 primary cases and nine source-minimum pilots |
| Matched capture/replay adapters | Prepared; 51 checks passed, 11 existing skips |
| Nine-family pilot | Completed: all nine native pilots; simulator support outcomes retained |
| Reviewed expansion | Completed: 137 native cases from the pilot and bounded expansion; full manifest remains deferred |
| Full 2,500-case study | CPU simulation running across primary manifest; 2371 native cases remain pending GPU approval |
| CPU-only continuation | Running: PID 3868818, 46 four-thread workers; 4742 new PTX/SASS jobs; GPU hold unchanged |
| Canonical and combined reports | Initial-stage results retained; live continuation reports show GPU approval hold |

## Revision History

- 2026-11-26T23:01:53-08:00: Campaign opened; hardware/CPU/storage observations are advisory until dedicated admission.

- 2026-11-26T23:07:35-08:00: All nine binaries compiled; fixed native/trace identities, window accounting and immutable replay receipts. Launched the resource-admitted family pilot; raw evidence and live status are project-owned.

- 2026-11-26T23:13:16-08:00: Source-audited the tracer variant suffix and FlashGPU atexit completion marker; revalidated five complete saved traces and six successful FlashGPU outputs. No simulation repeated. Retained the MoE foreign-context acquisition and admitted a fresh dedicated interval. Shared checks: 52 passed, 11 existing skips. First GCoM replays and reviewed expansion continue.

- 2026-11-26T23:18:18-08:00: Five GCoM source-pilot replays completed. Admitted a capture-only cuBLAS Std ELF recovery: CUDA 13.3 nvdisasm accepts the retained cubins rejected by cuobjdump; an external text adapter preserves every PC/opcode/128-bit instruction and verifies encodings against original ELF bytes (63,120 instructions checked offline). Adapter/admission tests: 11 passed. Same binary, native references and timing model; no prior cuBLAS simulator execution is repeated.

- 2026-11-26T23:26:14-08:00: Retained all failed Std ELF captures. Corrected nvdisasm-only branch framing/annotations and verified the exact exported cubin bytes plus all 63,120 instructions through the original GCoM CPU metadata parser before another changed-tool capture. No GCoM simulation has run for these cuBLAS cases. The MoE PTX pilot completed; its GCoM pilot remains active.

- 2026-11-26T23:30:17-08:00: Pilot resource review admits up to 64 staged cases and 48 independent four-thread points, bounded by the observed maximum trace size and 16 GiB reserve. An inotify watcher will hand off the controller only at an expansion boundary with no descendants, GPU leases or unfinished model keys; no capture/model is interrupted or repeated. It will finalize figures, reports and link/hash verification after execution. The original 16-case waves remain historical scheduling evidence.

- 2026-11-26T23:33:42-08:00: Verified the live report snapshot: 25 native completions, unique case identities, independently recomputed MdAPE/P95, 28 local links and frozen model/tool/config hashes. 55 regression checks passed; 11 existing skips. All three cuBLAS traces qualified after exact-byte static rendering; first replays remain in the execution queue.

- 2026-11-27T00:18:53-08:00: Finalized retained canonical evidence and combined report. Native completions 137; qualified PTX/SASS/GCoM pairs 44/36/45. Verified 9 combined evidence hashes and report links. Full 2,500-case coverage is not claimed.

- 2026-11-27T11:03:59-08:00: Corrected stale running rows after finalizer completion. Recomputed canonical errors independently; 81/36/85 complete PTX/SASS/GCoM runs and 44/36/45 timing-qualified pairs. Added the 44-case common comparison and the retained fused-case gap evidence. No simulation or timing mask changed.

- 2026-11-27T11:19:53-08:00: Audited the incomplete primary study. The hard 128-case expansion cap and 200-million work-estimate filter were agent-authored initial resource limits; execution stopped after that stage rather than continuing the full manifest. Primary coverage is 129/2500, plus eight supplemental pilots. Trace/qualification failures are separate from the 2371 unscheduled primary cases.

- 2026-11-27T15:14:10-08:00: Full-scope runner and capture repair integrated; 65 CPU-only checks passed, 11 existing skips. The user explicitly requires approval before GPU work, so the approval receipt is pending and no new GPU/controller job is launched. Full study remains open.

- 2026-11-27T15:38:52-08:00: Authorized CPU-only continuation. All 2500 primary identities are in scope; 129 existing PTX/SASS case outcomes are reused, leaving 4742 new CPU jobs for 2371 cases. Native timings may be absent and are never fabricated. GPU visibility is empty and an owned-context guard protects the GPU hold. GCoM has 77 completed primary outcomes and 2423 entries awaiting captured inputs; the 52 saved failed cuBLAS traces lack required function-address metadata, which affects I-cache pricing and cannot be invented. GPU approval remains pending. CPU checks: 78 passed, 11 existing skips.

- 2026-11-27T15:40:52-08:00: Verified active CPU controller and worker processes. Current completed CPU jobs: 152; remaining: 4590. GPU approval is still pending, and no full native/trace attempt markers exist. GCoM keeps 77 primary outcomes and 2423 pending GPU-input entries. CPU-only launch/isolation checks passed.

- 2026-11-27T15:47:49-08:00: Scheduled hourly status/ETA checks in this conversation, job f6cea2c2, next displayed run 16:35 PST. Session-only schedule auto-expires after seven days and will be canceled after the CPU continuation finishes. Reports distinguish finished attempts/success/failure and CPU ETA from GPU-dependent overall study completion. Initial snapshot recorded; GPU launch hold unchanged.

- 2026-11-27T16:00:18-08:00: Prepared publication of the task-owned changes. Amora simulator support integrates the newer upstream device-qualification commit while retaining standalone CUDA workloads; tests pass 112 checks (13 skips) and the wheel builds. Uni3Sim H100 profile commit 406a0e5 is published to the personal fork h100-profile branch because upstream access is read-only. Report/plan/assets are a live CPU-run snapshot; raw evidence remains Git-ignored. GPU approval hold and hourly monitoring remain active.
