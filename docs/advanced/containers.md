# Containers on G2

## What are Containers?

Containers package an application together with all its dependencies — libraries, tools, configuration — into a single portable image. On G2, containers are managed by **Apptainer** (formerly Singularity), which is designed for HPC environments.

Unlike Docker, Apptainer does not require root privileges and integrates naturally with SLURM.

## Why Use Containers?

- **Reproducibility**: Same environment on any system
- **Complex dependencies**: Software that conflicts with system libraries or requires specific versions
- **Portability**: Containers from Docker Hub or other registries run without modification
- **User control**: Install anything inside the container without needing admin access

## Loading Apptainer

```bash
module load apptainer
```

## Pulling Container Images

### From Docker Hub

```bash
# Pull a Docker image and convert to Apptainer SIF format
apptainer pull docker://ubuntu:22.04
apptainer pull docker://python:3.12

# Pull with a custom name
apptainer pull myimage.sif docker://tensorflow/tensorflow:latest-gpu
```

### From Sylabs Cloud

```bash
apptainer pull library://lolcow
```

## Running Containers

### Execute a Single Command

```bash
apptainer exec ubuntu_22.04.sif echo "Hello from inside the container"

# Run Python inside the container
apptainer exec python_3.12.sif python my_script.py
```

### Interactive Shell Inside a Container

```bash
apptainer shell ubuntu_22.04.sif
Apptainer> echo $HOME
Apptainer> python3 --version
Apptainer> exit
```

### Binding Host Directories

By default, your home directory is accessible inside the container. To mount additional directories (like scratch or group):

```bash
apptainer exec --bind /groups/mygroup:/data myimage.sif python /data/script.py

# Bind scratch
apptainer exec --bind ~/scratch:/scratch myimage.sif ./my_program
```

## Running GPU Containers

Use the `--nv` flag to pass through NVIDIA GPU support:

```bash
apptainer exec --nv tensorflow_gpu.sif python train.py
```

This makes the GPU visible inside the container and loads the appropriate NVIDIA drivers.

## Using Containers in SLURM Jobs

### CPU Job with Container

```bash
#!/bin/bash
#SBATCH --job-name=container_job
#SBATCH --output=container_%j.out
#SBATCH --partition=cpu-preempt
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --mem=16G
#SBATCH --time=4:00:00

module load apptainer

apptainer exec --bind ~/scratch:/scratch \
  /groups/mygroup/containers/myapp.sif \
  python /scratch/my_script.py
```

### GPU Job with Container

```bash
#!/bin/bash
#SBATCH --job-name=gpu_container
#SBATCH --output=gpu_container_%j.out
#SBATCH --partition=gpu-preempt
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=4
#SBATCH --gres=gpu:1
#SBATCH --mem=32G
#SBATCH --time=8:00:00

module load apptainer

apptainer exec --nv \
  --bind ~/scratch:/scratch \
  /groups/mygroup/containers/pytorch_gpu.sif \
  python /scratch/train.py
```

## Building Custom Images

### Using a Definition File

Create a file named `myapp.def`:

```singularity
Bootstrap: docker
From: ubuntu:22.04

%post
    apt-get update
    apt-get install -y python3 python3-pip
    pip3 install numpy scipy pandas

%environment
    export PATH=/usr/bin:$PATH

%runscript
    python3 "$@"
```

Build the image (requires `--fakeroot` or a sandbox on systems without root):

```bash
apptainer build --fakeroot myapp.sif myapp.def
```

!!! note
    Building images from definition files may require additional permissions. Contact [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu) if you encounter issues.

### Using an Existing Docker Container as Base

For most use cases, pulling from Docker Hub is the easiest approach:

```bash
apptainer pull docker://rocker/r-ver:4.3
apptainer pull docker://ghcr.io/biocontainers/samtools:1.21--h50ea8bc_0
```

## Storing Container Images

Container `.sif` files can be large (several GB). Store them in your group directory rather than home:

```bash
mkdir -p /groups/<pi-name>/containers
apptainer pull /groups/<pi-name>/containers/myapp.sif docker://myimage
```

## Useful Apptainer Commands

```bash
# Pull an image
apptainer pull myimage.sif docker://image:tag

# Run a command
apptainer exec myimage.sif command

# Interactive shell
apptainer shell myimage.sif

# GPU support
apptainer exec --nv myimage.sif command

# Bind a directory
apptainer exec --bind /host/path:/container/path myimage.sif command

# Show image metadata
apptainer inspect myimage.sif

# List cached images
apptainer cache list
```

## Next Steps

- [Miniconda for Python environments (no containers needed) →](miniconda.md)
- [Python optimization →](python-optimization.md)

## Need Help?

- **Email**: [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)
- **Apptainer documentation**: [apptainer.org/docs](https://apptainer.org/docs/)
