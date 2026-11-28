# FlashGPU-Sim and GCoM H100 combined report plan

## User request

```text
also generate a report with flashGPU-Sim and GCOM results for both canonical and portable_core v2 kernels. if canonical kernels results are not avaiable, let me know.
```

## Scope

Reparse existing evidence for both simulators against the H100. Verify canonical
`ppp_canonical` cohort availability and distinguish the 36 diagnostic probes and
instruction microbenchmarks from canonical application kernels. Reuse the sealed
portable_core v2 corpus, report each lane's coverage/eligibility and compare the
intersection of identical case/program hashes while keeping each lane's physical
native reference and timing policy explicit. Preserve stock/aligned PTX and the
small SASS intersection separately. No new simulation, retuning or kernel launch.

## Work and deliverables

- Inventory compact ledgers and identify missing canonical cohorts.
- Recompute independent and common-cohort timing metrics from saved rows.
- Generate combined operator/shape figures in the requested stock report layout.
- Write the report to reports/, figures to reports/assets/, and machine-readable
  evidence/hashes to Git-ignored local_artifacts/flashgpu-gcom-comparison/.
- Verify joined identities, arithmetic, source coverage and all report links.

## Revision History

- 2026-11-26T21:30:17-08:00: Created the report plan; portable evidence exists for both simulators, canonical cohort availability is under verification.
