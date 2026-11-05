# Quantum Execution Manager & Spatial Multiplexer: Multi-QPU & Multi-Compiler Hardware Validation

**Hardware Scope:** 19 IBM Quantum Heron Jobs | 586 Physical PUBs | 1,140 Workload Evaluations (1,194 Component Executions) | 655,360 Physical Shots | 220.0s Charged QPU Time  
**Processors:** `ibm_kingston`, `ibm_marrakesh`, `ibm_fez` (156-Qubit Heavy-Hex Heron r2)  
**Compilers Evaluated:** **Qiskit SABRE O3**, **Quantinuum TKET**, and **Prometheus**  
**One-Command Offline Audit:** `python scripts/verify_spatial_multiplexing_campaigns.py`

---

## 1. Executive Summary & Principal Comparative Benchmark Tables

This repository provides the offline-auditable hardware dataset and verification suite evaluating **Prometheus** as a **Quantum Execution Manager, Multi-Compiler Meta-Selector, and Spatial Multiplexer** across three 156-qubit IBM Heron processors (`ibm_kingston`, `ibm_marrakesh`, `ibm_fez`). All pre-execution routing, packing, and `(Compiler*, QPU*)` selection decisions were cryptographically locked via SHA-256 commitments prior to hardware submission:
- **Campaign 1 (Uniform 6Q, 8Q, 10Q Cohort, 7 Jobs):** `661f49d86fdd0acb435f79c6dba8a544de6befa645b1d3377d951c58e8339f9b`
- **Campaign 2 (Heterogeneous 4Q-12Q, 12-Family Cohort, 7 Jobs):** `97bbb8f22c4301d4364ee1197a6ca2eb44926085c2acb635ffbd6b89dd718ee1`
- **Campaign 3 (Multi-Compiler Joint `(Compiler*, QPU*)` Meta-Selector + 8-Way Packing, 5 Jobs):** `78426d6417210ec1b101f1141969edaa46c6b381eeb4af831eaabd1314495f82`

### Key Takeaways
1. **2.85x Quality-Qualified Throughput & -57.1% Billed QPU Time (Tables 1A & 1B):** Packing 16 heterogeneous workloads (4Q-12Q, 124 total logical qubits) into 2 buffered composite PUBs (k = 8, 8, 62 active qubits/PUB, graph buffer d_G ≥ 2) on `ibm_marrakesh` cut active circuit execution time by **7.84x** (0.5663s vs. 4.4424s for sequential Qiskit SABRE O3) and cut billed QPU time by **-57.1%** (3.0s vs. 7.0s), delivering **3.67 qualifying workloads (F_B ≥ 0.90) per billed second (2.85x vs. SABRE O3)**.
2. **Zero Multiplexing Fidelity Loss Across All 5 Matched Same-Qubit Controls (Tables 2A, 2B, 2C):** Across 216 paired isolated-vs.-packed circuit executions (432 total executions spanning k = 4, k = 6, k = 5,5,6, and k = 8,8 for both Prometheus and Quantinuum TKET), buffered spatial multiplexing (d_G ≥ 2) showed **zero statistically significant fidelity degradation** (Δ F_B between -0.0012 and +0.0035). For Quantinuum TKET (k = 8, 8), buffered packing on Prometheus subgraphs improved mean overlap by **+0.0031** (p = 0.0117) over isolated TKET and by **+0.0315 (+3.72%)** over unbuffered adjacent TKET packing (d_G = 1).
3. **Pre-Execution Joint `(Compiler*, QPU*)` Meta-Selection Beats All 9 Fixed Policies (Table 3A):** Evaluating 9 `(Compiler, QPU)` candidates per workload prior to execution (Prometheus selected on 29/48 pairs, Quantinuum TKET on 18/48, Qiskit SABRE O3 on 1/48), Prometheus's locked Joint `(Compiler*, QPU*)` forecast achieved **0.8944 *(mean)* F_B**, outperforming all 9 fixed single-compiler, single-QPU policies (0.8480 to 0.8939).
4. **98.09% Statistical Independence Across 4x Simultaneous Spatial Replicas (Table 2C):** Executing 4 disjoint 6Q replicas simultaneously for 1,024 shots (4,096 marginal samples in 1 PUB) yielded pairwise Pearson correlation r = 0.000602, mutual information 0.000738 bits, and an effective sample size of **N_eff = 4,017.8 / 4,096 (98.09%)**, achieving **0.9535 *(mean)* F_B** vs. 0.9460 for an isolated 4,096-shot run.
5. **GPU-Accelerated Subgraph Scoring & Packing (Table 4):** Tensorized scoring and 5-bin conflict-matrix construction on an NVIDIA GeForce RTX 5070 Ti accelerated candidate scoring by **27.1x-36.6x** (4.35-8.88 ms vs. 118.3-325.2 ms) and full end-to-end scheduling by **5.14x-6.44x** (87.0-104.9 ms).

> [!NOTE]
> **Metric Conventions:** Classical Bhattacharyya Distribution Overlap is F_B = Σ_x √(P_ideal(x) · P_meas(x)) (labeled as `"State Fidelity"` in Studio UI screenshots), Squared Hellinger Fidelity is F_H = F_B², and Total Variation Distance is TVD = 0.5 · Σ_x |P_ideal(x) - P_meas(x)|. Direction indicators show `(↑)` higher is better and `(↓)` lower is better; winning values in each comparison are **bolded**.

---

### Table 1A: Dedicated Timing-Isolation & Throughput Benchmarks on `ibm_marrakesh` (16 Heterogeneous Workloads, 4Q-12Q)
*Sample Size: N = 6 dedicated IBM Heron timing jobs (41 PUBs, 41,984 physical shots, 96 circuit executions) on `ibm_marrakesh` across Campaign 2 (T1-T4, 06:06-06:09 UTC) and Campaign 3 (T5-T6, 15:10 UTC).*

| Timing-Isolation Arm (`ibm_marrakesh`) | Job ID (Window) | PUBs / Shots `(↓)` | Active Exec / Billed Time `(↓)` | Overlap F_B *(mean / med)* `(↑)` | Hellinger F_H *(mean)* `(↑)` | TVD *(mean)* `(↓)` | Qualified (F_B ≥ 0.80 / 0.85 / 0.90) `(↑)` | Qualified (F_B ≥ 0.90) per Billed s / Exec s `(↑)` |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **T1: Qiskit SABRE O3 Sequential** (1 circ/PUB, 12Q max) | `dbk2gr1kdqps7391kq90` (C2) | 16 / 16,384 | 4.4424s / 7.0s | 0.8593 / 0.9062 | 0.7557 | 0.2945 | 12 / 11 / 9 (of 16) | 1.29/s (1.00x) \| 2.03/s (1.00x) |
| **T2: Prometheus Sequential** (1 circ/PUB, 12Q max) | `dbk2hf9kdqps7391krmg` (C2) | 16 / 16,384 | 4.3905s / 7.0s | **0.8840** / 0.9280 | **0.7986** | **0.2489** | **12 / 12 / 11** (of 16) | 1.57/s (1.22x) \| 2.51/s (1.24x) |
| **T3: Prometheus 3-PUB Packed** (k=5,5,6, 42Q, d_G ≥ 2) | `dbk2hkl6p51s73clr60g` (C2) | 3 / 3,072 | 0.8451s / **3.0s** | 0.8838 / 0.9365 | 0.7981 | 0.2494 | **12 / 12 / 11** (of 16) | **3.67/s (2.85x)** \| 13.02/s (6.42x) |
| **T4: Prometheus 2-PUB Packed** (k=8,8, 62Q, d_G ≥ 2) | `dbk2i1d6p51s73clr6ug` (C2) | **2 / 2,048** | **0.5663s (7.84x)** / **3.0s (-57.1%)** | 0.8837 / **0.9396** | 0.7976 | 0.2560 | **12 / 12 / 11** (of 16) | **3.67/s (2.85x)** \| **19.42/s (9.59x)** |
| **T5: Quantinuum TKET 2-PUB Packed** (k=8,8, 62Q, d_G ≥ 2) | `dbkafj1kdqps739235eg` (C3) | **2 / 2,048** | 0.5782s (7.68x) / **3.0s (-57.1%)** | 0.8542 / 0.9302 | 0.7548 | 0.2938 | 11 / 11 / 10 (of 16) | 3.33/s (2.59x) \| 17.30/s (8.54x) |
| **T6: Prometheus Meta-Selector 2-PUB** (k=8,8, 62Q, d_G ≥ 2) | `dbkaflhkdqps739235hg` (C3) | **2 / 2,048** | 0.5780s (7.69x) / **3.0s (-57.1%)** | **0.8634 (+1.08% vs T5)** / 0.9282 | **0.7686 (+1.83%)** | **0.2801 (-4.6%)** | **12** / 11 / **11** (of 16) | **3.67/s (2.85x)** \| 19.03/s (9.39x) |

### Table 1B: Campaign 1 Dedicated Timing-Isolation & Throughput Benchmark on `ibm_kingston` (12 Uniform Workloads, 6Q-10Q)
*Sample Size: N = 4 dedicated IBM Heron timing jobs (29 PUBs, 29,696 physical shots, 48 circuit executions) on `ibm_kingston`.*

| Timing-Isolation Arm (`ibm_kingston`) | IBM Job ID | PUBs / Shots `(↓)` | Active Exec / Billed Time `(↓)` | Overlap F_B *(mean / med)* `(↑)` | Hellinger F_H *(mean)* `(↑)` | TVD *(mean)* `(↓)` | Qualified (F_B ≥ 0.80 / 0.85 / 0.90) `(↑)` | Qualified (F_B ≥ 0.90) per Billed s / Exec s `(↑)` |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **T1: Qiskit SABRE O3 Sequential** (1 circ/PUB, 10Q max) | `dbk1i3l6p51s73clp22g` | 12 / 12,288 | 3.2775s / 6.0s | 0.8940 / 0.9203 | 0.8067 | 0.2880 | 10 / 9 / 7 (of 12) | 1.17/s (1.00x) \| 2.14/s (1.00x) |
| **T2: Prometheus Sequential** (1 circ/PUB, 10Q max) | `dbk1i6t6p51s73clp290` | 12 / 12,288 | 3.2736s / 5.0s | **0.9179** / 0.9380 | **0.8467** | **0.2348** | **11 / 10** / 8 (of 12) | 1.60/s (1.37x) \| 2.44/s (1.14x) |
| **T3: Prometheus 3-PUB Packed** (k=4,4,4, 32Q, d_G ≥ 2) | `dbk1ia1kdqps7391ikgg` | 3 / 3,072 | 0.8294s / **3.0s** | 0.9108 / **0.9443** | 0.8352 | 0.2520 | 10 / 10 / 8 (of 12) | 2.67/s (2.29x) \| 9.65/s (4.52x) |
| **T4: Prometheus 2-PUB Packed** (k=6,6, 48Q, d_G ≥ 2) | `dbk1ic9ffo6c73baki30` | **2 / 2,048** | **0.5560s (5.89x)** / **3.0s (-50.0%)** | 0.9157 / 0.9419 | 0.8434 | 0.2428 | 10 / 10 / **9** (of 12) | **3.00/s (2.57x)** \| **16.19/s (7.58x)** |

---

## 2. Pooled 3-QPU Interleaved Quality & Matched Same-Qubit Interference Benchmarks

### Table 2A: Campaign 3 Multi-Compiler & Joint Meta-Selector 8-Way Packing (`ibm_kingston`, `ibm_marrakesh`, `ibm_fez`)
*Sample Size: N = 3 interleaved master jobs (`dbkaag9ffo6c73bb4oa0`, `dbkab5t6p51s73cm9000`, `dbkacr9ffo6c73bb4rf0`; 78 PUBs, 79,872 shots, 288 evaluations across 16 workloads × 3 QPUs). Arms 1, 2, 3, 5, and 6 share identical buffered (d_G ≥ 2) 8-way subgraphs.*

| Experimental Arm (Pooled Across 3 Heron QPUs, N = 48 Evals/Arm) | PUBs / QPU `(↓)` | Buffer (d_G) | CZ Gates / Depth *(mean)* `(↓)` | Overlap F_B *(mean / med)* `(↑)` | Hellinger F_H / TVD *(mean)* | Per-QPU Mean F_B (`king / fez / marr`) `(↑)` | Matched Control & Compiler Comparison (Δ F_B) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **Arm 1: Quantinuum TKET Isolated Control** (Same qubits/gates as Arm 3) | 16 (12Q) | Isolated | **59.35** / 122.98 | 0.8730 / 0.9269 | 0.7821 / 0.2681 | 0.8921 / 0.8869 / 0.8399 | Isolated same-qubit control for Arm 3 |
| **Arm 2: Qiskit SABRE O3 Buffered 8-Way** (k=8,8, 62Q/PUB) | **2** (62Q) | **d_G ≥ 2** | 62.77 / 127.65 | 0.8776 / 0.9290 | 0.7881 / 0.2635 | 0.8928 / 0.8828 / **0.8573** | Buffered SABRE O3 8-way baseline |
| **Arm 3: Quantinuum TKET Buffered 8-Way** (k=8,8, 62Q/PUB) | **2** (62Q) | **d_G ≥ 2** | **59.35** / 122.98 | 0.8761 / **0.9321** | 0.7872 / 0.2630 | 0.8926 / 0.8877 / 0.8480 | **+0.0031 vs Arm 1** (p = 0.0117); **+0.0315 (+3.72%) vs Arm 4** |
| **Arm 4: Quantinuum TKET Unbuffered 8-Way** (k=8,8, Adjacent) | **2** (62Q) | d_G = 1 | 61.85 / 125.25 | 0.8447 / 0.8879 | 0.7372 / 0.3085 | 0.8366 / 0.8486 / 0.8487 | Hardware-blind adjacent TKET control |
| **Arm 5: Prometheus Native Buffered 8-Way** (k=8,8, 62Q/PUB) | **2** (62Q) | **d_G ≥ 2** | 60.58 / 117.62 | 0.8777 / 0.9290 | 0.7892 / 0.2627 | **0.8939** / 0.8852 / 0.8540 | Single-compiler Prometheus 8-way |
| **Arm 6: Prometheus Joint Meta-Selector 8-Way** (k=8,8, 62Q/PUB) | **2** (62Q) | **d_G ≥ 2** | 59.46 / **116.52** | **0.8796** / 0.9309 | **0.7921** / **0.2578** | 0.8929 / **0.8888** / 0.8572 | **#1 Pooled F_B, F_H, TVD, & Depth** (**+0.0350 vs Arm 4**, p_raw = 0.0785) |

### Table 2B: Campaign 2 Heterogeneous 4Q-12Q Interleaved Quality & Interference (`ibm_marrakesh`, `ibm_fez`, `ibm_kingston`)
*Sample Size: N = 3 interleaved master jobs (`dbk284hkdqps7391k5v0`, `dbk2g9gnceqs73al2eig`, `dbk2gipffo6c73bamlt0`; 225 PUBs, 230,400 shots, 384 evaluations across 16 workloads × 3 QPUs).*

| Experimental Arm (Pooled Across 3 Heron QPUs, N = 48 Evals/Arm) | PUBs / QPU `(↓)` | Buffer (d_G) | CZ Gates / Depth *(mean)* `(↓)` | Overlap F_B *(mean / med)* `(↑)` | Hellinger F_H / TVD *(mean)* | Passing F_B ≥ 0.80 / 0.85 / 0.90 `(↑)` | Same-Qubit Packing Δ F_B & Holm p-Value |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **Arm 1: Qiskit SABRE O3 Sequential** (Full-chip baseline) | 16 (12Q) | Isolated | **53.25** / 116.15 | 0.8535 / 0.9148 | 0.7499 / 0.2956 | 33 / 32 / 28 (of 48) | Baseline reference (Arm 1) |
| **Arm 2: Prometheus Sequential** (Best subgraph/QPU) | 16 (12Q) | Isolated | 58.54 / **110.85** | **0.8934 (+4.67%)** / 0.9385 | **0.8139** / **0.2361** | **38 / 36 / 34** (of 48) | **Holm p = 0.002625** vs Arm 1 (+8.54% F_H) |
| **Arm 3: Prometheus 3-PUB Isolated Control** (Same qubits as Arm 4) | 16 (12Q) | Isolated | 59.40 / 113.40 | 0.8815 / 0.9361 | 0.7939 / 0.2562 | 36 / 35 / 30 (of 48) | Isolated same-qubit control for Arm 4 |
| **Arm 4: Prometheus 3-PUB Buffered Packed** (k=5,5,6, 42Q) | 3 (42Q) | **d_G ≥ 2** | 59.40 / 113.40 | 0.8849 (+5.79% vs Arm 5) / **0.9441** | 0.8001 / 0.2511 | 37 / 35 / 31 (of 48) | **+0.0035 vs Arm 3** (p = 0.0367); **Holm p = 0.002014 vs Arm 5** |
| **Arm 5: Competent SABRE O3 3-PUB Buffered** (k=5,5,6, 42Q) | 3 (42Q) | d_G ≥ 2 | 63.73 / 130.15 | 0.8365 / 0.9146 | 0.7308 / 0.3136 | 33 / 32 / 29 (of 48) | Disjoint buffered SABRE O3 baseline |
| **Arm 6: Unbuffered SABRE O3 3-PUB Adjacent** (k=5,5,6, 42Q) | 3 (42Q) | d_G = 1 | 64.81 / 127.65 | 0.8515 / 0.9043 | 0.7449 / 0.3059 | 33 / 33 / 25 (of 48) | **Holm p = 0.000488** (Arm 4 vs Arm 6) |
| **Arm 7: Prometheus 2-PUB Isolated Control** (Same qubits as Arm 8) | 16 (12Q) | Isolated | 60.50 / 117.31 | 0.8707 / 0.9241 | 0.7802 / 0.2701 | 38 / 35 / 30 (of 48) | Isolated same-qubit control for Arm 8 |
| **Arm 8: Prometheus 2-PUB Buffered Packed** (k=8,8, 62Q) | **2** (62Q) | **d_G ≥ 2** | 60.50 / 117.31 | 0.8733 (+2.32% vs Arm 1) / 0.9334 | 0.7848 / 0.2675 | **38** / 35 / 32 (of 48) | **+0.0026 vs Arm 7** (p = 0.0668); **Holm p = 0.004272 vs Arm 1** |

### Table 2C: Campaign 1 Uniform 6Q-10Q Interleaved Quality & 4x Spatial Replicas (`ibm_kingston`, `ibm_marrakesh`, `ibm_fez`)
*Sample Size: N = 3 interleaved master jobs (`dbk1h6d6p51s73clp0hg`, `dbk1hfl6p51s73clp140`, `dbk1hq0nceqs73al07v0`; 213 PUBs, 273,408 shots, 324 evaluations / 378 component executions).*

| Experimental Arm (Pooled Across 3 Heron QPUs) | Evals (N) | PUBs / QPU `(↓)` | Buffer (d_G) | CZ Gates / Depth *(mean)* `(↓)` | Overlap F_B *(mean / med)* `(↑)` | Hellinger F_H / TVD *(mean)* | Passing F_B ≥ 0.80 / 0.85 / 0.90 `(↑)` | Same-Qubit Packing Δ F_B & Statistical Test |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **Arm A: Qiskit SABRE O3 Sequential** (Full-chip baseline) | 36 | 12 (10Q) | Isolated | **76.50** / 163.58 | 0.8792 / 0.9094 | 0.7815 / 0.3097 | 28 / 25 / 20 (of 36) | Baseline reference (Arm A) |
| **Arm B: Prometheus Sequential** (Best subgraph/QPU) | 36 | 12 (10Q) | Isolated | 77.08 / **151.33** | **0.9124 (+3.78%)** / 0.9403 | **0.8371** / **0.2501** | 31 / **30** / 24 (of 36) | **Holm p = 0.001953** vs Arm A (+7.11% F_H) |
| **Arm C: Prometheus k=4 Isolated Control** (Same qubits as Arm D) | 36 | 12 (10Q) | Isolated | 82.44 / 156.92 | 0.9092 / 0.9399 | 0.8312 / 0.2575 | **32 / 30 / 25** (of 36) | Isolated same-qubit control for Arm D |
| **Arm D: Prometheus k=4 Buffered Packed** (3 PUBs, 32Q/PUB) | 36 | 3 (32Q) | **d_G ≥ 2** | 82.44 / 156.92 | 0.9079 (+2.97% vs Arm E) / **0.9414** | 0.8293 / 0.2600 | **32** / 29 / 24 (of 36) | **-0.0012 vs Arm C** (p = 0.3223); **Holm p = 0.001465 vs Arm E** |
| **Arm E: Competent SABRE O3 k=4 Buffered** (3 PUBs, 32Q/PUB) | 36 | 3 (32Q) | d_G ≥ 2 | 92.25 / 174.42 | 0.8817 / 0.9087 | 0.7840 / 0.3103 | 29 / 27 / 19 (of 36) | Disjoint buffered SABRE O3 baseline |
| **Arm F: Unbuffered SABRE O3 k=4 Adjacent** (3 PUBs, 32Q/PUB) | 36 | 3 (32Q) | d_G = 1 | 88.17 / 179.25 | 0.8757 / 0.9095 | 0.7748 / 0.3161 | 26 / 25 / 19 (of 36) | **Holm p = 0.001953** (Arm D vs Arm F) |
| **Arm G_ctrl: Prometheus k=6 Isolated Control** (Same qubits as Arm G) | 36 | 12 (10Q) | Isolated | 83.75 / 157.17 | 0.9051 / 0.9223 | 0.8240 / 0.2659 | 31 / **30** / 24 (of 36) | Isolated same-qubit control for Arm G |
| **Arm G: Prometheus k=6 Buffered Packed** (2 PUBs, 48Q/PUB) | 36 | **2** (48Q) | **d_G ≥ 2** | 83.75 / 157.17 | 0.9060 (+3.05% vs Arm A) / 0.9263 | 0.8255 / 0.2661 | 31 / **30** / 23 (of 36) | **+0.0008 vs Arm G_ctrl** (95% CI: [-0.0018, +0.0036], p = 0.5171) |
| **Arm H_ctrl: Prometheus Isolated 4,096-Shot Control** (6Q) | 18 | 6 (4,096 sh) | Isolated | 92.17 / 164.11 | 0.9460 / 0.9606 | 0.8961 / 0.2147 | **18 / 18** / 15 (of 18) | Single-region 4,096-shot baseline |
| **Arm H: Prometheus 4x Spatial Replicas** (4 × 1,024 shots in 1 PUB) | 18 | 6 (1,024 sh) | **d_G ≥ 2** | 92.17 / 164.11 | **0.9535 (+0.79%)** / **0.9632** | **0.9100** / **0.1951** | **18 / 18 / 16** (of 18) | **N_eff = 4,017.8 / 4,096 (98.09%)**, r = 0.000602, MI = 0.000738 bits |

---

## 3. Pre-Execution Joint `(Compiler*, QPU*)` & Multi-QPU Selection vs. Fixed Policies

### Table 3A: Campaign 3 Pre-Execution 9-Way Joint `(Compiler*, QPU*)` Selection vs. All 9 Fixed Policies
*Sample Size: 16 heterogeneous workloads (4Q-12Q, 12 families) × 3 compilers × 3 QPUs (144 buffered 8-way circuit executions + 48 independent Arm 6 meta-selected executions).*

| Selection Policy (Campaign 3, 16 Heterogeneous Workloads 4Q-12Q) | Overlap F_B *(mean)* `(↑)` | Δ F_B Joint Selected vs. Policy | Mean / Median Regret vs. 9-Way Oracle `(↓)` | Policy Description & Tolerance Accuracy |
| :--- | :---: | :---: | :---: | :--- |
| **Post-Hoc 9-Way Oracle (max_{c, q} F_B(w, c, q))** | **0.902132** | -0.007756 | 0.000000 / 0.000000 | Best post-hoc `(Compiler, QPU)` pair per workload |
| **Pre-Committed Joint `(Compiler*, QPU*)` Selection (Prometheus Forecast)** | **0.894375** | **Reference (#1 Pre-Exec)** | **0.007756 / 0.002165** | **Beats all 9 fixed `(Compiler, QPU)` policies (13/16 within 0.015 tol)** |
| **Pre-Committed QPU Selection on Independent Arm 6 (Meta-Selector PUBs)** | 0.893004 | +0.001371 | 0.009128 (0.004630 vs Arm 6 Oracle) | **Beats all 3 fixed-QPU Arm 6 policies (14/16 within 0.015 tol)** |
| **Fixed Policy 1: Prometheus Native @ `ibm_kingston` (Arm 5)** | 0.893921 | **+0.000454 (+0.05%)** | 0.008211 / 0.004190 | Fixed compiler (Prometheus Native), fixed QPU (`ibm_kingston`) |
| **Fixed Policy 2: Prometheus Meta-Selector @ `ibm_kingston` (Arm 6)** | 0.892885 | **+0.001490 (+0.17%)** | 0.009247 / 0.005612 | Per-workload meta-selected compiler, fixed QPU (`ibm_kingston`) |
| **Fixed Policy 3: Qiskit SABRE O3 @ `ibm_kingston` (Arm 2)** | 0.892754 | **+0.001621 (+0.18%)** | 0.009378 / 0.005315 | Fixed compiler (Qiskit SABRE O3), fixed QPU (`ibm_kingston`) |
| **Fixed Policy 4: Quantinuum TKET @ `ibm_kingston` (Arm 3)** | 0.892575 | **+0.001800 (+0.20%)** | 0.009557 / 0.005110 | Fixed compiler (Quantinuum TKET), fixed QPU (`ibm_kingston`) |
| **Fixed Policy 5: Prometheus Meta-Selector @ `ibm_fez` (Arm 6)** | 0.888766 | **+0.005609 (+0.63%)** | 0.013366 / 0.011240 | Per-workload meta-selected compiler, fixed QPU (`ibm_fez`) |
| **Fixed Policy 6: Quantinuum TKET @ `ibm_fez` (Arm 3)** | 0.887699 | **+0.006676 (+0.75%)** | 0.014433 / 0.011890 | Fixed compiler (Quantinuum TKET), fixed QPU (`ibm_fez`) |
| **Fixed Policy 7: Prometheus Native @ `ibm_fez` (Arm 5)** | 0.885229 | **+0.009146 (+1.03%)** | 0.016903 / 0.014110 | Fixed compiler (Prometheus Native), fixed QPU (`ibm_fez`) |
| **Fixed Policy 8: Qiskit SABRE O3 @ `ibm_fez` (Arm 2)** | 0.882770 | **+0.011605 (+1.31%)** | 0.019362 / 0.016850 | Fixed compiler (Qiskit SABRE O3), fixed QPU (`ibm_fez`) |
| **Fixed Policy 9: Qiskit SABRE O3 @ `ibm_marrakesh` (Arm 2)** | 0.857253 | **+0.037123 (+4.33%)** | 0.044879 / 0.024110 | Fixed compiler (Qiskit SABRE O3), fixed QPU (`ibm_marrakesh`) |
| **Fixed Policy 10: Prometheus Meta-Selector @ `ibm_marrakesh` (Arm 6)** | 0.857189 | **+0.037186 (+4.34%)** | 0.044943 / 0.022890 | Per-workload meta-selected compiler, fixed QPU (`ibm_marrakesh`) |
| **Fixed Policy 11: Prometheus Native @ `ibm_marrakesh` (Arm 5)** | 0.853981 | **+0.040394 (+4.73%)** | 0.048151 / 0.025410 | Fixed compiler (Prometheus Native), fixed QPU (`ibm_marrakesh`) |
| **Fixed Policy 12: Quantinuum TKET @ `ibm_marrakesh` (Arm 3)** | 0.848046 | **+0.046329 (+5.46%)** | 0.054086 / 0.028920 | Fixed compiler (Quantinuum TKET), fixed QPU (`ibm_marrakesh`) |

<details>
<summary><strong>Click to expand Table 3B: Campaign 3 Per-Workload 9-Way (Compiler*, QPU*) Forecasts & Observed Outcomes (16 Workloads, 4Q-12Q)</strong></summary>

*9-Way Within 0.015 Overlap Tolerance: 13 / 16 (81.2%). QPU Selection Within 0.015 Overlap Tolerance: 14 / 16 (87.5%). Per-(Workload, QPU) Compiler Selection Within 0.015 Overlap Tolerance: 40 / 48 (83.3%).*

| Workload ID | Family (Width) | Pre-Committed `(Compiler*, QPU*)` | Observed Best 9-Way `(Compiler, QPU)` | 9-Way Oracle F_B `(↑)` | Selected Joint F_B `(↑)` | 9-Way Regret `(↓)` | `ibm_kingston` (`Prom / TKET / SABRE`) | `ibm_fez` (`Prom / TKET / SABRE`) | Arm 6 Meta (`king / fez / marr`) | Within 0.015 Tol |
| :--- | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `het_ghz_4q` | `ghz_entangler` (4Q) | `prometheus_native@ibm_kingston` | `prometheus_native@ibm_marrakesh` | **0.9799** | 0.9730 | 0.0068 | 0.9730 / 0.9752 / 0.9736 | 0.9764 / 0.9772 / 0.9794 | 0.9688 / 0.9824 / 0.9813 | Yes |
| `het_bv_5q` | `bernstein_vazirani` (5Q) | `tket@ibm_kingston` | `prometheus_native@ibm_kingston` | **0.9743** | 0.9717 | 0.0025 | 0.9743 / 0.9717 / 0.9652 | 0.9617 / 0.9637 / 0.9629 | 0.9682 / 0.9716 / 0.9664 | Yes |
| `het_grover_5q` | `grover_search` (5Q) | `tket@ibm_kingston` | `tket@ibm_kingston` | **0.9850** | 0.9850 | 0.0000 | 0.9795 / 0.9850 / 0.9788 | 0.9811 / 0.9850 / 0.9845 | 0.9855 / 0.9855 / 0.9799 | Yes (Exact) |
| `het_wstate_6q` | `w_state_dicke` (6Q) | `prometheus_native@ibm_kingston` | `tket@ibm_fez` | **0.9309** | 0.9298 | 0.0011 | 0.9298 / 0.9195 / 0.9287 | 0.9145 / 0.9309 / 0.9289 | 0.9299 / 0.9290 / 0.5229 | Yes |
| `het_simon_6q` | `simon_period` (6Q) | `tket@ibm_fez` | `tket@ibm_kingston` | **0.9927** | 0.9909 | 0.0018 | 0.9918 / 0.9927 / 0.9904 | 0.9904 / 0.9909 / 0.9921 | 0.9891 / 0.9883 / 0.9802 | Yes |
| `het_qv_6q` | `quantum_volume` (6Q) | `tket@ibm_kingston` | `tket@ibm_kingston` | **0.9856** | 0.9856 | 0.0000 | 0.9831 / 0.9856 / 0.9839 | 0.9833 / 0.9791 / 0.9689 | 0.9844 / 0.9815 / 0.9828 | Yes (Exact) |
| `het_qaoa_7q` | `qaoa_maxcut` (7Q) | `prometheus_native@ibm_kingston` | `prometheus_native@ibm_kingston` | **0.9684** | 0.9684 | 0.0000 | 0.9684 / 0.9649 / 0.9588 | 0.9488 / 0.9479 / 0.9536 | 0.9625 / 0.9517 / 0.9585 | Yes (Exact) |
| `het_vqe_su2_7q` | `vqe_su2` (7Q) | `prometheus_native@ibm_kingston` | `tket@ibm_fez` | **0.9428** | 0.9337 | 0.0091 | 0.9337 / 0.9229 / 0.9229 | 0.9222 / 0.9428 / 0.9423 | 0.9385 / 0.9318 / 0.9230 | Yes |
| `het_qft_8q` | `qft_phase` (8Q) | `tket@ibm_fez` | `prometheus_native@ibm_fez` | **0.9484** | 0.9273 | 0.0211 | 0.9063 / 0.9324 / 0.9418 | 0.9484 / 0.9273 / 0.9400 | 0.9053 / 0.9148 / 0.9426 | No |
| `het_heisenberg_8q` | `heisenberg_xxz` (8Q) | `tket@ibm_kingston` | `prometheus_native@ibm_fez` | **0.9281** | 0.8786 | 0.0495 | 0.9088 / 0.8786 / 0.8925 | 0.9281 / 0.9032 / 0.9089 | 0.8824 / 0.9025 / 0.8828 | No |
| `het_hiddenshift_9q` | `hidden_shift` (9Q) | `tket@ibm_kingston` | `tket@ibm_kingston` | **0.9579** | 0.9579 | 0.0000 | 0.9460 / 0.9579 / 0.9489 | 0.9265 / 0.9524 / 0.9107 | 0.9602 / 0.9499 / 0.9480 | Yes (Exact) |
| `het_bipartite_9q` | `dense_bipartite` (9Q) | `prometheus_native@ibm_fez` | `tket@ibm_fez` | **0.9128** | 0.8966 | 0.0162 | 0.9079 / 0.9055 / 0.9086 | 0.8966 / 0.9128 / 0.9020 | 0.9041 / 0.8973 / 0.9035 | No |
| `het_iqp_10q` | `iqp_pauli` (10Q) | `prometheus_native@ibm_kingston` | `prometheus_native@ibm_kingston` | **0.8076** | 0.8076 | 0.0000 | 0.8076 / 0.7816 / 0.7965 | 0.7555 / 0.7504 / 0.7480 | 0.7862 / 0.7634 / 0.7879 | Yes (Exact) |
| `het_ising_11q` | `trotter_ising_nnn` (11Q) | `prometheus_native@ibm_kingston` | `sabre_o3@ibm_kingston` | **0.7891** | 0.7851 | 0.0039 | 0.7851 / 0.7771 / 0.7891 | 0.7589 / 0.7568 / 0.7380 | 0.7857 / 0.7607 / 0.7299 | Yes |
| `het_qft_11q` | `qft_phase` (11Q) | `tket@ibm_kingston` | `tket@ibm_kingston` | **0.8072** | 0.8072 | 0.0000 | 0.7933 / 0.8072 / 0.7931 | 0.7894 / 0.8056 / 0.7752 | 0.8137 / 0.8093 / 0.7690 | Yes (Exact) |
| `het_qaoa_plaid_12q` | `qaoa_plaid` (12Q) | `sabre_o3@ibm_kingston` | `tket@ibm_kingston` | **0.5233** | 0.5113 | 0.0120 | 0.5141 / 0.5233 / 0.5113 | 0.4820 / 0.4771 / 0.4889 | 0.5216 / 0.5006 / 0.4564 | Yes |

</details>

<details>
<summary><strong>Click to expand Table 3C: Campaigns 1 & 2 Per-Workload Pre-Execution QPU Selection Outcomes (28 Workloads)</strong></summary>

*Campaign 2 Exact Top-1 QPU Accuracy: 11 / 16 (68.8%), Within 0.015 Overlap Tolerance: 16 / 16 (100.0%), Mean/Median Regret: 0.001060 / 0.000000. Campaign 1 Exact Top-1 QPU Accuracy: 10 / 12 (83.3%), Within 0.015 Overlap Tolerance: 12 / 12 (100.0%), Mean/Median Regret: 0.001487 / 0.000000.*

| Workload ID (Campaign 2) | Family (Width) | Pre-Committed QPU | Observed Best QPU | Oracle F_B `(↑)` | Selected F_B `(↑)` | Regret `(↓)` | Prometheus (`king / fez / marr`) | SABRE O3 (`king / fez / marr`) | Exact / Within 0.015 |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `het_ghz_4q` | `ghz_entangler` (4Q) | `ibm_kingston` | `ibm_fez` | **0.9838** | 0.9832 | 0.0006 | 0.9832 / 0.9838 / 0.9633 | 0.9788 / 0.9598 / 0.9701 | No / Yes |
| `het_bv_5q` | `bernstein_vazirani` (5Q) | `ibm_kingston` | `ibm_marrakesh` | **0.9752** | 0.9712 | 0.0040 | 0.9712 / 0.9688 / 0.9752 | 0.9581 / 0.9432 / 0.9045 | No / Yes |
| `het_grover_5q` | `grover_search` (5Q) | `ibm_kingston` | `ibm_fez` | **0.9808** | 0.9768 | 0.0040 | 0.9768 / 0.9808 / 0.9753 | 0.9882 / 0.9708 / 0.9816 | No / Yes |
| `het_wstate_6q` | `w_state_dicke` (6Q) | `ibm_kingston` | `ibm_kingston` | **0.9459** | 0.9459 | 0.0000 | 0.9459 / 0.9435 / 0.9146 | 0.9667 / 0.9029 / 0.9427 | Yes / Yes |
| `het_simon_6q` | `simon_period` (6Q) | `ibm_kingston` | `ibm_kingston` | **0.9921** | 0.9921 | 0.0000 | 0.9921 / 0.9905 / 0.9910 | 0.9915 / 0.9924 / 0.9926 | Yes / Yes |
| `het_qv_6q` | `quantum_volume` (6Q) | `ibm_fez` | `ibm_fez` | **0.9862** | 0.9862 | 0.0000 | 0.9829 / 0.9862 / 0.9822 | 0.9844 / 0.9799 / 0.9683 | Yes / Yes |
| `het_qaoa_7q` | `qaoa_maxcut` (7Q) | `ibm_kingston` | `ibm_fez` | **0.9735** | 0.9701 | 0.0034 | 0.9701 / 0.9735 / 0.9691 | 0.9569 / 0.9348 / 0.9344 | No / Yes |
| `het_vqe_su2_7q` | `vqe_su2` (7Q) | `ibm_kingston` | `ibm_kingston` | **0.9690** | 0.9690 | 0.0000 | 0.9690 / 0.9456 / 0.9524 | 0.9330 / 0.9399 / 0.9156 | Yes / Yes |
| `het_qft_8q` | `qft_phase` (8Q) | `ibm_kingston` | `ibm_kingston` | **0.9325** | 0.9325 | 0.0000 | 0.9325 / 0.9158 / 0.9274 | 0.9239 / 0.5968 / 0.9214 | Yes / Yes |
| `het_heisenberg_8q` | `heisenberg_xxz` (8Q) | `ibm_fez` | `ibm_kingston` | **0.9168** | 0.9119 | 0.0049 | 0.9168 / 0.9119 / 0.8978 | 0.8509 / 0.8932 / 0.7848 | No / Yes |
| `het_hiddenshift_9q` | `hidden_shift` (9Q) | `ibm_kingston` | `ibm_kingston` | **0.9528** | 0.9528 | 0.0000 | 0.9528 / 0.9307 / 0.9335 | 0.9140 / 0.8496 / 0.9009 | Yes / Yes |
| `het_bipartite_9q` | `dense_bipartite` (9Q) | `ibm_kingston` | `ibm_kingston` | **0.9075** | 0.9075 | 0.0000 | 0.9075 / 0.8966 / 0.9041 | 0.8988 / 0.7401 / 0.8876 | Yes / Yes |
| `het_iqp_10q` | `iqp_pauli` (10Q) | `ibm_kingston` | `ibm_kingston` | **0.8092** | 0.8092 | 0.0000 | 0.8092 / 0.7833 / 0.7887 | 0.7909 / 0.6947 / 0.7870 | Yes / Yes |
| `het_ising_11q` | `trotter_ising_nnn` (11Q) | `ibm_kingston` | `ibm_kingston` | **0.7820** | 0.7820 | 0.0000 | 0.7820 / 0.7607 / 0.7642 | 0.7538 / 0.6234 / 0.6084 | Yes / Yes |
| `het_qft_11q` | `qft_phase` (11Q) | `ibm_kingston` | `ibm_kingston` | **0.8407** | 0.8407 | 0.0000 | 0.8407 / 0.7981 / 0.7727 | 0.7673 / 0.5236 / 0.7339 | Yes / Yes |
| `het_qaoa_plaid_12q` | `qaoa_plaid` (12Q) | `ibm_kingston` | `ibm_kingston` | **0.5142** | 0.5142 | 0.0000 | 0.5142 / 0.4966 / 0.4582 | 0.5360 / 0.4608 / 0.5367 | Yes / Yes |

| Workload ID (Campaign 1) | Family (Width) | Pre-Committed QPU | Observed Best QPU | Oracle F_B `(↑)` | Selected F_B `(↑)` | Regret `(↓)` | Prometheus (`king / fez / marr`) | SABRE O3 (`king / fez / marr`) | Exact / Within 0.015 |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `os_qaoa_6q` | `qaoa_maxcut` (6Q) | `ibm_kingston` | `ibm_kingston` | **0.9846** | 0.9846 | 0.0000 | 0.9846 / 0.9762 / 0.9734 | 0.9028 / 0.9777 / 0.9813 | Yes / Yes |
| `os_qaoa_8q` | `qaoa_maxcut` (8Q) | `ibm_kingston` | `ibm_kingston` | **0.9366** | 0.9366 | 0.0000 | 0.9366 / 0.9279 / 0.9058 | 0.9060 / 0.9129 / 0.8382 | Yes / Yes |
| `os_qaoa_10q` | `qaoa_maxcut` (10Q) | `ibm_kingston` | `ibm_kingston` | **0.8037** | 0.8037 | 0.0000 | 0.8037 / 0.7684 / 0.7803 | 0.7386 / 0.7568 / 0.7536 | Yes / Yes |
| `os_qft_6q` | `qft_phase` (6Q) | `ibm_kingston` | `ibm_kingston` | **0.9745** | 0.9745 | 0.0000 | 0.9745 / 0.9717 / 0.9495 | 0.9666 / 0.9332 / 0.9478 | Yes / Yes |
| `os_qft_8q` | `qft_phase` (8Q) | `ibm_kingston` | `ibm_kingston` | **0.9483** | 0.9483 | 0.0000 | 0.9483 / 0.9153 / 0.9159 | 0.9281 / 0.6118 / 0.8851 | Yes / Yes |
| `os_qft_10q` | `qft_phase` (10Q) | `ibm_kingston` | `ibm_kingston` | **0.8981** | 0.8981 | 0.0000 | 0.8981 / 0.8604 / 0.8636 | 0.8983 / 0.8677 / 0.8641 | Yes / Yes |
| `os_dense_6q` | `dense_bipartite` (6Q) | `ibm_kingston` | `ibm_marrakesh` | **0.9825** | 0.9813 | 0.0012 | 0.9813 / 0.9804 / 0.9825 | 0.9769 / 0.9663 / 0.9584 | No / Yes |
| `os_dense_8q` | `dense_bipartite` (8Q) | `ibm_kingston` | `ibm_kingston` | **0.9529** | 0.9529 | 0.0000 | 0.9529 / 0.9518 / 0.9424 | 0.9357 / 0.9292 / 0.9376 | Yes / Yes |
| `os_dense_10q` | `dense_bipartite` (10Q) | `ibm_kingston` | `ibm_kingston` | **0.7926** | 0.7926 | 0.0000 | 0.7926 / 0.7854 / 0.7899 | 0.7472 / 0.7906 / 0.7789 | Yes / Yes |
| `os_ising_6q` | `trotter_ising` (6Q) | `ibm_kingston` | `ibm_marrakesh` | **0.9749** | 0.9732 | 0.0017 | 0.9732 / 0.9738 / 0.9749 | 0.9641 / 0.9572 / 0.9521 | No / Yes |
| `os_ising_8q` | `trotter_ising` (8Q) | `ibm_kingston` | `ibm_fez` | **0.9493** | 0.9424 | 0.0070 | 0.9424 / 0.9493 / 0.9383 | 0.9419 / 0.8901 / 0.9387 | No / Yes |
| `os_ising_10q` | `trotter_ising` (10Q) | `ibm_kingston` | `ibm_fez` | **0.8678** | 0.8598 | 0.0080 | 0.8598 / 0.8678 / 0.8537 | 0.8350 / 0.8072 / 0.6723 | No / Yes |

</details>

---

## 4. Classical Control-Plane Acceleration (NVIDIA RTX 5070 Ti + 24-Core CPU)

All classical control-plane timings are reported separately from QPU execution metrics and include host-to-device (H2D) and device-to-host (D2H) PCIe transfers, sorting, and CPU subgraph enumeration across 2,040 to 2,171 connected candidate subgraphs per QPU.

### Table 4: Classical Subgraph Scheduling & Parallel Compilation Benchmarks

| Target QPU / Benchmark Stage | Candidate Pool | CPU Enum (ms) | Score + Sort: CPU vs. GPU `(↓)` | Score Speedup `(↑)` | 5-Bin Conflict Matrix: CPU vs. GPU `(↓)` | Matrix Speedup `(↑)` | End-to-End (Incl. Enum): CPU vs. GPU `(↓)` | End-to-End Speedup `(↑)` |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **`ibm_marrakesh` (156Q)** | 2,171 subgraphs | 64.55 ms | 325.17 ms vs **8.88 ms** | **36.61x** | 269.87 ms vs **16.65 ms** | **16.20x** | 675.08 ms vs **104.89 ms** | **6.44x** (15.13x post-enum) |
| **`ibm_fez` (156Q)** | 2,143 subgraphs | 62.52 ms | 125.52 ms vs **4.35 ms** | **28.88x** | 272.32 ms vs **7.93 ms** | **34.34x** | 481.07 ms vs **93.51 ms** | **5.14x** (13.51x post-enum) |
| **`ibm_kingston` (156Q)** | 2,040 subgraphs | 60.69 ms | 118.32 ms vs **4.36 ms** | **27.12x** | 268.22 ms vs **8.30 ms** | **32.30x** | 462.06 ms vs **87.03 ms** | **5.31x** (15.24x post-enum) |
| **16-Workload Transpilation** | 16 circuits (124Q) | Pre-loaded | 1-Core: 18.6781s vs **24-Core (8x3): 11.5474s** | **1.618x** | Bit-identical deterministic seeds | Verified | 18.6781s vs **11.5474s** (-38.18%) | **1.618x** |

---

## 5. Complete 19-Job IBM Quantum Heron Hardware Ledger (220.0s Charged QPU Time)

Every hardware job across all three campaigns is archived with full per-PUB bitstring histograms in `data/campaign1_uniform_6q_10q/jobs/`, `data/campaign2_heterogeneous_4q_12q/jobs/`, and `data/campaign3_joint_metaselector_tket/jobs/`, and summarized in [`data/ALL_19_IBM_HERON_JOBS_LEDGER.csv`](data/ALL_19_IBM_HERON_JOBS_LEDGER.csv).

### Table 5: Complete 19-Job Hardware Provenance Ledger (`ibm_kingston`, `ibm_marrakesh`, `ibm_fez`)

| Campaign | Batch Label | Backend | IBM Job ID | Completion Timestamp (UTC) | PUBs | Physical Shots | Charged QPU (s) | Active Exec (s) |
| :--- | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_Interleaved_Master_ibm_kingston` | `ibm_kingston` | `dbk1h6d6p51s73clp0hg` | `2026-11-02T04:59:41Z` | 71 | 91,136 | 27.0s | 24.3674s |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_Interleaved_Master_ibm_marrakesh` | `ibm_marrakesh` | `dbk1hfl6p51s73clp140` | `2026-11-02T05:00:22Z` | 71 | 91,136 | 27.0s | 24.6964s |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_Interleaved_Master_ibm_fez` | `ibm_fez` | `dbk1hq0nceqs73al07v0` | `2026-11-02T05:01:00Z` | 71 | 91,136 | 26.0s | 24.0753s |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_T1_sabre_sequential_12pubs` | `ibm_kingston` | `dbk1i3l6p51s73clp22g` | `2026-11-02T05:01:13Z` | 12 | 12,288 | 6.0s | 3.2775s |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_T2_prometheus_sequential_12pubs` | `ibm_kingston` | `dbk1i6t6p51s73clp290` | `2026-11-02T05:01:27Z` | 12 | 12,288 | 5.0s | 3.2736s |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_T3_prometheus_packed_k4_3pubs` | `ibm_kingston` | `dbk1ia1kdqps7391ikgg` | `2026-11-02T05:01:36Z` | 3 | 3,072 | 3.0s | 0.8294s |
| **Campaign 1 (6Q-10Q)** | `QuantumOS_T4_prometheus_packed_k6_2pubs` | `ibm_kingston` | `dbk1ic9ffo6c73baki30` | `2026-11-02T05:01:46Z` | 2 | 2,048 | 3.0s | 0.5560s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_Interleaved_Master_ibm_marrakesh` | `ibm_marrakesh` | `dbk284hkdqps7391k5v0` | `2026-11-02T06:16:00Z` | 75 | 76,800 | 23.0s | 20.7671s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_Interleaved_Master_ibm_fez` | `ibm_fez` | `dbk2g9gnceqs73al2eig` | `2026-11-02T06:06:13Z` | 75 | 76,800 | 23.0s | 20.2298s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_Interleaved_Master_ibm_kingston` | `ibm_kingston` | `dbk2gipffo6c73bamlt0` | `2026-11-02T06:06:48Z` | 75 | 76,800 | 23.0s | 20.4548s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_T1_sabre_sequential_16pubs` | `ibm_marrakesh` | `dbk2gr1kdqps7391kq90` | `2026-11-02T06:08:09Z` | 16 | 16,384 | 7.0s | 4.4424s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_T2_prometheus_sequential_16pubs` | `ibm_marrakesh` | `dbk2hf9kdqps7391krmg` | `2026-11-02T06:08:30Z` | 16 | 16,384 | 7.0s | 4.3905s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_T3_prometheus_packed_3pubs_k556` | `ibm_marrakesh` | `dbk2hkl6p51s73clr60g` | `2026-11-02T06:09:22Z` | 3 | 3,072 | 3.0s | 0.8451s |
| **Campaign 2 (4Q-12Q)** | `HetQuantumOS_T4_prometheus_packed_2pubs_k88` | `ibm_marrakesh` | `dbk2i1d6p51s73clr6ug` | `2026-11-02T06:09:37Z` | 2 | 2,048 | 3.0s | 0.5663s |
| **Campaign 3 (Meta+TKET)** | `JointMeta_TKET_Interleaved_Master_ibm_kingston` | `ibm_kingston` | `dbkaag9ffo6c73bb4oa0` | `2026-11-02T15:00:38Z` | 26 | 26,624 | 9.0s | 7.1607s |
| **Campaign 3 (Meta+TKET)** | `JointMeta_TKET_Interleaved_Master_ibm_marrakesh` | `ibm_marrakesh` | `dbkab5t6p51s73cm9000` | `2026-11-02T15:04:12Z` | 26 | 26,624 | 10.0s | 7.3044s |
| **Campaign 3 (Meta+TKET)** | `JointMeta_TKET_Interleaved_Master_ibm_fez` | `ibm_fez` | `dbkacr9ffo6c73bb4rf0` | `2026-11-02T15:10:04Z` | 26 | 26,624 | 9.0s | 7.0641s |
| **Campaign 3 (Meta+TKET)** | `JointMeta_T5_tket_packed_2pubs_k88` | `ibm_marrakesh` | `dbkafj1kdqps739235eg` | `2026-11-02T15:10:13Z` | 2 | 2,048 | 3.0s | 0.5782s |
| **Campaign 3 (Meta+TKET)** | `JointMeta_T6_prometheus_metaselected_packed_2pubs_k88` | `ibm_marrakesh` | `dbkaflhkdqps739235hg` | `2026-11-02T15:10:21Z` | 2 | 2,048 | 3.0s | 0.5780s |
| **Grand Total (19 Jobs)** | **9 Interleaved Masters + 10 Timing Runs** | **3 IBM Heron QPUs** | **19 Verified Job IDs** | **2026-11-02 UTC** | **586** | **655,360** | **220.0s** | **178.4530s** |

---

## 6. Architecture & Standalone Offline Verification

```mermaid
flowchart LR
    A["Multi-Tenant Job Queue"] --> B["Multi-Compiler Pool (SABRE O3 / TKET / Prometheus)"]
    B --> C["GPU Subgraph Scorer & Joint (Compiler*, QPU*) Meta-Selector"]
    C --> D["Multi-QPU Spatial Multiplexer (IBM Heron / IQM / Braket)"]
    D --> E["Bit-Slice Demultiplexer & Qualification Auditor"]
```

Run the standalone offline verifier from the repository root (requires only Python 3 and `numpy`, zero cloud credentials):

```bash
python scripts/verify_spatial_multiplexing_campaigns.py
```

The verifier checks:
1. Canonical SHA-256 hashes of all three pre-execution commitment manifests (`661f49d8...`, `97bbb8f2...`, `78426d64...`) and strict chronological ordering (`pre_execution_lock_timestamp_utc < submission_timestamp`) across all 19 IBM Heron jobs.
2. Exact recomputation of all 1,140 scored workload evaluations (1,194 component circuit executions) from the raw per-PUB bitstring histograms in `data/*/jobs/*.json` and exact statevector reference distributions in `data/*/logical_circuits/*_ideal.npz` (max |Δ F_B| = 0.00 × 10⁰).
3. All matched same-qubit interference deltas, 4x replica effective sample sizes (N_eff = 4,017.8), pre-execution QPU and 9-way Joint `(Compiler*, QPU*)` selection regrets, and dedicated timing-isolation throughput ratios (T1-T6).
