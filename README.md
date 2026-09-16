# Direct Memory Access (DMA) Data Polling Controller

![FPGA](https://img.shields.io/badge/Hardware-Intel_Cyclone_II-blue)
![Language](https://img.shields.io/badge/Language-Verilog_HDL-orange)
![Synthesis](https://img.shields.io/badge/Synthesis-Quartus_II-lightgrey)

## Overview
In modern embedded computing, processor-controlled data movement requires the CPU to manage repetitive memory access operations, increasing processor overhead and reducing system efficiency[cite: 1]. 

This project implements a lightweight, high-performance Direct Memory Access (DMA) Data Polling Controller written entirely in Verilog HDL[cite: 1]. Rather than utilizing interrupt-driven CPU routines, this controller acts as an autonomous hardware courier. It actively polls for hardware triggers with zero latency and executes high-speed memory bursts, preserving 100% of the CPU's processing bandwidth for high-level computation.

## Hardware Specifications
* **Target Device:** Intel Cyclone II EP2C35F672C6 FPGA[cite: 1]
* **System Clock:** 50 MHz[cite: 1]
* **Internal Storage:** 256x8-bit synchronous M4K RAM block[cite: 1]
* **Input/Output:** 16 Toggle Switches (Data/Address), 2 Push-Buttons (Control), Seven-Segment Hexadecimal Displays (Verification)[cite: 1]

## System Architecture
The architecture is partitioned into five specialized Verilog modules to ensure clean signal routing and combinational logic efficiency:

1. **Debouncer (`debouncer.v`):** Filters mechanical spring oscillations from the physical start button, translating noisy voltage transients into a single, clean logic pulse to protect the state machine from chaotic triggering[cite: 1].
2. **DMA FSM (`dma_fsm.v`):** The control brain driven by the 50 MHz clock[cite: 1]. It features a 5-bit burst counter tracking 16 contiguous memory transfers and manages a deterministic `IDLE` -> `WRITE` -> `READ` -> `DONE` execution sequence[cite: 1].
3. **Address Generator (`address_generator.v`):** A combinational hardware routing engine. It dynamically computes the final memory destination by continuously adding a static Base Address to a moving execution offset[cite: 1].
4. **DMA Memory (`dma_memory.v`):** An instantiated 256-byte internal SRAM block for data payload storage[cite: 1].
5. **HEX Decoder (`hex_decoder.v`):** Decodes the binary memory data directly to the physical HEX0 and HEX1 output displays for real-time hardware observation[cite: 1].

## Key Engineering Features
* **Zero-Latency Hardware Polling:** The FSM monitors the start trigger on every single clock edge while in the `IDLE` state, initiating the memory burst the exact nanosecond the data is ready[cite: 1].
* **Dynamic Data Generation:** To overcome physical switch limitations during the 16-cycle write burst, the hardware dynamically adds the FSM burst counter to the input payload on every clock cycle. This generates a sequential "trail" of 16 mathematically unique bytes.
* **Manual Verification Multiplexing:** Because a 50 MHz burst completes in 320 nanoseconds, a combinational multiplexer hands address control to the manual toggle switches during the `READ` state, allowing for human-speed physical verification of the stored memory blocks[cite: 1].

## Synthesized Performance & Footprint
The RTL design was synthesized, fitted, and routed using Quartus II v13.0.1 SP1 with zero synthesis errors[cite: 1]. The architecture achieved enterprise-grade efficiency metrics:

* **Logic Elements:** 86 / 33,216 (< 1%)[cite: 1]
* **Dedicated Logic Registers:** 33[cite: 1]
* **Total Pins:** 51 / 475 (11%)[cite: 1]
* **Memory Bits:** 2,048 / 483,840 (< 1%)[cite: 1]
* **Unrestricted Fmax:** 288.77 MHz[cite: 1]

By restricting the logic footprint to just 86 LEs, the vast majority of the FPGA fabric remains completely free for CPU instantiation and peripheral routing, proving the module's viability as a lightweight system offload engine.

## Getting Started
### Prerequisites
* Quartus II Web Edition (v13.0.1 SP1 recommended for Cyclone II support)[cite: 1]
* ModelSim-Altera for RTL simulation[cite: 1]
* Altera DE2 Development Board (or equivalent Cyclone II hardware)

### Build Instructions
1. Clone this repository to your local machine.
2. Open Quartus II and create a new project targeting the `EP2C35F672C6` device[cite: 1].
3. Add all `.v` files from the `/src` directory to the project.
4. Import the provided `.qsf` pin assignment file to map the I/O to the DE2 board switches and displays.
5. Run full compilation (Analysis & Synthesis, Fitter, Assembler, Timing Analyzer)[cite: 1].
6. Flash the `.sof` file to the FPGA via the Quartus Programmer tool using a USB-Blaster.

## Team / Contributors
**CPE 309 - Group 13**[cite: 1]
* Babarinlo Adedotun Temitope[cite: 1]
* Idowu Ibraheem Alaba 
* Adeosun Ezekiel Ayokunle
* *(Add the remaining group members here)*
