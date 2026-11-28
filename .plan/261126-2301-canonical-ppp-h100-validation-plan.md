# Canonical PPP H100 validation plan

## User request

```text
Work on the Canonical PPP kernel results for both simulators.
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

## Objective

Produce retained H100 hardware and FlashGPU-Sim/GCoM results for AMORA's nine canonical PPP families and update the combined canonical/portable report. Preserve the canonical CUDA templates and measurement semantics; do not replace cuBLAS or bounded kernels with different algorithms to obtain simulator support.

## Scope and execution contract

Seal the deterministic `h100_2500` manifest with seed 20260717 and source hashes in Git-ignored `local_artifacts/canonical-ppp-h100/`. Add and run a minimum-work shape per family from the full canonical source candidate set (these pilot shapes may lie outside the standard 2,500-case subset); inspect native launch accounting, simulator support, trace storage and resource cost before admitting additional shapes. Keep sampled attention/MoE and fused-component estimates labeled. Native completion is distinct from numerical correctness because current canonical templates do not supply independent output/oracle validation.

Use the same canonical source, build flags, arguments, input initialization and launch window within each comparison. Capture native per-launch CUPTI timestamps and match complete simulator launch records. Report any unavoidable binary/compiler/runtime/prefix difference explicitly. PTX and SASS support outcomes remain distinct. Retain unsupported features, timeouts and incomplete traces as outcomes, not missing zero-error values.

Freeze the previously validated H100 residency-aligned FlashGPU model and GCoM H100 model (132 SMs, 2048 threads, 64 warps, 32 CTAs per SM). Do not fit timing settings. Source-proof any necessary absent-feature overrides case by case; real asynchronous consumers must retain their sidecars or be reported unsupported.

Use as many dedicated idle H100s as qualify under continuous GPU UUID/process/load observation. Never kill foreign jobs. Use disjoint NUMA CPU sets and up to 48 independent GCoM points with four threads each when actual CPU quota, foreign workload, RAM and storage permit. Preserve at least 16 GiB disk reserve and apply backpressure based on observed trace sizes. Reuse exact source/config/input executions, including failed outcomes; no identical simulator retries. Lossless archives require hash verification before removing redundant originals.

Native scoring requires three complete dedicated CUPTI captures, a median kernel span of at least 10 microseconds and process-repeat CV at most 10 percent. Preserve shorter/variable observations as diagnostics. All lanes use the same CUDA 13.3 shared-runtime binary with `AMORA_GCOM_TRACE`; warmup and embedding L2 flush are consistently compiled out. Native span and body sums remain separate.

The initial 128-case stage and 200-million work-estimate cutoff are historical. The default `execute` action now resumes all 2,500 primary identities through a native-first full runner, without either cutoff. The previous stage remains available only as `execute-bounded`. Existing results and unique simulator execution keys are retained.

GPU measurements and tracing are currently **waiting for explicit user approval** because a higher-priority task may use the devices. Approval is not inferred from idle observations or elapsed time. `full-study/gpu-launch-approval.json` remains pending; no new controller or GPU jobs have been launched.

## Work sequence

| Stage | Acceptance |
| --- | --- |
| Inventory and sealing | Nine family contracts, immutable manifest, source/config/tool hashes, explicit pilot selection |
| Adapter preparation | Native launch capture, canonical binary build, simulator selection/count parsing and immutable receipts verified |
| Pilot execution/review | Every family has a native and simulator support outcome; supported predictions include complete windows |
| Expansion | Deterministic admitted cohorts in increasing observed cost; bounded CPU/storage use and no repeated simulation keys |
| Analysis/report | MdAPE, P95 APE, signed bias/correlation, family/shape coverage, unsupported/failure reasons and timing limitations |

The existing generic AMORA detail workflow requires rank comparisons and explicit review markers before promotion. If reused, create those markers only after reviewing complete retained comparisons. This campaign's equivalent pilot review must be persisted before expansion; no scientific gate threshold is invented.

## Ownership and deliverables

Reusable code belongs under AMORA `amora/benchmarking/` and backend adapters. This project owns runtime evidence, this plan, the [tracker](tracker/261126-2301-canonical-ppp-h100-validation.md) and timestamped reports under `reports/` with figures under `reports/assets/`. Update the combined report with canonical availability/metrics without pooling incompatible clocks, native references or execution semantics.

## Full-study continuation

The new `amora/benchmarking/canonical_ppp/full_campaign.py` first acquires the 2,371 unmeasured primary cases, preserving the 129 measured primary cases and eight supplemental pilots. Native acquisition is separated from tracing so expensive trace construction cannot hide native coverage.

After approval, source-matched traces use the verified Std ELF adapter for every capture, including previously unadmitted cuBLAS inputs. Existing completed or failed simulator executions remain cached; only inputs never admitted to GCoM are repaired/captured for their first replay. Native references remain tied to their original GPU UUID.

Trace staging uses estimated bytes from observed trace sizes and actual launch geometry. These estimates control in-flight work only. Large/unknown cases get one monitored capture rather than permanent exclusion by score. The documented 50,000-instruction incremental flush mode is recorded and its GPU transport qualification remains part of the first admitted trace review.

The full pipeline preserves a 16 GiB disk reserve and 64 GiB host-memory reserve, limits models by observed CPU quota/busy cores/RAM, and uses disjoint NUMA sets with up to 48 independent four-thread points. Capture and replay failures remain explicit. Every primary case receives an outcome; full case coverage does not imply 2,500 successful timing pairs. Completion is based on the remaining queue, not the number of cases in an initial sample.

Approval may restrict GPU UUIDs. The runner filters devices to that scope and keeps references bound to the measured card. Cases needing an unapproved reference GPU remain pending; the approved pool is never expanded automatically. No GPU job is launched before approval.

CPU-only verification passes 65 checks, with 11 existing skips. Tests cover all-manifest selection, continuation of the remaining 2,371 cases, prior-result reuse, byte-based wave admission, large-case handling, approval denial/scope and owned-process RAM protection. Source/tool/model parameters and native timing qualification rules are unchanged.

## Revision History

- 2026-11-26T23:01:53-08:00: Started the authorized canonical campaign; recorded all nine families, bounded pilot, unchanged workload/model contract, dedicated resource admission and non-repetition policy.

- 2026-11-26T23:07:35-08:00: Sealed 2,500 primary cases plus minimum-work source pilots; compiled all nine canonical binaries. Froze three cold-process CUPTI references and source-cost/128-case expansion bounds before observing errors. Admission/resource checks: 51 passed, 11 existing skips. Family pilot execution started.

- 2026-11-26T23:13:16-08:00: Source-audited the tracer variant suffix and FlashGPU atexit completion marker; revalidated five complete saved traces and six successful FlashGPU outputs. No simulation repeated. Retained the MoE foreign-context acquisition and admitted a fresh dedicated interval. Shared checks: 52 passed, 11 existing skips. First GCoM replays and reviewed expansion continue.

- 2026-11-26T23:18:18-08:00: Five GCoM source-pilot replays completed. Admitted a capture-only cuBLAS Std ELF recovery: CUDA 13.3 nvdisasm accepts the retained cubins rejected by cuobjdump; an external text adapter preserves every PC/opcode/128-bit instruction and verifies encodings against original ELF bytes (63,120 instructions checked offline). Adapter/admission tests: 11 passed. Same binary, native references and timing model; no prior cuBLAS simulator execution is repeated.

- 2026-11-26T23:26:14-08:00: Retained all failed Std ELF captures. Corrected nvdisasm-only branch framing/annotations and verified the exact exported cubin bytes plus all 63,120 instructions through the original GCoM CPU metadata parser before another changed-tool capture. No GCoM simulation has run for these cuBLAS cases. The MoE PTX pilot completed; its GCoM pilot remains active.

- 2026-11-26T23:30:17-08:00: Pilot resource review admits up to 64 staged cases and 48 independent four-thread points, bounded by the observed maximum trace size and 16 GiB reserve. An inotify watcher will hand off the controller only at an expansion boundary with no descendants, GPU leases or unfinished model keys; no capture/model is interrupted or repeated. It will finalize figures, reports and link/hash verification after execution. The original 16-case waves remain historical scheduling evidence.

- 2026-11-27T15:14:10-08:00: Removed fixed full-study case/work cutoffs, implemented native-first full-manifest continuation, integrated the verified disassembly adapter, and added CPU/RAM/disk admission plus explicit GPU approval gates. CPU-only regression: 65 passed, 11 existing skips. GPU launch is pending the user's approval; no GPU benchmark or trace was started.

- 2026-11-27T15:38:52-08:00: Authorized CPU-only continuation. All 2500 primary identities are in scope; 129 existing PTX/SASS case outcomes are reused, leaving 4742 new CPU jobs for 2371 cases. Native timings may be absent and are never fabricated. GPU visibility is empty and an owned-context guard protects the GPU hold. GCoM has 77 completed primary outcomes and 2423 entries awaiting captured inputs; the 52 saved failed cuBLAS traces lack required function-address metadata, which affects I-cache pricing and cannot be invented. GPU approval remains pending. CPU checks: 78 passed, 11 existing skips.
