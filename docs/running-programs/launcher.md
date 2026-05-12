# High-Throughput Computing with Launcher

## What is Launcher?

**Launcher** is a tool designed for *high-throughput computing* (HTC) workloads — situations where you have many independent tasks that do not communicate with each other. Instead of submitting hundreds of individual SLURM jobs, Launcher runs all tasks within a single job allocation, distributing them across your requested cores automatically.

Common use cases:

- Parameter sweeps (same program, different inputs)
- Processing many independent files
- Running many short simulations
- Any embarrassingly parallel workload

## How Launcher Works

You provide Launcher with:

1. A **task list file** — one command per line
2. A **SLURM job script** — specifies resources and calls Launcher

Launcher distributes the tasks from the task list across all allocated cores, starting new tasks as previous ones finish.

## Setting Up a Launcher Job

### Step 1: Create a Task List

Create a file named `tasklist.txt` with one command per line:

```
python myscript.py input_1.txt output_1.txt
python myscript.py input_2.txt output_2.txt
python myscript.py input_3.txt output_3.txt
...
python myscript.py input_500.txt output_500.txt
```

Each line is a complete shell command that runs independently.

### Step 2: Create the Job Script

```bash
#!/bin/bash
#SBATCH --job-name=py_launcher
#SBATCH --output=py_launcher_%j.out
#SBATCH --error=py_launcher_%j.err
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=64
#SBATCH --partition=cpu-preempt
#SBATCH --time=00:30:00

# Load Launcher and any software your tasks need
module purge
module load launcher
module load miniconda
conda activate /groups/mygroup/envs/myenv

# Point Launcher to your working directory and task list
export LAUNCHER_WORKDIR=$PWD
export LAUNCHER_JOB_FILE=$PWD/tasklist.txt
export LAUNCHER_PPN=64       # tasks per node (match --ntasks-per-node)
export LAUNCHER_NHOSTS=2     # number of nodes (match --nodes)

echo "Starting high-throughput tasks..."
$LAUNCHER_DIR/paramrun
echo "All tasks completed."
```

### Step 3: Submit

```bash
sbatch job.sh
```

## Example: Processing Many Files

Suppose you have 100 input files and want to process each independently:

**Generate the task list**:
```bash
for i in $(seq 1 100); do
    echo "python process.py input_${i}.dat output_${i}.dat" >> tasklist.txt
done
```

**Job script using 1 node with 16 cores**:
```bash
#!/bin/bash
#SBATCH --job-name=file_processing
#SBATCH --output=proc_%j.out
#SBATCH --partition=cpu-preempt
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=16
#SBATCH --time=2:00:00

module purge
module load launcher
module load miniconda
conda activate /groups/mygroup/envs/analysis

export LAUNCHER_WORKDIR=$PWD
export LAUNCHER_JOB_FILE=$PWD/tasklist.txt
export LAUNCHER_PPN=16
export LAUNCHER_NHOSTS=1

$LAUNCHER_DIR/paramrun
```

Launcher assigns the 100 tasks to the 16 cores, running 16 at a time. As each core finishes a task, it picks up the next one from the list.

## Example: Parameter Sweep

Run the same simulation with different parameters:

```bash
# Generate task list
for temp in 100 200 300 400 500; do
    for pressure in 1 5 10 50; do
        echo "./simulate --temp $temp --pressure $pressure --output results_T${temp}_P${pressure}.dat" >> tasklist.txt
    done
done
```

This creates 20 tasks. On 4 cores, all 20 complete in roughly 5 serial simulation times.

## Launcher vs SLURM Array Jobs

Both handle embarrassingly parallel workloads:

| Feature | Launcher | SLURM Array Jobs |
|---------|----------|-----------------|
| Setup | Task list file | `--array` directive |
| Scheduling | All tasks in one job | Each task is a separate job |
| Overhead | Low (one job) | Higher (many jobs; SLURM limits) |
| Task length | Mixed lengths OK | Uniform tasks simpler |
| Output | Combined by default | Separate file per task |

Use **Launcher** when tasks have varying runtimes or you have many hundreds of tasks. Use **array jobs** when tasks are uniform and you need per-task output files.

## SLURM Array Job Example (Alternative)

```bash
#!/bin/bash
#SBATCH --job-name=array_run
#SBATCH --output=array_%A_%a.out
#SBATCH --partition=cpu-preempt
#SBATCH --array=1-100
#SBATCH --ntasks-per-node=1
#SBATCH --mem=2G
#SBATCH --time=0:30:00

python process.py input_${SLURM_ARRAY_TASK_ID}.dat output_${SLURM_ARRAY_TASK_ID}.dat
```

## Tips and Best Practices

**Match `LAUNCHER_PPN` to `--ntasks-per-node`**: If these disagree, Launcher may under-use your allocation.

**Keep output organized**: Since all tasks share one job, redirect each task's output to its own file:

```
python myscript.py input_1.txt > logs/output_1.log 2>&1
python myscript.py input_2.txt > logs/output_2.log 2>&1
```

**Test with a small task list first**:

```bash
head -5 tasklist.txt > tasklist_test.txt
# Run a short test job with tasklist_test.txt
```

**Use Scratch for task I/O**: If your tasks do heavy file I/O, work in `~/scratch` — see [Scratch Space](../getting-started/scratch-space.md).

## Next Steps

- [Parallelism models overview →](parallelism.md)
- [Python optimization →](../advanced/python-optimization.md)
- [SLURM job submission →](slurm.md)

## Need Help?

- **Email**: [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)
- **HPC Services**: [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services)
