# PHYSICAL VERIFICATION FOR TAPEOUT READINESS USING SKY130

**Author:** Hozefa Travadi ([@Hellbot007](https://github.com/Hellbot007))  
**Workshop:** VSD Physical Verification Workshop  
**Target PDK:** SkyWater SKY130 Open PDK (130nm node)

---

## Workshop Structure & Course Roadmap

This repository documents the complete 10-day physical verification journey for tapeout readiness using SkyWater SKY130 PDK and open-source EDA tools (Magic, Xschem, Ngspice, Netgen, KLayout, OpenLANE).

### 📚 Modules Overview
* **Module 1 (Part 1: `PV_D1SK1`):** Introduction to SkyWater SKY130 PDK & Open-Source EDA Tools (Lectures L1 to L6) — **DAY 1**
* **Module 1 (Part 2: `PV_D1SK2`):** Tool Installations and Basic DRC/LVS Design Flow Labs (Lectures L1 to L6) — **DAY 2**
* **Module 2 (Part 1: `PV_D2SK1`):** Introduction to DRC and LVS Theory (Lectures L1 to L9) — **DAY 3**
* **Module 2 (Part 2: `PV_D2SK2`):** Labs for GDS Read/Write, Extraction, DRC, LVS and XOR Setup (Lectures L1 to L7) — **DAY 4**
* **Module 3 (Part 1: `PV_D3SK1`):** Introduction to DRC Rules Theory (Lectures L1 to L9) — **DAY 5**
* **Module 3 (Part 2: `PV_D3SK2`):** Labs for All DRC Rules (Lectures L1 to L11) — **DAY 6**
* **Module 4 (`PV_D4SK1`):** Understanding PNR and Physical Verification (Lectures L1 to L6) — **DAY 7**
* **Module 5 (Part 1: `PV_D5SK1`):** Fundamentals of LVS Theory (Lectures L1 to L9) — **DAY 8**
* **Module 5 (Part 2: `PV_D5SK2`):** LVS Labs Part 1 (Lectures L1 to L6) — **DAY 9**
* **Module 5 (Part 2: `PV_D5SK2`):** LVS Labs Part 2 & Tapeout Signoff (Lectures L7 to L11) — **DAY 10**

> [!NOTE]
> **Module 5 Documentation Note:**
> Module 5 documentation (`PV_D5SK1` & `PV_D5SK2`) reflects the author's hands-on observations and current understanding of the respective lectures and lab exercises.

---

## 📅 Daily Progress & Content Index

| Day | Module & Content Summary | Quick Link |
| :--- | :--- | :---: |
| **DAY 1** | **PV_D1SK1 (Lectures L1 to L6):** Intro to SkyWater PDK (130nm node), Open-Source EDA Tools (`open_pdks`, Magic, Xschem, Ngspice, Netgen), Front-End & Metal Stack Layers, Device Primitives, Standard Cell Libraries & Naming Conventions, I/O Pad Cells, and Open Design Flows. *Includes 11 detailed screenshots.* | [View Day 1](./DAY%201/README.md) |
| **DAY 2** | **PV_D1SK2 (Lectures L1 to L6):** Hands-on Lab Design Flow: Check Tool Installations, Creating Sky130 Device Layout in Magic, Creating Schematic in Xschem, Symbol Export & SPICE Netlisting, Inverter Layout & Routing, Magic DRC Check (0 errors), Netgen LVS, and Post-Layout Parasitic Simulation. *Includes 14 detailed screenshots.* | [View Day 2](./DAY%202/README.md) |
| **DAY 3** | **PV_D2SK1 (Lectures L1 to L9 - Theory):** Introduction to DRC and LVS: GDSII File Format Architecture, Extraction Commands & Styles in Magic, Advanced Extraction Options, GDS Read/Write Controls, DRC Rule Definitions, Extraction Errors, Netgen LVS Setup (`setup.tcl`), and Verification by Boolean XOR. | [View Day 3](./DAY%203/README.md) |
| **DAY 4** | **PV_D2SK2 (Lectures L1 to L7 - Labs):** Hands-on Labs for GDS Read/Write, Port/Label Creation, LEF Abstract Views, Parasitic RC Extraction Flow, DRC Debugging, Netgen LVS Matching, and Automated KLayout XOR Comparison Setup. | [View Day 4](./DAY%204/README.md) |
| **DAY 5** | **PV_D3SK1 (Lectures L1 to L9 - Theory):** Introduction to DRC Rules: Silicon Manufacturing Constraints, Backend Metal Rules, Local Interconnect Rules, FEOL Transistor/Implants/Well Rules, Deep N-Well & High-Voltage Rules, Device Rules, Latch-up/Antenna/Stress Rules, Layer Density Limits, and DFM/ERC Rules. | [View Day 5](./DAY%205/README.md) |
| **DAY 6** | **PV_D3SK2 (Lectures L1 to L11 - Labs):** Labs for All DRC Rules: Width & Spacing Rules, Wide Metal & Notch Rules, Via Cuts & Auto-Vias, Min Area & Hole Rules, Deep N-Well Checks, Derived Layers, P-Cells, Angle Errors, Unimplemented KLayout Rules, Latch-up/Antenna Checks, and Density Dummy Fill. | [View Day 6](./DAY%206/README.md) |
| **DAY 7** | **PV_D4SK1 (Lectures L1 to L6):** Understanding PNR and Physical Verification: OpenLANE Flow Architecture, Automated RTL2GDS Setup, Interactive OpenLANE Run Steps (`prep`, `synthesis`, `floorplan`, `placement`, `cts`, `routing`), Techniques to Prevent PNR DRC Errors, and Manual ECO Layout Fixes in Magic. | [View Day 7](./DAY%207/README.md) |
| **DAY 8** | **PV_D5SK1 (Lectures L1 to L9 - Theory):** Fundamentals of LVS: Physical Verification of Extracted Netlists, Graph Isomorphism Matching, LVS vs Simulation Netlists, Netgen Core Matching Algorithm, Prematch Analysis & Hierarchical Flattening, Pin/Property Checking, Series-Parallel Reduction, Symmetry Breaking, and Result Log Interpretation. | [View Day 8](./DAY%208/README.md) |
| **DAY 9** | **PV_D5SK2 (Lectures L1 to L6 - Labs Part 1):** LVS Labs Part 1: Baseline Inverter LVS, Hierarchical Subcircuits, Blackboxing IP Blocks, SPICE Primitive Component Matching, and Analog Power-On Reset (POR) Circuit Block LVS Debugging (Parts 1 & 2). | [View Day 9](./DAY%209/README.md) |
| **DAY 10** | **PV_D5SK2 (Lectures L7 to L11 - Labs Part 2 & Signoff):** LVS Labs Part 2: Standard Cell Layout vs Verilog, Macro LVS, Digital Phase-Locked Loop (PLL) Mixed-Signal LVS (Parts 1 & 2), Debugging Device Property Errors, and Final Tapeout Readiness Signoff Checklist. | [View Day 10](./DAY%2010/README.md) |

---

## 🛠️ Open-Source Tool Suite Used
* **Magic VLSI:** Layout editing, DRC extraction & GDSII generation
* **Xschem:** Schematic capture & SPICE netlist export
* **Netgen:** LVS (Layout vs. Schematic) verification tool
* **Ngspice:** SPICE circuit & post-layout transient simulation
* **KLayout:** GDSII layout viewer & XOR comparison engine
* **OpenLANE / OpenROAD:** Automated RTL-to-GDSII ASIC placement & routing flow
* **open_pdks:** Open-Source SkyWater 130nm PDK integration framework
