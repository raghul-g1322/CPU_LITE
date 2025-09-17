# CPU Lite – RISC CPU with SRAM Buffer

## 📌 Project Overview
CPU Lite is a simple **RISC-based CPU** designed with a **small instruction set** and optimized for 
fast memory access using a synchronous **SRAM buffer**.  
The CPU operates at **150 MHz**, while Program and Data Memory operate at **70 MHz**.  
The SRAM buffer bridges this clock domain difference and provides caching functionality 
for LOAD/STORE operations.

---

## ⚙️ Specifications

### 🔹 Register Set
- 16 General Purpose Registers (R0 – R15)
- Program Counter (PC)

### 🔹 Memory
- **Program Memory (PROG):** 4K x 32
- **Data Memory (DATA):** 4K x 32
- **SRAM Buffer:** 1K x 32  
  - First access point for LOAD/STORE  
  - Operates at CPU clock (150 MHz)  
  - Acts as a cache with hit/miss detection  

---

## 🏗️ Micro-Architecture

- Block diagram with CPU, SRAM buffer, Data Memory, and Program Memory
- SRAM Control Logic:
  - Detects hit/miss
  - Manages address translation and validity
  - Coordinates read/write timing
- Timing diagrams included:
  - CPU → SRAM (150 MHz path)
  - CPU → Data Memory (via SRAM miss, 70 MHz path)

---

## 💻 RTL Implementation (Verilog)

-Verilog modules for:
  -CPU Core → cpu.v
  -SRAM Buffer → Sram.v
  -Cache Updater / SRAM-DM Control → sram_dm_control.v
  -Program Memory → program_mem.v
  -Data Memory → data_mem.v
  -FIFO Controllers → fifo_ctrl.v, Async_FIFO.v (for buffering & clock domain crossing)
  -Reset Synchronizers → rst_sync.v (fast/slow domain resets)
  -Top-level Integration → top.v
  
-LOAD/STORE Operations:
  -CPU checks SRAM buffer (Sram.v) first.
  -On miss → fetch from Data Memory (data_mem.v) through sram_dm_control.v, and update SRAM.

SRAM runs at CPU clock (150 MHz); Data Memory runs at 70 MHz.
---

## ✅ Verification

- **Single Testbench (tb_top.v) for the entire design:**
   - Validates CPU instruction execution with Program Memory.
   - Tests LOAD/STORE operations with SRAM hit/miss cases.
   - Ensures cache updater (sram_dm_control.v) works correctly.
   - Verifies FIFO controllers and async FIFOs for CDC handling.
   - Covers overall system integration in top.v.
- **Clock Domain Crossing (CDC):**
  - Synchronizers/buffers between SRAM (150 MHz) and Data Memory (70 MHz)
- **Static Timing Analysis (STA):**
  - Verified for both CPU and memory domains

---

## 📂 Project Structure
CPU-Lite/
│── docs/ # Block diagrams, timing diagrams, micro-architecture notes
│── rtl/ # RTL design sources
│ ├── top.v # Top-level integration
│ ├── cpu.v # CPU core (RISC)
│ ├── Sram.v # 1Kx32 synchronous SRAM buffer
│ ├── sram_dm_control.v # Cache updater / SRAM-DM interface logic
│ ├── program_mem.v # 4Kx32 Program memory
│ ├── data_mem.v # 16Kx32 Data memory
│ ├── fifo_ctrl.v # FIFO control logic
│ ├── Async_FIFO.v # Async FIFO for CDC
│ ├── rst_sync.v # Reset synchronizers (fast/slow)
│── tb/ # Testbenches
│ └── tb_top.v # Top-level testbench (full system verification)
│── reports/ # STA, CDC, simulation logs
│── README.md # Project description
