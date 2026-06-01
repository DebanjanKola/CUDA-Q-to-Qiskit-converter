# CUDA-Q-to-Qiskit-converter

> A Python library that automatically translates CUDA-Q quantum programs into equivalent Qiskit code using Abstract Syntax Tree (AST) parsing — enabling seamless cross-platform quantum computing.

---

## Overview

The quantum computing landscape is fragmented. CUDA-Q (by NVIDIA) excels at hybrid quantum-classical workflows with GPU acceleration, while Qiskit (by IBM) provides direct access to real quantum hardware. Switching between them currently requires significant manual code rewriting.

This library bridges that gap. By parsing CUDA-Q source code into an Abstract Syntax Tree and systematically mapping quantum operations to their Qiskit equivalents, developers can run their CUDA-Q algorithms on IBM's quantum hardware — **without rewriting their code**.

---

## Problem Statement

- **Vendor lock-in** — Quantum developers are forced to choose one platform and stay there.
- **Simulation limits** — Classical simulation of quantum circuits has exponential memory requirements. For `n` qubits, RAM needed grows as `8 × 2ⁿ` bytes, making real hardware access increasingly critical.
- **Code reuse** — Large investments in CUDA-Q codebases deserve to be reused, not rewritten.

| Qubits | RAM Required for Simulation |
|--------|----------------------------|
| 10     | 8 KB                        |
| 20     | 8 MB                        |
| 40     | 8 GB                        |
| 60     | 8,192 PB                    |

---

## How It Works

The converter follows a multi-step pipeline:

1. **Parse** — The CUDA-Q source file is parsed into an Abstract Syntax Tree (AST), producing a structured, traversable representation of the code.
2. **Extract** — The AST is walked to identify quantum gates, qubit allocations, and measurement instructions.
3. **Map** — Each CUDA-Q gate name is translated to its Qiskit equivalent via a predefined gate mapping dictionary.
4. **Generate** — A `QuantumCircuit` object is constructed in Qiskit with all operations applied in order.
5. **Save** — The resulting circuit is serialized as a ready-to-run Qiskit Python script.

### Supported Gate Mappings

| CUDA-Q Gate | Qiskit Equivalent |
|-------------|-------------------|
| `h`         | `h`               |
| `x`         | `x`               |
| `y`         | `y`               |
| `z`         | `z`               |
| `s`         | `s`               |
| `t`         | `t`               |
| `rx`        | `rx`              |
| `ry`        | `ry`              |
| `rz`        | `rz`              |
| `cx`        | `cx`              |
| `mz`        | `measure`         |

---


### Installation

```bash
# 1. Clone the repository
git clone https://github.com/NishankaDas/cudaq-qiskit-converter.git

# 2. Navigate into the directory
cd cudaq-qiskit-converter

# 3. Install the module
pip install .
```

### Usage

```python
from cudaq_to_qiskit import convert_cudaq_source_to_qiskit
import os

# Provide the path to your CUDA-Q source file
cudaq_file_path = input("Enter the path of the CUDA-Q source file: ")

if not os.path.exists(cudaq_file_path):
    print("Error: File not found!")
    exit()

# Read and convert
with open(cudaq_file_path, "r") as file:
    cudaq_source_code = file.read()

circuit, qiskit_code = convert_cudaq_source_to_qiskit(cudaq_source_code)

# Display output
print("Qiskit Circuit:")
print(circuit)
print("\nQiskit Code:")
print(qiskit_code)

# Save to file
output_file = input("Enter output file name: ")
with open(output_file, "w") as f:
    f.write(qiskit_code)

print(f"\nQiskit Python Code saved to: {output_file}")
```


## Architecture

```
CUDA-Q Source File
        │
        ▼
   AST Parser
        │
        ▼
  AST Traversal
  (Extract Gates, Qubits, Parameters)
        │
        ▼
  Gate Mapping Dictionary
  (CUDA-Q → Qiskit names)
        │
        ▼
  Qiskit QuantumCircuit Builder
        │
        ▼
  Qiskit Python Script Output
```

---
