# CVA6 RISC-V Trace Profiler and Eclipse Integration

A project focused on integrating E-Trace and System Trace decoding with profiling and reporting inside the Eclipse IDE for the CVA6 RISC-V processor.

## Project Title

CVA6 RISC-V E-Trace & System Trace Integration & Profiling in Eclipse IDE

## Author

Iqra Jawad Ahmed

---

## 1. Problem Statement

Integrate both the E-Trace and System Trace decoders directly into the Eclipse IDE. The goal is to allow users to acquire encoded trace data, decode it in real time, and generate profiling reports such as function execution time and cache misses by correlating the decoded trace with the corresponding firmware ELF.

This project bridges hardware tracing, software profiling, and IDE-based visualization in a single workflow.

---

## 2. Learning Outcomes

- Advanced IDE plugin development and Eclipse integration techniques
- Deep understanding of software profiling using hardware trace data
- Practical experience in data visualization and report generation
- Hands-on exposure to CVA6, Spike, Verilator, and trace analysis pipelines

---

## 3. System Architecture Overview

The system is organized into three major layers:

### Layer 1: Simulation

- CVA6 Verilator model
- Spike ISA simulator
- Produces firmware and trace data from the compiled ELF

### Layer 2: Trace Processing

- E-Trace pipeline
- System Trace pipeline
- Converts raw hardware traces into readable and decodable instruction-level data

### Layer 3: Profiling and Reporting

- Python-based analysis scripts
- Unified reporting of function execution metrics and cache miss behavior
- Eclipse plugin for viewing results inside the IDE

---

## 4. Architecture Layers

| Layer | Description |
| --- | --- |
| Layer 1: Simulation | CVA6 + Spike + Verilator compile and execute firmware ELF |
| Layer 2A: E-Trace Pipeline | post-iss tool, encoder_model.py, decoder_model.py |
| Layer 2B: System Trace Pipeline | Produces system_trace.txt with cycle, PC, mnemonic, event, function, register delta, and memory access info |
| Layer 3A: Profiling Scripts | func_exec_time.py, correlate_misses.py, finalize_profiling.py |
| Layer 3B: Eclipse Plugin | Reads CSV/TXT outputs and presents them as IDE views |

---

## 5. Data Flow

1. `fibonacci.c` is compiled into `simulation.elf`
2. ELF is executed using Spike to produce `spike_raw.log` and `spike_pc_trace.txt`
3. Verilator simulation generates `trace_hart_0.dasm` and `trace_rvfi_hart_00.dasm`
4. E-Trace encoding/decoding pipeline reconstructs PC trace data
5. System trace merge creates `system_trace.txt`
6. Profiling scripts compute execution metrics and cache miss correlations
7. Final report is generated as `unified_profiling_report.csv` and related summaries

---

## 6. System Setup and Installation

This project requires a full CVA6 trace environment and toolchain.

### 6.1 Clone CVA6 Repository

```bash
git clone https://github.com/openhwgroup/cva6.git
cd cva6
git submodule update --init --recursive
```

### 6.2 Install OS Dependencies

```bash
sudo apt-get install autoconf automake autotools-dev curl git \
libmpc-dev libmpfr-dev libgmp-dev gawk build-essential \
bison flex texinfo gperf libtool bc zlib1g-dev
sudo apt install gcc git autoconf automake texinfo libtool make pkg-config -y
```

### 6.3 Install RISC-V GNU Toolchain

```bash
git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
cd riscv-gnu-toolchain
./configure --prefix=/opt/riscv
make -j$(nproc)
```

Required binaries include:

- `riscv64-unknown-elf-gcc`
- `riscv64-unknown-elf-nm`
- `riscv64-unknown-elf-addr2line`
- `riscv64-unknown-elf-objdump`

### 6.4 Python Environment

```bash
sudo apt install python3-full
python3 -m venv ~/ENV_NAME
source ~/ENV_NAME/bin/activate
```

### 6.5 Environment Variables

```bash
export RISCV=/opt/riscv
export PATH=$RISCV/bin:$PATH
```

### 6.6 Install Spike and Verilator

```bash
bash verif/regress/install-spike.sh
bash verif/regress/install-verilator.sh
```

### 6.7 Verify Toolchain

```bash
cd cva6/tools
ls
```

You should see the installed Spike and Verilator tool directories.

---

## 7. Master Pipeline

The project uses a master pipeline script to automate the full process from firmware compilation to profiling and Eclipse launch.

### Script

```bash
bash run_trace_pipeline1.sh
```

### Pipeline Flow

1. Pre-flight validation
2. Source CVA6 environment
3. Compile C firmware to ELF
4. Run Spike ISA simulation
5. Run Verilator simulation
6. Generate E-Trace input
7. Encode and decode trace data
8. Create `system_trace.txt`
9. Run `func_exec_time.py`
10. Correlate cache misses with functions
11. Generate final unified report
12. Write `pipeline_done.trigger`
13. Launch Eclipse with results loaded

---

## 8. Key Scripts

### `gen_system_trace2.py`

Merges the Verilator `.dasm` traces into a single `system_trace.txt` file with:

- Cycle
- PC
- Mnemonic
- Event
- Function
- Register delta
- Memory access information

### `func_exec_time.py`

Computes per-function metrics such as:

- Total cycles
- Percentage of program runtime
- CPI
- Instruction count
- Calls
- Average cycles per call
- Memory intensity

### `correlate_misses.py`

Maps RTL cache misses to source functions by correlating I-cache and D-cache miss logs with the ELF symbol table.

### `finalize_profiling.py`

Creates the unified profiling report by combining timing data, source locations, and cache data into a single CSV and summary output.

---

## 9. Output Files

The pipeline produces a set of artifacts including:

- `simulation.elf`
- `spike_raw.log`
- `spike_pc_trace.txt`
- `simulation.log`
- `system_trace.txt`
- `function_exec_time.csv`
- `function_exec_time.txt`
- `rtl_profiling_report.csv`
- `unified_profiling_report.csv`
- `unified_profiling_summary.txt`

---

## 10. Eclipse Integration

The Eclipse plugin provides a dedicated view called `TraceDecoderView`.

It contains four tabs:

- Unified Profiling
- System Trace
- E-Trace
- Cache Misses

The plugin monitors a trigger file named `pipeline_done.trigger` and automatically reloads data when a new pipeline run completes.

This enables an end-to-end workflow where the user can:

- compile the firmware,
- collect trace data,
- decode and profile it,
- and immediately inspect the results inside Eclipse.

---

## 11. Project Goal

The project demonstrates how hardware trace data can be transformed into actionable profiling insights and displayed directly in a professional software development environment.

It combines:

- CPU simulation
- trace decoding
- profiling analysis
- cache correlation
- IDE integration

into one complete workflow for analyzing RISC-V execution behavior.

---

## 13. Summary

This project provides a complete trace analysis and profiling workflow for the CVA6 processor with:

- RISC-V firmware compilation
- Spike + Verilator simulation
- E-Trace and System Trace decoding
- Python-based profiling scripts
- Unified reporting
- Eclipse IDE integration

It is a strong example of hardware-software co-analysis and developer tooling for modern processor debugging and optimization.


