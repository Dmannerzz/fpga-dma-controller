# Direct Memory Access (DMA) Data Polling Controller

![FPGA](https://img.shields.io/badge/Hardware-Intel_Cyclone_II-blue)
![Language](https://img.shields.io/badge/Language-Verilog_HDL-orange)
![Synthesis](https://img.shields.io/badge/Synthesis-Quartus_II-lightgrey)

## Overview
In modern embedded computing, processor-controlled data movement requires the CPU to manage repetitive memory access operations, increasing processor overhead and reducing system efficiency. 

This project implements a lightweight, high-performance Direct Memory Access (DMA) Data Polling Controller written entirely in Verilog HDL. Rather than utilizing interrupt-driven CPU routines, this controller acts as an autonomous hardware courier. It actively polls for hardware triggers with zero latency and executes high-speed memory bursts, preserving 100% of the CPU's processing bandwidth for high-level computation.

## Hardware Specifications
* **Target Device:** Intel Cyclone II EP2C35F672C6 FPGA
* **System Clock:** 50 MHz
* **Internal Storage:** 256x8-bit synchronous M4K RAM block
* **Input/Output:** 16 Toggle Switches (Data/Address), 2 Push-Buttons (Control), Seven-Segment Hexadecimal Displays (Verification)

## System Architecture
The architecture is partitioned into five specialized Verilog modules to ensure clean signal routing and combinational logic efficiency:

1. **Debouncer (`debouncer.v`):** Filters mechanical spring oscillations from the physical start button, translating noisy voltage transients into a single, clean logic pulse to protect the state machine from chaotic triggering.
2. **DMA FSM (`dma_fsm.v`):** The control brain driven by the 50 MHz clock. It features a 5-bit burst counter tracking 16 contiguous memory transfers and manages a deterministic `IDLE` -> `WRITE` -> `READ` -> `DONE` execution sequence.
3. **Address Generator (`address_generator.v`):** A combinational hardware routing engine. It dynamically computes the final memory destination by continuously adding a static Base Address to a moving execution offset.
4. **DMA Memory (`dma_memory.v`):** An instantiated 256-byte internal SRAM block for data payload storage.
5. **HEX Decoder (`hex_decoder.v`):** Decodes the binary memory data directly to the physical HEX0 and HEX1 output displays for real-time hardware observation.

## Key Engineering Features
* **Zero-Latency Hardware Polling:** The FSM monitors the start trigger on every single clock edge while in the `IDLE` state, initiating the memory burst the exact nanosecond the data is ready.
* **Dynamic Data Generation:** To overcome physical switch limitations during the 16-cycle write burst, the hardware dynamically adds the FSM burst counter to the input payload on every clock cycle. This generates a sequential "trail" of 16 mathematically unique bytes.
* **Manual Verification Multiplexing:** Because a 50 MHz burst completes in 320 nanoseconds, a combinational multiplexer hands address control to the manual toggle switches during the `READ` state, allowing for human-speed physical verification of the stored memory blocks.

## Synthesized Performance & Footprint
The RTL design was synthesized, fitted, and routed using Quartus II v13.0.1 SP1 with zero synthesis errors. The architecture achieved enterprise-grade efficiency metrics:

* **Logic Elements:** 86 / 33,216 (< 1%)
* **Dedicated Logic Registers:** 33
* **Total Pins:** 51 / 475 (11%)
* **Memory Bits:** 2,048 / 483,840 (< 1%)
* **Unrestricted Fmax:** 288.77 MHz

By restricting the logic footprint to just 86 LEs, the vast majority of the FPGA fabric remains completely free for CPU instantiation and peripheral routing, proving the module's viability as a lightweight system offload engine.

## Getting Started
### Prerequisites
* Quartus II Web Edition (v13.0.1 SP1 recommended for Cyclone II support)
* ModelSim-Altera for RTL simulation
* Altera DE2 Development Board (or equivalent Cyclone II hardware)

### Build Instructions
1. Clone this repository to your local machine.
2. Open Quartus II and create a new project targeting the `EP2C35F672C6` device.
3. Add all `.v` files from the `/src` directory to the project.
4. Import the provided `.qsf` pin assignment file to map the I/O to the DE2 board switches and displays.
5. Run full compilation (Analysis & Synthesis, Fitter, Assembler, Timing Analyzer).
6. Flash the `.sof` file to the FPGA via the Quartus Programmer tool using a USB-Blaster.

## Team / Contributors
**CPE 309 - Group 13**

| Name | Matriculation Number |
| :--- | :--- |
| **Akinsunmade Temitope Ayodeji** | `CSC/2022/060` |
| **Ijiwoye Ibrahim Adekunle** | `CSC/2022/125` |
| **Babarinlo Adedotun Temitope** | `CSC/2022/090` |
| **Kolapo Oladotun Olumoroti** | `CSC/2022/133` |
| **Aderibigbe Emmanuel Semilore** | `CSC/2022/029` |
| **Akinyele Ayomide Victor** | `CSC/2022/066` |
| **Aminu Khadijah Funmilayo** | `CSC/2022/283` |
