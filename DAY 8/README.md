# DAY 8: Module 5 - PV_D5SK1: Fundamentals of LVS (Theory)

## Overview
Day 8 covers **Module 5 (Part 1: PV_D5SK1 - Lectures L1 to L9)**. This theoretical module provides a comprehensive foundation in Layout Versus Schematic (LVS) verification, graph matching algorithms, Netgen core engine architecture, prematch analysis, series-parallel reduction, symmetry resolution, and LVS log interpretation.

---

## Table of Contents
1. [PV_D5SK1_L1: Physical Verification Of Extracted Netlist](#pv_d5sk1_l1-physical-verification-of-extracted-netlist)
2. [PV_D5SK1_L2: How LVS Matching Works](#pv_d5sk1_l2-how-lvs-matching-works)
3. [PV_D5SK1_L3: LVS Netlist Vs Simulation Netlists](#pv_d5sk1_l3-lvs-netlist-vs-simulation-netlists)
4. [PV_D5SK1_L4: The Netgen Core Matching Algorithm](#pv_d5sk1_l4-the-netgen-core-matching-algorithm)
5. [PV_D5SK1_L5: Netgen Prematch Analysis, Hierarchical Checking And Flattening](#pv_d5sk1_l5-netgen-prematch-analysis-hierarchical-checking-and-flattening)
6. [PV_D5SK1_L6: Pin Checking And Property Checking](#pv_d5sk1_l6-pin-checking-and-property-checking)
7. [PV_D5SK1_L7: Series Parallel Combining](#pv_d5sk1_l7-series-parallel-combining)
8. [PV_D5SK1_L8: Symmetry Breaking](#pv_d5sk1_l8-symmetry-breaking)
9. [PV_D5SK1_L9: Interpreting Netgen Results](#pv_d5sk1_l9-interpreting-netgen-results)

---

## PV_D5SK1_L1: Physical Verification Of Extracted Netlist

While DRC verifies geometric mask rules, **LVS verifies electrical circuit topology**.

* **Extraction Process:**
  * Magic extracts physical geometry boundaries into `.ext` files (`extract all`).
  * `ext2spice lvs` formats extracted geometries into an LVS-ready SPICE netlist (`.spice`).
* **Extracted Netlist Components:**
  * Primitive transistor models (`sky130_fd_pr__nfet_01v8`, `pfet_01v8`).
  * Node connectivity definitions and pin labels.
  * Subcircuit declarations (`.subckt`).

---

## PV_D5SK1_L2: How LVS Matching Works

LVS compares the extracted layout topology against the reference schematic topology using **Graph Isomorphism**.

```
Schematic Netlist (Graph A)                 Layout Netlist (Graph B)
     [N1] --- (M1) --- [N2]                     [net1] --- (X1) --- [net2]
            |                                                |
          (M2)                                             (X2)
            |                                                |
         [GND]                                            [VGND]
```

* **Graph Representation:**
  * **Nodes:** Electrical nets ($V_{DD}$, $V_{SS}$, input/output signals).
  * **Edges:** Transistors, resistors, capacitors, and diodes.
* **Goal:** Determine if there exists a 1-to-1 mapping between nodes and edges of Graph A and Graph B.

---

## PV_D5SK1_L3: LVS Netlist Vs Simulation Netlists

| Feature | LVS Netlist (`ext2spice lvs`) | Simulation Netlist (`ext2spice`) |
| :--- | :--- | :--- |
| **Parasitic Capacitance** | Stripped / Disabled | Included ($C_{parasitic}$) |
| **Parasitic Resistance** | Stripped / Disabled | Included ($R_{parasitic}$) |
| **Hierarchy** | Preserves `.subckt` blocks | Flattens for SPICE solver |
| **Parallel Devices** | Combined ($W_{total} = \sum W_i$) | Retained as discrete devices |
| **Primary Purpose** | Fast graph isomorphism matching in Netgen | Precise transient analysis in Ngspice |

---

## PV_D5SK1_L4: The Netgen Core Matching Algorithm

Netgen uses an iterative partition refinement algorithm:

1. **Initial Partitioning:**
   * Groups nets and devices based on static attributes (e.g. number of connected terminals, device type).
2. **Signature Propagation:**
   * Computes a unique hash signature for each net based on the signatures of adjacent devices and nets.
3. **Iterative Refinement:**
   * Iteratively splits net groups until every node receives a unique signature.
4. **Isomorphism Decision:**
   * If all signatures between Layout and Schematic match 1-to-1, the circuits are verified identical.

---

## PV_D5SK1_L5: Netgen Prematch Analysis, Hierarchical Checking And Flattening

* **Prematch Analysis:**
  * Compares overall netlist statistics (total gate count, net count, subcircuit definitions) prior to graph reduction.
* **Hierarchical LVS:**
  * Verifies individual subcircuits separately (`.subckt`) before verifying the top-level assembly.
  * Drastically reduces memory overhead and runtime for large SOC designs.
* **Selective Flattening:**
  * If a subcircuit fails to match due to local layout differences, Netgen can selectively flatten that block into its parent hierarchy to resolve local routing discrepancies.

---

## PV_D5SK1_L6: Pin Checking And Property Checking

### 1. Pin Checking
* Verifies port names, pin counts, and pin directions between schematic symbols and layout ports.
* Detects missing power/ground pins or swapped signal ports.

### 2. Property Checking
* Compares device parameters ($W$ width, $L$ length, $R$ resistance, $C$ capacitance) against user tolerances.
* Configured in Netgen `setup.tcl`:
  ```tcl
  # Property matching tolerance (e.g. 1% tolerance on W and L)
  property default FET width 0.01
  property default FET length 0.01
  ```

---

## PV_D5SK1_L7: Series Parallel Combining

Before running graph matching, Netgen simplifies netlists by performing device reduction:

* **Parallel Transistors:**
  * Transistors connected to identical Gate, Source, and Drain nets are combined:
    $$W_{\text{combined}} = \sum_{i=1}^n W_i, \quad L_{\text{combined}} = L$$
* **Series Transistors:**
  * Series MOS devices with common gates are merged into equivalent single devices.
* **Parallel Resistors & Capacitors:**
  * Parallel passive devices are merged into single equivalent elements ($R_{eq}$, $C_{eq}$).

---

## PV_D5SK1_L8: Symmetry Breaking

Symmetric circuits (e.g. differential amplifiers, cross-coupled latches, ring oscillators) produce identical connectivity signatures for symmetric nodes.

```
       VDD
        |
     +--+--+
     |     |
    M1    M2   <-- Symmetric Transistors
     |     |
    OutA  OutB <-- Identical Topological Hashes
```

* **Symmetry Resolution in Netgen:**
  * Netgen detects symmetric ambiguity and applies deterministic symmetry breaking.
  * Uses pin label anchors or temporary node assignments to resolve symmetric ambiguity without generating false error reports.

---

## PV_D5SK1_L9: Interpreting Netgen Results

Reading the Netgen output comparison file (`lvs_comp.out`):

### 1. Match Verdicts
* **Successful Match:**
  ```text
  Result: Circuits match uniquely.
  ```
* **Property Mismatch:**
  ```text
  Result: Property errors found.
  ```
* **Topology Mismatch:**
  ```text
  Result: Circuits do not match.
  ```

### 2. Discrepancy Diagnostics
* **Unmatched Nets:** Lists specific nets present in schematic but missing in layout (Open circuit) or merged in layout (Short circuit).
* **Unmatched Devices:** Identifies missing or extra transistors.
* **Property Differences:** Reports exact $W$ or $L$ values that exceed defined tolerance thresholds.
