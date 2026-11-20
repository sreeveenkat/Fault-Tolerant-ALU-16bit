# 🔐 Fault-Tolerant ALU  
### Multi-Level Error Detection using Parity Prediction, Carry Checking & Residue Codes (mod-3 / mod-5)

✔ Full RTL → GDSII implementation using Cadence Innovus  

This project implements a **Fault-Tolerant Arithmetic Logic Unit (ALU)** in Verilog with multiple parallel error detection techniques designed for reliable digital systems.

---

## 🚀 Key Features

- ✅ Parity Prediction  
- ✅ Carry Chain Checking  
- ✅ Residue Code Checking (mod-3 and mod-5)  
- ✅ Multi-Level Error Decision Logic  

---

## 🎯 Applications

- Fault-resilient VLSI systems  
- AI accelerator datapaths  
- Safety-critical arithmetic units  
- FPGA/ASIC research implementations  

---
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/7e8787a6-bbf7-4b03-8904-6c3337e5eac5" />



---

## 🧠 System Overview

The design consists of two primary sections:

---

### 1️⃣ ALU Core (Functional Unit)

- Performs arithmetic and logical operations  
- Generates result and internal carry chain  
- Parameterizable bit-width (8/16/32-bit scalable)  

#### Supported Operations

| Opcode | Operation |
|--------|----------|
| 000 | ADD |
| 001 | SUB |
| 010 | AND |
| 011 | OR |
| 100 | XOR |
| 101 | Shift Left |
| 110 | Shift Right |

---

### 2️⃣ Error Detection Units (Parallel Checking)

Three independent mechanisms validate computation simultaneously.

---

#### 🔹 Parity Prediction

- Input parity: `P(A) ⊕ P(B)`  
- Compared with: `P(Result)`  

If mismatch → `parity_error = 1`

✔ Low area overhead  
✔ Fast detection  
⚠ May miss some multi-bit symmetric errors  

---

#### 🔹 Carry Chain Checking

- Monitors internal carry propagation  
- Ensures carry consistency across stages  

If invalid relation → `carry_error = 1`

✔ Strong for arithmetic datapath faults  

---

#### 🔹 Residue Code Checking (mod-3 & mod-5)

For addition: (A + B) mod m == (A mod m + B mod m) mod m


Where `m = 3 or 5`

**Process:**
- Compute residue of A  
- Compute residue of B  
- Compute residue of Result  
- Compare predicted vs actual  

If mismatch → `residue_error = 1`

✔ Detects multi-bit arithmetic faults  
✔ Higher coverage than parity alone  

---

## 🔎 Multi-Level Error Decision

```verilog
error_flag = parity_error
           | carry_error
           | residue3_error
           | residue5_error;
```

---

## 🏭 RTL → GDSII Design Flow

```
Verilog RTL
    ↓
Functional simulation (self-checking testbench + SVA)
    ↓
Synthesis
    ↓
Floorplanning → Placement → Clock Tree Synthesis → Routing
    ↓
Static Timing Analysis
    ↓
Physical verification (DRC)
    ↓
GDSII
```

---

## 📊 Physical Design Results (90 nm, Cadence Innovus)

| Metric | Value |
|---|---|
| Total cell area | 6,868.11 µm² |
| Total instances | 979 cells |
| Clock frequency | 100 MHz |
| Worst setup slack | +0.061 ns (timing met) |
| DRC violations | 0 |
| Connectivity | Clean |
| Supply voltage | 0.9 V |

Known issue: power-net connection warnings remain (the `globalNetConnect` setup needs refinement).

![gds_diagram](https://github.com/user-attachments/assets/bb4857d4-26c9-45a3-8733-5c1fdfa41c6f)

---

## 🧪 Verification

`testbench.v` is a self-checking testbench with SystemVerilog Assertions:

| Assertion | Checks |
|---|---|
| `assert_zero` | `zero` is set whenever `result == 0` |
| `assert_error` | the combined `error` flag is set whenever any individual checker fires |
| `assert_logical` | no error is flagged on logical/shift operations |
| `p_reset_clears` | reset clears `result`, `cout`, `zero`, `overflow` and `error` |

What each checker catches:

- **Parity:** single-bit (odd-weight) errors
- **Carry checking:** faults in the arithmetic carry chain
- **Residue codes (mod-3, mod-5):** multi-bit arithmetic errors

---

## 🧰 Tools

| Step | Tool |
|---|---|
| RTL | Verilog (`alu.v`) |
| Testbench | SystemVerilog assertions (`testbench.v`) |
| Simulation | ModelSim / Vivado XSIM |
| Synthesis | Cadence Genus |
| Place and route | Cadence Innovus |
