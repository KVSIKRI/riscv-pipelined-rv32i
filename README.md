# riscv-pipelined-rv32i
Fully pipelined RV32I RISC-V microprocessor in SystemVerilog — 13-module design with load-use hazard detection and stall handling.

# RISC-V Pipelined Microprocessor Architecture (RV32I)
A pipelined RV32I RISC-V microprocessor implemented in SystemVerilog, featuring complete instruction set coverage and hazard-aware pipeline control.

## Overview
This project implements a 3-stage pipelined RV32I core from scratch — IF, DE/EX, and MEM/WB — with dedicated load-use hazard detection and stall logic to guarantee correct execution.

## Pipeline Stages
- **Stage 1 (IF)**: `if_stage`, `imem`
- **Stage 2 (DE/EX)**: `id_stage`, register read, `alu_control`, `alu`, `branch_unit`
- **Stage 3 (MEM/WB)**: `dmem`, `wb_mux`, register write

## Architecture
13 modules, integrated in a top-level design:

| Module | File | Description |
|---|---|---|
| Instruction Fetch | `if_stage.sv` | Fetches instructions from instruction memory |
| Instruction Decode | `id_stage.sv` | Decodes instructions, reads register file |
| ALU | `alu.sv` | Arithmetic/logic unit |
| ALU Control | `alu_control.sv` | Generates ALU operation control signals |
| Register File | `regfile.sv` | 32x32-bit general purpose registers, write-first bypass |
| Instruction Memory | `imem.sv` | Stores program instructions |
| Data Memory | `dmem.sv` | Load/store data memory |
| IF/DE Pipeline Register | `if_de_reg.sv` | Pipeline register between IF and DE/EX stages |
| DE/MEM Pipeline Register | `de_mem_reg.sv` | Pipeline register between DE/EX and MEM/WB stages |
| Write-back Mux | `wb_mux.sv` | Selects data written back to register file |
| Branch Unit | `branch_unit.sv` | Resolves branch conditions and targets |
| Stall Unit | `stall_unit.sv` | Detects load-use hazards and issues a one-cycle stall pulse |
| Top-level Integration | `design.sv` | Connects all modules into the full pipeline |

## Features
- **Full RV32I instruction set coverage**: R-type, I-type, S-type, B-type, and J-type instructions
- **Load-use hazard handling**: Stall unit compares the instruction entering DE/EX next cycle against the in-flight load; issues a one-cycle stall pulse rather than a level signal, so the load is never flushed and the hazard always clears
- **No separate forwarding unit**: register file resolves same-cycle write-first bypass internally
- **Verified functionality**: Custom testbench with `program.hex` simulation covering arithmetic, logical, memory, branch, and jump operations
- Simulated and validated on Synopsys VCS / EDA Playground

## Tech Stack
- **Language**: SystemVerilog
- **Simulation**: Synopsys VCS, EDA Playground

## Repository Structure
```
riscv-pipelined-rv32i/
├── src/
│   ├── if_stage.sv
│   ├── id_stage.sv
│   ├── alu.sv
│   ├── alu_control.sv
│   ├── regfile.sv
│   ├── imem.sv
│   ├── dmem.sv
│   ├── if_de_reg.sv
│   ├── de_mem_reg.sv
│   ├── wb_mux.sv
│   ├── branch_unit.sv
│   ├── stall_unit.sv
│   └── design.sv
├── testbench/
│   └── testbench.sv
├── programs/
│   └── program.hex
└── README.md
```
## How to Simulate

### Option A: EDA Playground
1. Open [EDA Playground](https://www.edaplayground.com/)
2. Load `design.sv` and `testbench/testbench.sv` (design.sv includes all other modules via `` `include ``)
3. Load `programs/program.hex` as the instruction memory input
4. Run simulation — testbench covers arithmetic, logical, memory, branch, and jump instruction classes

### Option B: Synopsys VCS (local)
```bash
vcs -full64 -licqueue '-timescale=1ns/1ns' '+vcs+flush+all' '+warn=all' -sverilog design.sv testbench.sv
./simv +vcs+lic+wait
```
Note: only `design.sv` and `testbench.sv` are passed to VCS — `design.sv` pulls in every other module itself via `` `include ``, so compiling `src/*.sv` separately will cause duplicate-module errors.

## Status
Fully functional pipelined RV32I core with verified load-use hazard resolution.

## Author
**Kavya Sikri**
[LinkedIn](https://www.linkedin.com/in/kavya-sikri) | [GitHub](https://github.com/KVSIKRI)
