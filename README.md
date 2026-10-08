# PHYSICAL VERIFICATION FOR TAPEOUT READINESS USING SKY130

**Author:** Hozefa Travadi ([@Hellbot007](https://github.com/Hellbot007))  
**Workshop:** VSD Physical Verification Workshop  
**Target PDK:** SkyWater SKY130 Open PDK (130nm node)

---

## Workshop Structure & Course Roadmap

This repository documents the 10-day physical verification journey for tapeout readiness using SkyWater SKY130 PDK and open-source EDA tools (Magic, Xschem, Ngspice, Netgen, KLayout).

### 📚 Modules Overview
* **Module 1 (Part 1: `PV_D1SK1`):** Introduction to SkyWater SKY130 PDK & Open-Source EDA Tools (Lectures L1 to L6) — **DAY 1**
* **Module 1 (Part 2: `PV_D1SK2`):** Tool Installations and Basic DRC/LVS Design Flow Labs (Lectures L1 to L6) — **DAY 2**
* **Module 2 (Part 1: `PV_D2SK1`):** Introduction to DRC and LVS Theory (Lectures L1 to L9) — **DAY 3**
* **Module 2 (Part 2: `PV_D2SK2`):** Labs for GDS Read/Write, Extraction, DRC, LVS and XOR Setup (Lectures L1 to L7) — **DAY 4**
* **Module 3:** *(Upcoming)* — **DAY 5 & DAY 6**
* **Module 4:** *(Upcoming)* — **DAY 7 & DAY 8**
* **Module 5:** *(Upcoming)* — **DAY 9 & DAY 10**

---

## 📅 Daily Progress & Content Index

| Day | Module & Content Summary | Quick Link |
| :--- | :--- | :---: |
| **DAY 1** | **PV_D1SK1 (Lectures L1 to L6):** Intro to SkyWater PDK (130nm node), Open-Source EDA Tools (`open_pdks`, Magic, Xschem, Ngspice, Netgen), Front-End & Metal Stack Layers, Device Primitives, Standard Cell Libraries & Naming Conventions, I/O Pad Cells, and Open Design Flows. *Includes 11 detailed screenshots.* | [View Day 1](./DAY%201/README.md) |
| **DAY 2** | **PV_D1SK2 (Lectures L1 to L6):** Hands-on Lab Design Flow: Check Tool Installations, Creating Sky130 Device Layout in Magic, Creating Schematic in Xschem, Symbol Export & SPICE Netlisting, Inverter Layout & Routing, Magic DRC Check (0 errors), Netgen LVS, and Post-Layout Parasitic Simulation. *Includes 14 detailed screenshots.* | [View Day 2](./DAY%202/README.md) |
| **DAY 3** | **PV_D2SK1 (Lectures L1 to L9 - Theory):** Introduction to DRC and LVS: GDSII File Format Architecture, Extraction Commands & Styles in Magic, Advanced Extraction Options, GDS Read/Write Controls, DRC Rule Definitions, Extraction Errors, Netgen LVS Setup (`setup.tcl`), and Verification by Boolean XOR. | [View Day 3](./DAY%203/README.md) |
| **DAY 4** | **PV_D2SK2 (Lectures L1 to L7 - Labs):** Hands-on Labs for GDS Read/Write, Port/Label Creation, LEF Abstract Views, Parasitic RC Extraction Flow, DRC Debugging, Netgen LVS Matching, and Automated KLayout XOR Comparison Setup. | [View Day 4](./DAY%204/README.md) |
| **DAY 5** | *Upcoming Module 3 Labs & Lectures* | [View Day 5](./DAY%205/README.md) |
| **DAY 6** | *Upcoming Module 3 Labs & Lectures* | [View Day 6](./DAY%206/README.md) |
| **DAY 7** | *Upcoming Module 4 Labs & Lectures* | [View Day 7](./DAY%207/README.md) |
| **DAY 8** | *Upcoming Module 4 Labs & Lectures* | [View Day 8](./DAY%208/README.md) |
| **DAY 9** | *Upcoming Module 5 Labs & Lectures* | [View Day 9](./DAY%209/README.md) |
| **DAY 10** | *Upcoming Module 5 Labs & Lectures* | [View Day 10](./DAY%2010/README.md) |

---

## 🛠️ Open-Source Tool Suite Used
* **Magic VLSI:** Layout editing, DRC extraction & GDSII generation
* **Xschem:** Schematic capture & SPICE netlist export
* **Netgen:** LVS (Layout vs. Schematic) verification tool
* **Ngspice:** SPICE circuit & post-layout transient simulation
* **KLayout:** GDSII layout viewer & XOR comparison engine
* **open_pdks:** Open-Source SkyWater 130nm PDK integration framework
