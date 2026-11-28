# Uni3Sim Forward H100 integration tracker

## User request

```text
investigate the archprsim in ~/wk/uni3sim/, especially the 'Forward' model. 
write a plan to include forward model in addition to the FlashGPU and GCoM sims for all the tested kernels.
```

## Work ledger

| Work | Status |
| --- | --- |
| Forward architecture/input/support investigation | Completed; H100 registry/profile absent, helper build absent, source contracts identified |
| Full tested-cohort inventory and mapping | Completed planning snapshot: 1155 portable, 53 batch, 137 executed canonical, 54 feature runs and 43 stock runs |
| Integration plan and verification | Completed; see linked plan and planning inventory |
| CPU environment and H100 onboarding | Initial H100 ID/profile/loader complete as H200-based compatibility; native mechanism calibration and full suite environment remain planned |
| Source/launch/cache/window adapters | Planned |
| Pilot/freeze/full Forward prediction | Planned |
| All-model comparison and report refresh | Planned |

## Evidence

- [Integration plan](../261127-0024-uni3sim-forward-h100-validation-plan.md)
- [Hashed planning inventory](../../local_artifacts/uni3sim-forward-h100/planning-inventory.json)

## Revision History

- 2026-11-27T00:24:05-08:00: Opened the planned Forward lane after source inspection and exact tested-cohort inventory. Preserved H100, op/H3/batch/canonical/feature scope and unsupported architecture/contract dispositions. Existing canonical execution is independent; no Forward run is started.

- 2026-11-27T00:28:11-08:00: Plan verification passed: 30 operators, 9 canonical families, 22 links, 18 subject-source hashes and 8 immutable evidence snapshots. Source checkout and existing campaigns were not modified. All execution stages remain planned.

- 2026-11-27T01:07:28-08:00: User requested: `adapt the nvidia_h200 to create nvidia_h100 profile.` Implemented and validated the H100 derived profile and registry. HBM3/L2/PCIe/residency limits are H100; inherited services remain compatibility evidence. Benchmark kernel adapters and native H100 calibration remain planned.
