# PHYSICAL VERIFICATION FOR TAPEOUT READINESS USING SKY130

**Author:** Hozefa Travadi ([@Hellbot007](https://github.com/Hellbot007))  
**Workshop:** VSD Physical Verification Workshop  
**Target PDK:** SkyWater SKY130 Open PDK (130nm node)

---

## Workshop Structure & Course Roadmap

This repository documents the 10-day physical verification journey for tapeout readiness using SkyWater SKY130 PDK and open-source EDA tools (Magic, Xschem, Ngspice, Netgen).

### 📚 Modules Covered
* **Module 1:** Introduction to SkyWater SKY130 (`PV_D1SK1`)
* **Module 2:** Tool Installations & Basic DRC/LVS Design Flow (`PV_D1SK2`)
* **Module 3:** *(Upcoming)*
* **Module 4:** *(Upcoming)*
* **Module 5:** *(Upcoming)*

---

## 📅 Daily Progress & Content Index

| Day | Module & Content Summary | Quick Link |
| :--- | :--- | :---: |
| **DAY 1** | **Module 1 (Part 1):** Intro to SkyWater PDK (130nm node), Open-Source EDA Tools Ecosystem (`open_pdks`, Magic, Xschem, Ngspice, Netgen), and Sky130 Metal Stack & Layers. Includes diagrams & layout analysis. | [View Day 1](./DAY%201/README.md) |
| **DAY 2** | **Module 1 (Part 2):** Sky130 Primitive Devices (`nfet_01v8`, `pfet_01v8`, resistors, caps), Standard Cell Library Families (`hd`, `hs`, `ms`, `ls`), File Formats (`GDSII`, `LEF`, `LIB`, `SPICE`), and Open Tool Flows. | [View Day 2](./DAY%202/README.md) |
| **DAY 3** | **Module 2 (Lab Part 1):** EDA Tools Environment Setup & Verification, Placing Sky130 Device Layouts in Magic (NMOS/PMOS + Guard Rings), and Schematic Capture in Xschem. | [View Day 3](./DAY%203/README.md) |
| **DAY 4** | **Module 2 (Lab Part 2):** Symbol Generation & Netlist Export in Xschem, Inverter Layout & Routing in Magic, DRC Cleaning (0 errors), LVS Verification with Netgen, and Post-Layout Parasitic Extraction / Ngspice Simulation. | [View Day 4](./DAY%204/README.md) |
| **DAY 5** | *Upcoming Labs & Lectures* | [View Day 5](./DAY%205/README.md) |
| **DAY 6** | *Upcoming Labs & Lectures* | [View Day 6](./DAY%206/README.md) |
| **DAY 7** | *Upcoming Labs & Lectures* | [View Day 7](./DAY%207/README.md) |
| **DAY 8** | *Upcoming Labs & Lectures* | [View Day 8](./DAY%208/README.md) |
| **DAY 9** | *Upcoming Labs & Lectures* | [View Day 9](./DAY%209/README.md) |
| **DAY 10** | *Upcoming Labs & Lectures* | [View Day 10](./DAY%2010/README.md) |

---

## 🛠️ Open-Source Tool Suite Used
* **Magic VLSI:** Layout editing, DRC extraction & GDSII generation
* **Xschem:** Schematic capture & SPICE netlist export
* **Netgen:** LVS (Layout vs. Schematic) verification tool
* **Ngspice:** SPICE circuit & post-layout transient simulation
* **open_pdks:** Open-Source SkyWater 130nm PDK integration framework
