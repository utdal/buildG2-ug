# Module System

## What is the Module System?

G2 has many software packages installed, each potentially in multiple versions. If all of them were active simultaneously, their `PATH` and library settings would conflict. The **module system** solves this by letting you selectively load and unload software environments.

When you load a module, it sets the `PATH`, `LD_LIBRARY_PATH`, and other environment variables so that the corresponding software is available. Unloading a module reverses those changes.

## Default Modules on G2

When you first log in, the following modules are loaded automatically:

```bash
$ module list
Currently Loaded Modules:
  1) autotools    3) gnu12/12.4.0   5) ucx/1.15.0         7) openmpi4/4.1.6
  2) prun/2.2    4) hwloc/2.7.2    6) libfabric/1.19.0   8) ohpc
```

This means **GNU 12** (`gcc`, `g++`, `gfortran`) and **OpenMPI 4** (`mpicc`, `mpif90`) are available immediately after login without any extra `module load` commands. Other compiler or MPI versions (e.g., `gnu14`, `openmpi5`) must be loaded explicitly.

## Basic Module Commands

### List Available Modules

```bash
# Show all available modules
module avail

# Search for a specific package
module avail python
module avail cuda
module avail gnu
```

### Load a Module

```bash
# Load the default version
module load python

# Load a specific version
module load cuda/12.4
module load gnu14/14.2.0

# Load multiple modules at once
module load gnu14 openmpi4
```

### List Currently Loaded Modules

```bash
module list
```

### Unload a Module

```bash
# Unload a specific module
module unload python

# Unload all modules (start fresh)
module purge
```

### Get Module Details

```bash
# Show what a module changes (PATH, variables, etc.)
module show cuda/12.4

# Display help text for a module
module help python
```

### Switch Module Versions

```bash
# Swap one version for another
module swap gnu12 gnu14

# Or unload and reload
module unload gnu12
module load gnu14
```

## Common Module Patterns

### In Job Scripts

Load modules explicitly in your batch script. This ensures reproducibility and avoids inheriting settings from your interactive session.

```bash
#!/bin/bash
#SBATCH --job-name=my_job
#SBATCH --partition=cpu-preempt
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --mem=16G
#SBATCH --time=2:00:00
#SBATCH --output=output_%j.txt

# Start clean
module purge

# Load exactly what you need
module load gnu14
module load openmpi4/4.1.6

# Verify
module list

# Run your program
mpirun -np 8 ./my_mpi_program
```

### Loading CUDA for GPU Jobs

CUDA requires unloading the default GNU compiler first (they conflict):

```bash
module unload gnu12
module load cuda/12.4
nvcc -o mykernel mykernel.cu
```

## Software Available on G2

### Compilers and Toolkits

```bash
module load gnu12         # GCC 12 (default)
module load gnu14         # GCC 14
module load intel/2025.0  # Intel compilers
module load cuda/11.7     # NVIDIA CUDA 11.7
module load cuda/12.4     # NVIDIA CUDA 12.4
module load cuda/12.6     # NVIDIA CUDA 12.6
```

### MPI Implementations

```bash
module load openmpi4/4.1.6    # OpenMPI 4 (default)
module load mpich             # MPICH
```

### Programming Languages and Environments

```bash
module load python        # Python
module load R             # R statistical computing
module load julia         # Julia
module load miniconda     # Conda environment manager
```

### Scientific and Commercial Software

```bash
module load matlab        # MATLAB
module load gaussian/16   # Gaussian 16
module load ansys2025R1/fluent  # Ansys Fluent
module load gromacs       # GROMACS (molecular dynamics)
module load amber         # AMBER (molecular dynamics)
module load namd          # NAMD (molecular dynamics)
module load vasp          # VASP (materials modeling)
module load orca          # ORCA (quantum chemistry)
module load qchem         # Q-Chem (quantum chemistry)
module load stata         # Stata (statistics)
module load ollama        # Ollama (local LLM inference)
```

### Performance and Debugging Tools

```bash
module load amduprof      # AMD uProf profiler
module load valgrind      # Valgrind memory checker
module load launcher      # TACC Launcher (high-throughput)
module load apptainer     # Container runtime
module load spack         # Spack package manager
```

Use `module avail` for the full, current list.

## Module Hierarchy

Some modules unlock additional software. Load the compiler first, then MPI, then MPI-dependent libraries:

```
  gnu14  →  openmpi4 / mpich  →  hdf5, netcdf, petsc, ...
```

```bash
module load gnu14
module load openmpi4
module avail hdf5     # now shows gnu14-openmpi4 variant of hdf5
module load hdf5
```

Loading in the wrong order can result in "module not found" or subtle runtime errors.

## Best Practices

### 1. Always specify versions in job scripts

```bash
# Avoid (may change if defaults are updated)
module load python

# Prefer (explicit and reproducible)
module load miniconda/24.11.1
```

### 2. Start with a clean environment in batch jobs

```bash
module purge
module load gnu14
module load openmpi4
```

### 3. Don't load modules in `~/.bashrc`

Loading modules in `~/.bashrc` causes them to be inherited by every job, which can cause conflicts. Load modules in job scripts instead.

### 4. Test your module combination interactively before submitting

```bash
module purge
module load gnu14
module load openmpi4
mpicc --version   # verify correct version
```

## Module Cheat Sheet

```bash
module avail               # List all available modules
module avail python        # Search for Python modules
module load cuda/12.4      # Load specific version
module list                # Show loaded modules
module unload cuda         # Unload a module
module purge               # Unload all modules
module show cuda/12.4      # Show what module changes
module swap gnu12 gnu14    # Replace one version with another
```

## Requesting New Software

If software you need is not available:

1. Check: `module avail | grep -i software_name`
2. Try installing locally in your home or group directory
3. Open a support ticket at [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services) to request a system-wide installation
4. Use containers (Apptainer) for complex environments — see [Containers](../advanced/containers.md)

## Next Steps

- [Available software details →](software.md)
- [Submit jobs →](../running-programs/slurm.md)
- [Set up Python environments with Miniconda →](../advanced/miniconda.md)

## Need Help?

- **Module questions**: [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)
- **Software requests**: Open a ticket at the HPC Services page
