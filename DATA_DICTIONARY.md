# Data Dictionary & Schema Reference

## 1. Repository Layout

- `README.md`: Principal comparative benchmark tables (strictly separating `T1..T6` Timing-Isolation tables from Pooled 3-QPU Interleaved Quality tables), Multi-Compiler `{SABRE O3, TKET, Prometheus}` Joint `(Compiler*, QPU*)` Meta-Selection evaluation, classical CPU/GPU acceleration benchmarks, 19-job hardware ledger, and Quantum OS architectural roadmap.
- `PUBLIC_DISCLOSURE.md`: Intellectual property boundary and empirical provenance statement.
- `REPOSITORY_SHA256SUMS.txt`: Complete SHA-256 manifest of all files in this repository.
- `scripts/verify_spatial_multiplexing_campaigns.py`: Standalone offline verifier (Python 3 + `numpy`) that recomputes all `1,140` scored workload evaluations (`1,194` physical component circuit executions) and verifies all `19` IBM Quantum Heron jobs and pre-execution SHA-256 commitments.
- `data/ALL_19_IBM_HERON_JOBS_LEDGER.csv` (and backward-compatible `ALL_14_IBM_HERON_JOBS_LEDGER.csv`): Master 19-job hardware ledger (`586` PUBs, `655,360` physical shots, `220.0s` charged QPU seconds).
- `data/ALL_1140_SPATIAL_MULTIPLEXING_EVALUATIONS.csv` (and backward-compatible `ALL_820_SPATIAL_MULTIPLEXING_EVALUATIONS.csv`): Master flat CSV containing all `1,140` scored workload evaluations (`372` Campaign 1 + `448` Campaign 2 + `320` Campaign 3 = `1,140` evaluations covering `1,194` physical component circuits).
- `data/campaign1_uniform_6q_10q/`: Campaign 1 (`12` uniform workloads, `6Q-10Q`, `7` jobs, `242` PUBs, `372` evaluations / `426` component circuits, `97.0s` charged).
- `data/campaign2_heterogeneous_4q_12q/`: Campaign 2 (`16` heterogeneous workloads, `4Q-12Q`, `7` jobs, `262` PUBs, `448` evaluations, `89.0s` charged).
- `data/campaign3_joint_metaselector_tket/`: Campaign 3 (`16` heterogeneous workloads, `4Q-12Q`, Multi-Compiler `{SABRE O3, TKET, Prometheus}` Joint `(Compiler*, QPU*)` Meta-Selection + 8-Way Spatial Multiplexing, `5` jobs, `82` PUBs, `320` evaluations, `34.0s` charged).

## 2. Field Notes on Metric Naming

- `classical_overlap_fb`: Classical Bhattacharyya distribution overlap `F_B = Σ_x √(P_ideal(x) · P_meas(x))`.
- `hellinger_fidelity_fh`: Squared Hellinger fidelity `F_H = (F_B)²`.
- `tvd`: Total Variation Distance `TVD = 0.5 · Σ_x |P_ideal(x) - P_meas(x)|`.
- `within_0015_overlap_tolerance`: Boolean indicator testing whether `|F_B,oracle - F_B,selected| ≤ 0.015`.
