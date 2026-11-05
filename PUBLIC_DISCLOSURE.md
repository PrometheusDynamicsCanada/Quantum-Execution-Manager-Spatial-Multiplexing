# Public Disclosure & Intellectual Property Boundary

## 1. Purpose of This Public Evidence Repository

This repository provides a complete, cryptographically verifiable, offline-auditable hardware validation package for the **Prometheus Quantum Execution Manager, Multi-Compiler Meta-Selector & Spatial Multiplexing Campaigns** (`19` IBM Quantum Heron jobs, `586` composite and isolated PUBs, `655,360` physical shots, `1,140` scored workload evaluations / `1,194` physical component circuit executions, `220.0` charged QPU seconds) executed across **`ibm_kingston`**, **`ibm_marrakesh`**, and **`ibm_fez`**.

Every hardware metric, bitstring marginalization, shot-by-shot replica correlation, QPU and Joint `(Compiler*, QPU*)` selection regret calculation, and timing-isolation throughput ratio in the accompanying report can be verified offline without cloud credentials using [`scripts/verify_spatial_multiplexing_campaigns.py`](scripts/verify_spatial_multiplexing_campaigns.py).

## 2. Included Empirical Provenance (100% Public & Auditable)

- **IBM Quantum Hardware Job IDs & Timestamps:** All `19` IBM Quantum Heron Job IDs (`dbk1h6d6p51s73clp0hg`, `dbk1hfl6p51s73clp140`, `dbk1hq0nceqs73al07v0`, `dbk1i3l6p51s73clp22g`, `dbk1i6t6p51s73clp290`, `dbk1ia1kdqps7391ikgg`, `dbk1ic9ffo6c73baki30`, `dbk284hkdqps7391k5v0`, `dbk2g9gnceqs73al2eig`, `dbk2gipffo6c73bamlt0`, `dbk2gr1kdqps7391kq90`, `dbk2hf9kdqps7391krmg`, `dbk2hkl6p51s73clr60g`, `dbk2i1d6p51s73clr6ug`, `dbkaag9ffo6c73bb4oa0`, `dbkab5t6p51s73cm9000`, `dbkacr9ffo6c73bb4rf0`, `dbkafj1kdqps739235eg`, `dbkaflhkdqps739235hg`), submission and completion timestamps, provider-charged QPU seconds (`220.0s` total), active circuit execution times (`178.4530s` total), and raw per-PUB measurement bitstring histograms (`data/*/jobs/*.json`).
- **Pre-Execution Cryptographic Commitments:** All three canonical SHA-256 pre-execution commitment manifests (`661f49d86fdd0acb435f79c6dba8a544de6befa645b1d3377d951c58e8339f9b` for Campaign 1, `97bbb8f22c4301d4364ee1197a6ca2eb44926085c2acb635ffbd6b89dd718ee1` for Campaign 2, and `78426d6417210ec1b101f1141969edaa46c6b381eeb4af831eaabd1314495f82` for Campaign 3), locked strictly prior to the first hardware job submission in each campaign.
- **Multi-QPU Calibration Telemetry & Logical Circuits:** Complete live device calibration snapshots (`data/*/calibration_snapshot_*.json`), all unrouted logical benchmark circuits in OpenQASM 2.0 format (`data/*/logical_circuits/*.qasm`), and exact statevector reference probability distributions (`data/*/logical_circuits/*_ideal.npz`).

## 3. Withheld Proprietary Compiler & Scheduler Internals

In accordance with Prometheus Dynamics intellectual property protection standards:
- Internal subgraph scoring weights, multi-objective routing heuristics, Pareto selection rules, and proprietary synthesis pass implementations are withheld from this public repository.
- Experimental arms and compiled circuits are identified strictly by their public experimental arm designations, their physical subgraph qubit lists (`sub_nodes`), and their verifiable SHA-256 circuit hashes.
