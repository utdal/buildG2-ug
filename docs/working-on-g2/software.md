# Available Software and Compilers

## Overview

G2 provides a broad range of software through the module system. Use `module avail` to see the full, up-to-date list. This page summarizes the major categories.

## Compilers

| Module | Version | Language |
|--------|---------|---------|
| `gnu5` | GCC 5 | C, C++, Fortran |
| `gnu8` | GCC 8 | C, C++, Fortran |
| `gnu9` | GCC 9 | C, C++, Fortran |
| `gnu11` | GCC 11 | C, C++, Fortran |
| `gnu12` | GCC 12 (**default**) | C, C++, Fortran |
| `gnu13` | GCC 13 | C, C++, Fortran |
| `gnu14` | GCC 14 | C, C++, Fortran |
| `intel/2025.0` | Intel oneAPI 2025 | C, C++, Fortran |
| `aocc` | AMD AOCC 5 | C, C++, Fortran |

```bash
# GNU 12 is loaded by default
gcc --version

# Switch to GNU 14
module swap gnu12 gnu14
gcc --version
```

## GPU Compilers and Toolkits

| Module | Notes |
|--------|-------|
| `cuda/11.7` | NVIDIA CUDA 11.7 |
| `cuda/12.4` | NVIDIA CUDA 12.4 |
| `cuda/12.6` | NVIDIA CUDA 12.6 |
| `nvhpc/24.11` | NVIDIA HPC SDK (OpenACC, OpenMP offload) |

```bash
# Load CUDA (unload conflicting GNU compiler first)
module unload gnu12
module load cuda/12.4
nvcc --version
```

## MPI and Parallel Libraries

| Module | Notes |
|--------|-------|
| `openmpi4/4.1.6` | OpenMPI 4 (**default**) |
| `openmpi5` | OpenMPI 5 |
| `mpich` | MPICH |
| `prun/2.2` | Process runner (loaded by default) |
| `ucx/1.15.0` | Communication framework (loaded by default) |

## Programming Languages

| Module | Description |
|--------|-------------|
| `python` | Python (use `miniconda` for environment management) |
| `miniconda` | Conda environment manager |
| `R` | R statistical computing |
| `julia` | Julia |
| `java/11` | Java 11 |

## Scientific and Commercial Applications

### Molecular Dynamics and Chemistry

| Module | Description |
|--------|-------------|
| `gromacs` | GROMACS molecular dynamics |
| `amber` | AMBER molecular dynamics |
| `namd` | NAMD molecular dynamics |
| `gaussian/16` | Gaussian quantum chemistry |
| `orca` | ORCA quantum chemistry |
| `qchem` | Q-Chem quantum chemistry |
| `vasp` | VASP materials modeling |

### Engineering and Simulation

| Module | Description |
|--------|-------------|
| `ansys2025R1/fluent` | Ansys Fluent (CFD) |
| `ansys2025R1/AnsysEM` | Ansys EM |
| `ansys2025R1/autodyn` | Ansys Autodyn |
| `comsol` | COMSOL Multiphysics |
| `openfoam` | OpenFOAM (CFD) |

### Mathematics and Statistics

| Module | Description |
|--------|-------------|
| `matlab/r2024b` | MATLAB |
| `R/4.5.0` | R with statistical packages |
| `stata/19.5` | Stata |
| `gurobi` | Gurobi optimization solver |
| `cplex` | IBM CPLEX optimizer |

### AI and Machine Learning

| Module | Description |
|--------|-------------|
| `ollama` | Local LLM inference (run AI models on GPU) |
| `cuda/12.4` | Required for GPU-accelerated deep learning |
| `miniconda` | Manage PyTorch, TensorFlow, etc. via Conda |

## Performance and Profiling Tools

| Module | Description |
|--------|-------------|
| `amduprof` | AMD uProf CPU/GPU profiler |
| `valgrind` | Memory error detector and profiler |

## Infrastructure and Workflow Tools

| Module | Description |
|--------|-------------|
| `launcher` | TACC Launcher for high-throughput computing |
| `apptainer` | Container runtime (formerly Singularity) |
| `spack` | Spack package manager for user software |
| `cmake` | CMake build system |
| `EasyBuild` | HPC software build framework |

## Checking What's Available

```bash
# Full list
module avail

# Search for a specific package
module avail python
module avail gaussian
module avail cuda

# Case-insensitive search
module avail 2>&1 | grep -i tensorflow

# Load and verify
module load gnu14
gcc --version
```

## Software Not in the Module System

Some software must be installed locally:

- **Python packages**: Use `conda install` or `pip install` inside a Conda environment (see [Miniconda Guide](../advanced/miniconda.md))
- **R packages**: Use `install.packages()` in an R session
- **Group-specific software**: Install to `/groups/<pi-name>/` so group members can share it
- **Containers**: Complex software stacks can be run via Apptainer — see [Containers](../advanced/containers.md)

To request a new system-wide installation, open a ticket at [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services).

## Next Steps

- [Module commands reference →](modules.md)
- [Running scientific programs →](../running-programs/common-programs.md)
- [Miniconda for Python environments →](../advanced/miniconda.md)

## Need Help?

- **Software requests**: [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)
- **HPC Services**: [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services)
