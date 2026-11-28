# GCoM portable_core v2 H100 validation and correlation plan

## User request

```text
persist a plan to run the same validation/correlation experiments for portable_core v2 (both the op gates and H3 gates) with GCoM ~/wk/modern-gpu-simulator-micro-2025/ with same H100 target.
```

```text
use as any many GPUs as possible as long as it's dedicated (no loads).
```

```text
and use as many CPU as possible for GCoM's data points.
```

```text
execute the plan
```

```text
I freed some disk space, use more gcom instances.
```

## Objective and execution boundary

Evaluate the same source-available portable_core v2 cases used for the
[stock FlashGPU-Sim validation](../reports/261124-1630-flashgpu-portable-v2-report.md)
and [residency-aligned comparison](../reports/261124-2322-flashgpu-h100-residency-report.md)
through GCoM's SASS trace pipeline. Include the operation suite and both H3
suite families, retain numerical and trace admission separately, and report
absolute timing error and correlation against the same H100 80GB target SKU,
using as many matching, dedicated idle devices as are available.

Execution is authorized and has started with source/tool admission. The sealed
corpus and unchanged CUDA executable have been revalidated, and the pinned
simulator and capture tool have been rebuilt in an isolated source worktree.
Runtime capture, timing qualification and full-panel replay remain pending.
The original read-only inventory below is retained as planning provenance;
fresh execution identities live in `local_artifacts/gcom-portable-v2/execution-admission.json`.
No simulator
retuning, gate promotion, remote experiment launch or publication is part of
this plan.

## Target and source inventory

| Subject | Planning snapshot |
| --- | --- |
| Hardware pool | Up to eight local NVIDIA H100 80GB HBM3 devices, admitted only while dedicated and idle; each child exposes its assigned GPU UUID as logical device 0 |
| Historical GPU 7 reference UUID | `GPU-b356e84d-b220-a33c-dd68-3ea01ec109ea`; retained as the original reference and an anchor when dedicated |
| Architecture / driver | Compute capability 9.0 / 595.71.05 |
| Native residency | 132 SMs, 2,048 threads, 64 warps and 32 CTAs per SM |
| Requested GCoM checkout | [modern-gpu-simulator-micro-2025](../../modern-gpu-simulator-micro-2025/), `main`, clean at `122f91afac2155b80fe9f5e5d46d2817d8f09571` |
| Simulator root | [simulator-remodeled](../../modern-gpu-simulator-micro-2025/simulator-remodeled/) |
| Amora | `56749f4ed228ce1daece388195a882caf101083f`, `tpu`; existing uncommitted FlashGPU integration is preserved |
| Workload source | [portable_core v2](../../amora/benchmark_generators/portable_core/expanded/v2/) |
| Existing CUDA realization | `cuda-explicit-node-boundaries-v1`, explicit materialized node outputs |
| Corpus manifest SHA-256 | `ce380730ad0f21d1724eb454ccd80b47dd8bde029259b92350b248d62f9e29d7` |
| Existing executable SHA-256 | `2790ce58621d9f97cf6e1a6892e666fbf8ad93196b689155738e68c128ea9a3e` |
| Existing compiler / linkage | CUDA 13.3.73; SM90a cubin plus compute_90a PTX; C++17/O2, disabled FMA contraction, line info; shared CUDA runtime |

Pin fresh source and binary hashes at execution admission. Existing simulator
and tracer files are present but have not been admitted for these workloads:

- Simulator: `gpu-simulator/bin/release/accel-sim.out`, SHA-256
  `691b37486819b2db22684aeb0c729ea652aa5f5f09395106d98c6b7f69b627bd`.
- Tracer: `util/tracer_nvbit/tracer_tool/tracer_tool.so`, SHA-256
  `bc9799b5569ed573ebe49a6464b396e8e5a7d25c455c69cf6bbac5453d4a9526`.

Use the explicitly requested checkout. The GCoM rules for concurrent model
improvement and local `agent/` branches do not authorize improvement work in
this validation. If a source repair becomes necessary, record that dependency
and design it separately; do not silently substitute Accorde's internal GCoM
checkout, publish an `agent/` branch or change the model to fit these labels.

## H100 configuration contract

Select the existing Amora SKU `gcom_h100`, backed by:

- [80 GB architecture config](../../modern-gpu-simulator-micro-2025/simulator-remodeled/gpu-simulator/gpgpu-sim/configs/tested-cfgs/SM90_H100_L2_50MB_80GB/gpgpusim.config),
  SHA-256 `82d51b95a560a33bff65631f729d049afe6b33c17de3a62c137bc14b866c2572`.
- [Matching trace config](../../modern-gpu-simulator-micro-2025/simulator-remodeled/gpu-simulator/configs/tested-cfgs/SM90_H100_L2_50MB_80GB/trace.config),
  SHA-256 `a4d2d01079241db28ee3601c7a25adacdf64dd5a6656560fce893493ce77fbf1`.

This preset already specifies CC 9.0, 132 SMs, `2048:32` thread/warp slots,
32 CTA slots, 65,536 registers and 233,472 shared-memory bytes per SM. Its L2
geometry is 256 sets × 128 bytes × 20 ways × 80 subpartitions = 50 MiB.
Do not use the distinct 94 GB config; that file has different memory topology,
shared-memory capacity and cache geometry.

Freeze the shipped `1800:1800:1800:8000` clock domains for the main GCoM lane.
Convert reported core cycles using the effective 1.8 GHz model clock. The
previous FlashGPU profiles use 1.5 GHz; raw cycle counts from different model
clocks are not interchangeable. Hardware clocks stay free-running and are
recorded alongside repeat quality. A clock-matched study, if later requested,
is a separately identified protocol, not an unreported frequency edit.

GCoM's memory-controller parameters describe its model; they are not copied
from FlashGPU on the basis of matching names. Correct residency does not imply
complete H100 core/memory calibration.

The current Amora runner automatically adds `tma_real_base_addr_enable=0` and
`tma_operand_addr_tiling_enable=0` when a trace lacks the TMA base-map sidecar.
Inventory this behavior before admission. These overrides are admissible only
when the selected trace is proven to contain no TMA instructions, with both
flags recorded in effective configuration provenance. If a trace contains TMA,
require the exact sidecars rather than disabling its model. No further hidden
compatibility or timing overrides are allowed. The current preset also
enables the WaitBlock v5 exact-generation experiment globally. Its loader
requires nonempty synchronization and TMA sidecars even for an ordinary trace
without those instructions. For a statically proven absence of
SYNCS/mbarrier/TMA/asynchronous wait-block consumers, explicitly admit
`-wait_block_enable 0`, `-wait_block_exact_generation_enable 0` and its dependent
observation flag `-wait_block_late_arrival_instrument_enable 0` as additional
absent-feature compatibility flags. Record the full opcode inventory and all
five effective overrides. Reject this shortcut for any trace containing an
applicable consumer. Ordinary CTA named barriers and memory fences, including
`BAR.SYNC.DEFER_BLOCKING`, keep their stock `BARRIER_OP` model; they do not
require the asynchronous WaitBlock/TMA sidecars. Timing values and simulator
model source remain frozen.

## Corpus and gate scope

Reuse the [sealed corpus](../local_artifacts/flashgpu-portable-v2/corpus/manifest.json)
and independent oracle banks: 1,155 unique cases, 381 program hashes, 18 shapes,
30 operators, 5,215 node occurrences and 2,418 input/oracle byte banks. Retain
case/program/upstream identities and every suite/profile alias.

| Suite | Profile | Selections before alias deduplication | Execution scope |
| --- | --- | ---: | --- |
| operation-gate-v2 | reference | 720 | Pinned operation-registry programs |
| operation-gate-v2 | development | 288 | Separately identified development source |
| operation-gate-v2 | smoke | 10 | Bounded witnesses |
| mechanism-controls | reference | 288 | Aliases of operation development; execute once |
| mechanism-controls | development | 9 | Diagnostic controls |
| mechanism-controls | smoke | 3 | Diagnostic witnesses |
| h3-semantics | development | 40 | Semantic, indexing, stride/offset and collision controls |
| h3-semantics | smoke | 7 | Seven H3 semantic witnesses |
| h3-programs | development | 84 | Directed pairs, topology/shape controls and fork/chain witnesses |
| h3-programs | smoke | 6 | Bounded program witnesses |
| h3-semantics | exact reference | 0 | `blocked_missing_source` |
| h3-programs | exact reference | 0 | `blocked_missing_source` |

The two exact historical H3 reference profiles require approved per-case
exports from their source owner. Development fixtures do not close those
rows. If exports become available, create a new sealed cohort with a source
crosswalk and separate results; do not retrofit it into the existing 1,155-case
manifest or infer historical input bytes from similar family names.

Suite membership identifies workload coverage. Formal operation/H3 gate
acceptance additionally requires approved split assignments and frozen gate
criteria; the portable source metadata does not itself assign a scientific
train/test split. Do not invent a gate cutoff from a timing result, count
controls as historical reference validation or interpret correlation as gate
promotion. Reference/holdout membership, if supplied, must remain disjoint at
the canonical program group level across shapes and aliases.

## Numerical qualification and realization identity

Reuse the prior 1,505 required CPU checks after source/case/oracle validation.
The three previous GPU 7 native runs remain device-specific historical evidence;
reuse them only for a correctly identified GPU 7 comparison with matching
executable and protocol. Other pool devices require their own uninstrumented
native qualification and timing labels. Keep all three repeat processes for a
case/protocol on the same assigned physical GPU; never relabel GPU 7 samples as
measurements from another card. For new tracing runs, require accepted native
correctness on the assigned device before tracing and successful driver checks
after capture. Every node output and unchanged input storage remain checked against
the independent v2 oracle, including FP16 temporary rounding, int32 tuple ports,
top-k tie behavior, scatter collisions and physical backing strides/offsets.

GCoM consumes a hardware execution trace. Its admission verdict means the
captured instruction/control/memory stream was accepted for timing replay;
it is not a new simulator-side arithmetic/oracle pass. Keep `native_correct`,
`trace_complete`, `replay_admitted` and `timing_qualified` as separate fields.

Attempt the existing hashed CUDA executable first. GCoM documents CUDA 11.4 /
12.8 and static-library applications, while the existing driver uses CUDA 13.3
and shared cudart. Pilot NVBit module capture, loaded-cubin/static metadata and
opcode/control-bit decoding before claiming compatibility. Do not rebuild it
implicitly through the generic `trace_probe` helper, which defaults to a
different compilation contract.

If a static-runtime or older-toolkit build is necessary, name and hash that
realization separately and qualify its full native numerical/timing panel.
Previous timing labels cannot be transferred to a different executable. Keep
the unchanged-binary comparison as explicitly unavailable if it cannot trace.
The half-identity contraction is still scaling; this panel does not establish
dense GEMM, Tensor Core, TMA, WGMMA or DSM coverage.

## Trace capture and timing admission

Capture one case at a time per dedicated GPU, with all admitted pool devices
working in parallel. Preserve the exact input banks, node order,
grid/block shapes and launch-to-case mapping; execute each already-unrolled
node once. The frozen pilot has 39 cases: the previous 35 operator/layout
witnesses plus four missing large-shape and H3 graph/state witnesses.
The exact selection is recorded in `local_artifacts/gcom-portable-v2/witness-selection.json`.
The two-case pipeline bootstrap precedes that panel. Native common anchors add
the largest shape and largest graph work to indices 0 and 13; these hardware
repeats do not create duplicate simulator jobs.

For a warm protocol, capture the actual validation/warmup prefix and one target
program execution, not just a suffix with an assumed warm cache. The current
driver performs a validation graph, the requested warmup graphs, then samples;
with one sample and three warmups, expect five executions of each graph node.
Validate the actual application launch IDs instead of assuming no extra kernels.
Label the target program separately so prefix work is simulated for state but
excluded from the scored duration. If a cold protocol is used instead, acquire
matching cold hardware labels and identify the different cohort.

Use the tracer's inclusive `DYNAMIC_KERNEL_LIMIT_START/END` controls or explicit
profiler markers after proving their behavior in the pilot. `ACTIVE_FROM_START=0`
disables launch-range controls; do not combine them as if both were effective.
Discovery-only tracing is available for checking the launch list without full
instruction capture. Require complete `dynamic_trace.pb`,
`extra_info/enhanced_execution_info.json`, all referenced threadblock records,
captured cubin/static metadata and any genuinely required sidecars.

The current tracer/source uses enhanced protobuf traces, despite older README
examples describing `.traceg` and `kernelslist.g`. Confirm the installed binary
and parser agree on this format; do not pass an incomplete or ambiguous trace
directory through a first-candidate fallback. Preserve active masks, control
bits, function/PC identity, memory addresses and every selected CTA/warp; no
sampling or truncation may be hidden behind a successful process exit.

| Timing lane | Hardware reference | Admission and interpretation |
| --- | --- | --- |
| Original CUDA stream program span | Device-matched three-run event labels; prior GPU 7 samples only for the GPU 7 historical control | Admit only if GCoM exposes a target-window timeline across all nodes with an explicit modeled launch/gap policy. Never call a sum of kernel bodies the same estimand. |
| Device kernel duration / sum of target kernel bodies | Fresh uninstrumented CUPTI or equivalent timestamps on each assigned dedicated H100 | A separate, directly trace-replayable correlation lane when the original span cannot be modeled. Recompute all timing eligibility; do not inherit the old 1,041/114 split. |
| Short-case H3 batched protocol | Fresh same-protocol labels on the assigned H100 for a fixed batch of program repetitions | A separate amortized span or body-sum result for controls too short for the original gate. No substitution into the single-invocation scores. |
| Optional instruction/counter diagnostics | Separately collected untraced hardware counters | Use only denominator-matched, documented mappings and label direct/proportional/proxy fidelity. Not a replacement for timing or numerical qualification. |

Prove the program-span lane during the pilot: account for first/last boundaries,
configured launch latency, serialization, modeled inter-node delays and prefix
exclusion. Do not inject measured native gaps, oracle time, or native labels into
the prediction. If the replay exposes only body cycles, keep the original-span
comparison `not_comparable` and report the body lane separately. Optional
FlashGPU/body comparisons require new matched body measurements; existing
program-span numbers cannot be pooled with them.

Obtain performance labels without NVBit instruction instrumentation. Native
correctness accompanies the trace, but instrumented wall time is not H100
ground truth. For a new timing lane, use three independent native processes,
at least 30 samples per case, the same warmup/cache protocol, median at least
10 microseconds and relative MAD at most 5% within and across repeat medians.
Report short/noisy rows as inconclusive. Per-kernel diagnostics may use an
independent declared noise floor; they must not relax the program-level gate.

The batch lane selects source-available H3 fixtures with at most eight nodes
and at most 16,384 M × N elements, plus any H3 case below the native 10-microsecond
floor. These source size bounds cover small semantic controls, directed pairs
and bounded fork/chain witnesses even when host submission makes their
single-program event spans exceed the floor. Selection does not use simulator
error, and single-program eligibility remains unchanged.

For short H3 controls and directed program pairs, predeclare a secondary batch
protocol in the pilot: 64 independent executions of the prepared program on
one stream per measured batch, three process runs and at least 30 batch samples
each, with batch median at least 100 microseconds and relative MAD at most 5%.
Check oracle outputs and unchanged inputs before/after; each repetition starts
from the same external inputs and executes the already-unrolled graph once.
Capture/replay the exact batch with its warm-state prefix and label its total
and amortized result (`batch_duration / 64`). If the pilot cannot meet the
declared resolution or resource gates, record that rather than selecting a
batch size after observing simulator error. A revised batch size must be
frozen before full collection and creates a new protocol identity.

This keeps short-case H3 coverage visible without weakening the original
single-program timing gate. Batch spans still require a corresponding modeled
timeline; otherwise correlate measured and simulated batch kernel-body sums.
Single-invocation, body and amortized batch results have separate cohorts,
correlations and legends. Repeated kernels inside a recorded batch are one
declared trace input, not repeated executions of an identical simulator job.

## Dedicated GPU pool and maximum CPU throughput

### GPU admission and lease lifecycle

The additional resource instructions supersede the earlier GPU-7-only schedule.
Use every available matching H100 80GB card on this machine, up to all eight,
subject to dedicated-device and CPU/RAM/storage admission. GPU 7 remains the
historical anchor, not a reason to leave other eligible cards unused.

A candidate must match the target SKU, CC, driver and full-device capabilities,
with MIG disabled. Require at least 30 seconds of idle observation before a
lease: no foreign compute or graphics process/context, no GPU or memory activity,
and no unexplained memory allocation beyond its driver bookkeeping baseline.
Zero utilization alone is insufficient; a retained context is a competing load.
Use process accounting and GPU telemetry throughout each measurement/capture
interval, allowing only the worker's recorded process tree once it starts.

Acquire an exclusive per-UUID scheduler/coordination lease, then launch one
hardware worker per leased GPU. Use an enforceable scheduler or permitted
exclusive-process allocation when available. Advisory locks coordinate this
campaign but do not guarantee exclusion of unrelated jobs; record the actual
mechanism and qualify only intervals verified free of foreign load. Never
terminate another user's workload to free a device. If a foreign context appears,
quarantine that interval, stop assigning work to the card and reject contaminated
labels/traces. Retry hardware acquisition only after a fresh lease, preserving
rejected receipts; do not silently accept the overlap or substitute a zero.

Each subprocess sets its own `CUDA_VISIBLE_DEVICES=<assigned GPU UUID>` and
verifies the driver-reported UUID, logical device 0, SM count and architecture.
Pass a separate environment dictionary to each child; do not race concurrent
workers by changing process-global environment variables. Native timing and
NVBit tracing are serial within a GPU, while different GPUs operate concurrently.
Use isolated case/device/protocol output paths and record lease, PID, assignment,
idle observations and trace/native input identities in every result.

Freeze deterministic, balanced case/program-group assignments across suite,
shape, dtype and graph strata. Do not put all H3 cases on one card and all op
cases on another. All repeats for a case stay on its assigned GPU; migration
requires a new device-specific repeat set and explicit provenance. Run a small
common native anchor set across admitted devices to expose hardware variation;
these are hardware repeats, not duplicate GCoM replays.

Report per-GPU errors and native distributions before pooling. Pool only matching
SKU/protocol, within-device qualified rows. If a common anchor's per-device
median differs by more than 5% from the cross-device median, use device-stratified
scores as primary and label the combined score descriptive. Do not fit per-card
model corrections. Historical FlashGPU comparisons must identify their fixed
GPU 7 reference separately; newly collected GPU-pool labels cannot be described
as those same historical hardware measurements.

The [GPU snapshot](../local_artifacts/gcom-portable-v2/gpu-pool-planning-snapshot.json)
is read-only availability evidence, not a reservation. Availability already
changed during inspection, so neither GPU indices nor worker count are frozen
from that snapshot. Re-admit devices before dispatch; if all are busy, keep
hardware work queued while CPU replay of existing traces continues.

### CPU replay allocation

Use as much available CPU capacity as possible for independent GCoM data points;
the earlier four-process starting schedule is not a permanent cap. This host
currently allows 192 logical CPUs (96 physical cores, two NUMA nodes, SMT=2),
with no CPU quota in the inspected cgroup ancestry. Its present simulator links
OpenMP (`libgomp`). Recheck affinity, cgroup quota, foreign allocations and
resource pressure before launching, rather than treating 192 as a guaranteed
reservation.

Keep hardware acquisition and CPU replay budgets separate. Reserve a physical
host core, including its SMT sibling, per active GPU hardware worker and use
NUMA-appropriate affinity. GCoM replay must not run on those acquisition cores.
Protect uninstrumented timing windows from heavy CPU/IO contention; delay or
throttle replay when isolation is insufficient. This matters because the
original program-span metric includes host submission gaps. Do not call labels
collected under materially different host contention the old timing protocol.

Let `C` be the effective usable logical-CPU budget after quotas, actual foreign
allocations and active acquisition reservations. Allocate disjoint worker CPU
sets so `sum(OMP threads per replay) <= C`. Clear inherited global pinning or
scope it within each worker's set; never pin every process to the same cores.
Prefer NUMA-local memory and record CPU set, NUMA node, OpenMP count and SMT use.
Replay children clear tracer injection/preload and use no physical GPU; hardware
leases are released when collection finishes rather than held for CPU simulation.

With the full 192-CPU budget free, a starting maximum-fill layout is **24
simulations × 8 OpenMP threads**. With eight active hardware workers reserving
16 logical CPUs, the corresponding ceiling is **22 × 8 = 176 replay threads**,
before any other constraints. These are capacity examples, not promises of
linear speedup or mandatory allocations. Fill smaller remainders with smaller
worker thread counts if qualified, and release acquisition reservations once
that phase finishes.

Choose per-point thread count and process count using wall-time throughput,
actual utilization, memory and IO measured on disjoint representative pilot
inputs. Use existing validated OpenMP behavior; do not rerun identical traces
merely to benchmark CPU layouts. Large points may merit more threads while many
small independent points may favor more processes. New schemas/plots reparse
saved data instead of generating more simulator repetitions.

The admitted process count is bounded jointly by the CPU budget, available
unique points, measured worst-case RSS with a safety margin, capture memory and
trace/log disk allowance. The latest capacity check has about 616 GiB available
RAM and only 32 GiB free on the workspace filesystem; these are observations,
not allocations. Size the pilot and maintain a disk reserve before increasing
workers. Use a bounded producer/consumer queue, verified lossless archives and
backpressure; adding CPU workers must not cause OOM, swap thrashing, incomplete
traces or disk exhaustion. Grow toward the available capacity as constraints
permit, and shrink on new resource pressure without killing unrelated jobs or
creating duplicate replay ownership.

Record admission decisions and unique trace/config/source keys in the tracker.
One key has one CPU-job owner; op/H3 aliases and multiple figure views reuse that
single result. More CPUs accelerate distinct data points, not artificial repeats
of an identical deterministic simulation.

## Execution sequence and tracker gates

| Phase | Work | Required exit evidence |
| --- | --- | --- |
| Source and reuse admission | Pin the requested checkout, exact binaries, configs, corpus, approved profile scope and existing evidence index | Complete mandatory identities; no best-effort `None` fields admitted |
| Adapter preparation | Extend Amora to trace an existing v2 executable, bind case/node launch IDs, stream receipts and preserve per-kernel stats | Integrity and CLI checks; no changes to mathematical workload sources |
| Native and trace pilot | Qualify the unchanged binary or separately named compatible build; capture operator/H3 witnesses | Correctness, cubin/opcode admission, complete trace structure and target/prefix mapping |
| Timing-contract pilot | Establish the original-span lane or explicitly select the distinct body lane; estimate trace disk/RAM/time cost | Frozen estimand, cache protocol, label quality, parser and concurrency budget |
| Full source-available panel | Capture/reuse one qualified trace per exact case/protocol and replay once per unique fingerprint | All 1,155 cases have outcomes and profile aliases; failed/missing rows remain visible |
| Analysis and closeout | Compute matched errors, correlations and grouped coverage; inspect large residuals | Recomputable data, figures, hashes, no unaccounted jobs and explicit source-blocked references |

Native acquisition and tracing use all dedicated admitted GPU leases, with
one hardware worker per card. CPU replay scales independently toward the
available CPU/RAM/IO budget using the allocation rule above; it is not capped
at four processes. Freeze the adaptive rule and per-point limits from the pilot
before full scheduling. The previous FlashGPU 16-worker policy is not a GCoM
performance guarantee.
Suggested initial bounds are 1,800 seconds for a traced case and 7,200 seconds
for a replay, subject to an explicit pilot revision before full scheduling.
Size trace storage before launch, retain a reserve, and use a bounded capture /
replay pipeline with verified lossless archives if the raw corpus does not fit.
Do not delete the only trace copy or silently switch to another host/storage.

Respect GCoM's rule against repeated simulations of identical source/config/input.
Key reuse by simulator and linked-library hashes, effective config/overrides,
complete trace/static/sidecar hashes and execution-affecting arguments. Store
case aliases, scored-window selection and metric interpretation versions
separately. Reparse retained raw statistics for new summaries; a parser or
metric-schema change alone does not justify another simulator execution.
Execute each qualified input key once, including
when it serves both op and H3 summaries. Hardware timing repeats are distinct
from deterministic simulator reruns. A timeout or crash is retained as an
execution outcome, not numerical failure; there are no automatic identical
retries. Recovery requires a documented changed-condition protocol or valid
checkpoint continuation and must not duplicate active/queued work.

Current Amora `parse_stats` keeps only the last value per key. Preserve every
kernel/window record for this multi-node corpus, validate count/order against
the trace and avoid treating the last kernel's `gpu_sim_cycle` as a program
total. Require successful exit, final completion marker, positive finite cycles
and full selected launch accounting. Partial `missing_stat`, unknown opcode,
missing sidecar, timeout and native failure states remain separate.

## Analysis and scientific reporting

Compute APE as `100 × |prediction / native − 1|` on eligible matched rows;
report MdAPE, linearly interpolated P95 APE, signed median bias, sample count
and coverage denominator. Global weights are one per unique case, not aliases
or repeated timing samples. For each accepted estimand, also report Pearson
correlation, Spearman rank correlation and log-duration correlation where
defined. Constant or too-small groups return `not_evaluable`, not a perfect
correlation. Good ranking does not establish absolute timing accuracy.

Provide separate tables for operation reference/development, H3 semantics,
H3 programs, diagnostic controls and source-blocked exact references. Add
shape M × N, family, contained-operator, graph size/topology, state-edge,
unroll and dtype/physical-layout breakdowns. Operator-containing groups overlap
and measure whole programs. Include native/simulator scatter, APE distributions
and MdAPE/P95 plots by operator and shape, with missing cohorts labeled.

Use the same paired case intersection for any comparison with FlashGPU PTX or
SASS and disclose realization, timing, cache/prefix, config and physical-device
differences. Keep the fixed historical GPU 7 reference comparison separate from
fresh per-device GPU-pool labels; do not transfer hardware timings between cards.
Keep SASS's previously three-case timing intersection distinct from the full
PTX panel. Preserve outliers and trace-admission failures, and examine residuals
by graph/shape rather than adjusting the simulator after observing errors.

No new accuracy cutoff or predictor training is authorized. Formal gate reports
require the original approved admission rules and split manifest; otherwise
publish descriptive validation/correlation only and retain `gate_not_evaluated`.

## Ownership and deliverables

- Workload definitions stay unchanged in Amora's portable package.
- Reusable capture/replay support lives under
  [amora/backends/gcom_cuda](../../amora/amora/backends/gcom_cuda/) and campaign
  code under [amora/benchmarking/portable_v2](../../amora/amora/benchmarking/portable_v2/).
  The existing generic GCoM benchmark adapter targets another replay contract;
  it is not assumed to support this v2 corpus already.
- This project owns the plan and [tracker](tracker/261125-1652-gcom-portable-v2-h100.md).
- Raw evidence, trace snapshots, manifests, native samples and logs belong under
  Git-ignored `local_artifacts/gcom-portable-v2/`; runtime state does not belong
  in the simulator or shared workload package.
- The final timestamped report belongs under `reports/`, with PNG/SVG exports in
  `reports/assets/`. Include machine-readable case, launch, coverage, gate-scope
  and correlation ledgers, exact source/tool/config hashes and reproduction
  commands verified against the implementation.

For initial read-only capability inspection, from the Amora checkout, set
`GCOM_GPU_UUID` to an eligible device selected by the scheduler:

```bash
GCOM_ROOT=../modern-gpu-simulator-micro-2025/simulator-remodeled \
CUDA_VISIBLE_DEVICES="${GCOM_GPU_UUID:?set a dedicated GPU UUID first}" PYTHONPATH=. \
python3 -m amora gcom_cuda inspect-capabilities
```

This is discovery, not a portable v2 execution command. The v2 GCoM harness and
its CLI must be implemented and checked before a launch recipe is published.

Cite GCoM's MICRO 2025 architecture paper, its simulation-parallelization work
and Accel-Sim according to the [GCoM README](../../modern-gpu-simulator-micro-2025/README.md).

## Revision History

- 2026-11-25 16:52 -0800: Created the plan from the existing frozen portable corpus, GCoM source/config inventory and Amora contracts; preserved the exact request, GPU 7 target, op/H3 source scope, deterministic-reuse rule and timing comparability gates. Execution remains planned.
- 2026-11-25 16:59 -0800: Predeclared a separate short-case H3 batch protocol and clarified that interpretation changes reuse raw simulator results instead of rerunning identical inputs.
- 2026-11-25 17:07 -0800: Adopted the user's dedicated multi-GPU and maximum-CPU resource instructions. Added live leases, per-device native references, host-timing isolation, disjoint OpenMP CPU budgets and RAM/IO backpressure; no experiment launch is performed by this planning revision.
- 2026-11-25 17:54 -0800: Began authorized execution. Revalidated the 1,155-case corpus and unchanged CUDA binary; rebuilt GCoM at the pinned source commit with CUDA 12.8/GCC 11/protobuf 3.21.12. Rebuilt its tracer using official NVBit 1.8.2 for driver 595, with only the documented channel/thread API rename recorded in `nvbit-compatibility.patch`. Built a separate CUPTI activity collector. No model timing parameter or workload arithmetic changed. The runtime pilot is queued for a 30-second dedicated H100 window; previous observation windows admitted no device. Corrected NVML memory accounting to exclude driver-reserved memory from user allocation. All build failures and the schema-import bootstrap failure are preserved; runtime qualification is still required.
- 2026-11-25 18:25 -0800: Froze the 39-case witness panel, balanced GPU assignments, NUMA-local acquisition cores, disjoint 8-thread replay slots and CPU/RAM/disk admission. Added verified lossless trace/log archives and an 8 GiB disk reserve. Source review fixes the scored GCoM estimand as sequential target service including its shipped 1,500-cycle CTA launch delay and zero modeled host gaps, converted at 1.8 GHz; CUPTI body timestamps are diagnostics, not a relabeling of service cycles. Built a separately hashed 64-repetition H3 driver with unchanged kernel/oracle code. Native three-run oracle qualification and three complete 952-kernel CUPTI captures succeeded on dedicated GPU 0. Fixed the CUPTI iterator's required null initialization, preserving its failed first attempt. NVBit then failed before the first kernel with binary version zero, including under eager loading. A capture-only real Driver API status diagnostic is queued; no GCoM replay or accuracy score exists yet.
- 2026-11-25 18:35 -0800: Qualified the unchanged CUDA 13.3 chain case after resolving its documented context-independent CUkernel launch handles to CUfunction. The complete trace has 15 launches, 120 CTAs, 30,720 dynamic warp instructions and three captured cubin functions. The first GCoM attempt aborted before the first kernel because the shipped preset globally requires WaitBlock synchronization/TMA sidecars. Preserved that unique failed key and admitted a changed configuration disabling only those absent-feature consumers after full static opcode proof; no timing parameter was fitted and no identical replay is repeated. Tested loader modes separately: LD_PRELOAD alone produced native results but no trace on this shared-runtime executable, so CUDA injection alone is under qualification as the documented alternate loader.
- 2026-11-25 18:38 -0800: CUDA injection alone qualified the affine control with five complete launches. Source-audited the full dependency checks for WaitBlock: its late-arrival observation flag must also be disabled when exact-generation mode is off. Recorded the complete five-override absent-feature contract before the next distinct replay key; retained both prior pre-kernel aborts. No timing result has been used to select any setting.
- 2026-11-25 19:06 -0800: All 39 native/trace witnesses qualified on five dedicated H100s. Fifteen replayed successfully; 24 were blocked before any simulator invocation by an overly broad Python guard that included ordinary `BAR.SYNC.DEFER_BLOCKING`. Source inspection confirms its stock named-barrier path is separate from `MBARRIER_OP`/WaitBlock; narrowed the guard to actual asynchronous consumers and launched those 24 existing traces for their first replay. Added regression coverage for ordinary barriers/fences and for refusal to disable real asynchronous consumers. Replay instruction-stat files are retained separately before verified raw-trace cleanup.
- 2026-11-25 19:25 -0800: Pilot filled 190 admitted logical CPUs at dispatch; larger traces took roughly 10–14 minutes with eight threads. Froze a dynamic disjoint NUMA CPU pool before full scheduling: request 32 threads for traces at least 16 MiB and eight for smaller inputs, process larger inputs first and fill smaller remainders without exceeding the admitted budget. This changes CPU scheduling only and does not rerun existing keys. Added the source size rule for batch64 H3 controls so both bounded semantics and directed/fork/chain fixtures are included independently of simulator error. Fixed replay auxiliary-output retention before lossless raw cleanup. The adapter/resource tests pass 64 checks.
- 2026-11-26 08:22 -0800: Applied the user's request to increase independent GCoM instances after disk recovery. Prepared a reloadable project-owned resource policy with four OpenMP threads per point, up to 48 instances, and up to 64 staged cases per wave, bounded by actual CPU quota/foreign load, RAM and observed worst-case trace size. All 67 resource/admission tests pass. A kernel-notified watcher will replace the old controller only during a fresh GPU-observation window with no active capture or simulation; all completed/failed results and immutable keys are reused. No model timing parameter, corpus input or hardware reference is changed. The current wave continues before the handoff.
