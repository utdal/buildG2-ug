# Monitoring Jobs and Cluster State

## Overview

This page covers SLURM commands for monitoring running jobs, inspecting past jobs, and understanding the overall state of the cluster.

## Monitoring Your Jobs

### squeue — View Queued and Running Jobs

```bash
# Your jobs only
squeue --me

# All jobs in a partition
squeue -p cpu-preempt

# Detailed output with reason for pending
squeue -u $USER -l
```

Example output:
```
[netID@g2-l-01 ~]$ squeue --me
       JOBID  PARTITION     NAME    USER ST       TIME  NODES NODELIST(REASON)
        8378      rotea   crunch netID    R       0:09      1 c-09-06
        8379 cpu-preemp  analyze netID   PD       0:00      1 (Resources)
```

**State codes**:

| Code | Meaning |
|------|---------|
| `R` | Running |
| `PD` | Pending (waiting for resources) |
| `CG` | Completing |
| `PR` | Preempted |
| `F` | Failed |

### scontrol show job — Detailed Job Info

```bash
scontrol show job 8378
```

Shows the full job record including requested resources, allocated nodes, start time, and reason for pending.

### Watching Job Output

While a job is running, you can follow the output file in real time:

```bash
tail -f myjob_8378.out
```

## Inspecting Completed Jobs

### sacct — Job Accounting

```bash
# Summary for a specific job
sacct -j 8378

# Detailed format
sacct -j 8378 --format=JobID,JobName,State,Start,End,Elapsed,MaxRSS,CPUTime

# All your recent jobs
sacct -u $USER

# Jobs since a specific date
sacct -u $USER --starttime 2026-01-01
```

Useful `--format` fields:

| Field | Description |
|-------|-------------|
| `JobID` | Job identifier |
| `JobName` | Job name |
| `State` | Final state (COMPLETED, FAILED, TIMEOUT, CANCELLED) |
| `Elapsed` | Wall time used |
| `MaxRSS` | Peak memory used |
| `CPUTime` | Total CPU time charged |
| `Start` / `End` | Start and end timestamps |

### Checking Resource Efficiency

After a job finishes, check whether your resource requests were accurate:

```bash
sacct -j 8378 --format=JobID,AllocCPUS,MaxRSS,Elapsed,State
```

If `MaxRSS` is much lower than your `--mem` request, reduce it next time to improve scheduling. If a job was `TIMEOUT`, increase `--time`.

## Cluster State

### sinfo — Partition and Node State

```bash
# Overview of all partitions
sinfo

# Filter by partition
sinfo -p cpu-preempt

# Custom format showing node count and state
sinfo -o "%P %D %C %l"
```

Node states in `sinfo`:

| State | Meaning |
|-------|---------|
| `idle` | Available for new jobs |
| `alloc` | Fully allocated |
| `mix` | Partially allocated |
| `drain` | No new jobs; existing jobs finishing |
| `down` | Offline |

### scontrol show partition — Partition Details

```bash
scontrol show partition cpu-preempt
scontrol show partition g1mig
```

This shows total CPUs, memory, GPU resources, time limits, and the list of nodes in the partition.

### scontrol show node — Node Details

```bash
scontrol show node c-08-01
```

Shows CPU count, allocated memory, GPU resources, current state, and partition membership.

## Checking GPU Availability

```bash
# All GPU nodes and their state
sinfo -p gpu-preempt

# See GPU resources in a partition
scontrol show partition gpu-preempt | grep "TRES"
```

## Managing Jobs

### Cancel Jobs

```bash
# Cancel a single job
scancel 8378

# Cancel all your jobs
scancel -u $USER

# Cancel all pending jobs only
scancel -u $USER -t PENDING

# Cancel jobs in a specific partition
scancel -u $USER -p cpu-preempt
```

### Hold and Release

```bash
# Prevent a pending job from starting
scontrol hold 8378

# Allow a held job to start
scontrol release 8378
```

### Update a Pending Job

```bash
# Extend time limit (if not yet running and within partition max)
scontrol update job=8378 TimeLimit=10:00:00

# Change partition
scontrol update job=8378 Partition=cpu-preempt
```

## Diagnosing Common Issues

### Why is My Job Still Pending?

```bash
squeue -j 8378 -l
```

Look at the `REASON` column:

| Reason | Meaning | Action |
|--------|---------|--------|
| `Resources` | Not enough idle nodes | Wait; reduce resource request |
| `Priority` | Higher-priority jobs ahead | Wait |
| `QOSMaxCpuPerUserLimit` | Over per-user CPU limit | Reduce `--ntasks-per-node` or `--nodes` |
| `AssocGrpMemLimit` | Over memory limit | Reduce `--mem` |
| `ReqNodeNotAvail` | Requested node unavailable | Remove node constraint or pick different partition |

### Why Did My Job Fail?

```bash
# Check the error file
cat myjob_8378.err

# Check accounting record
sacct -j 8378 --format=State,ExitCode,DerivedExitCode
```

Common failure patterns:

- Exit code `1`: Program error — check stderr for messages
- Exit code `137`: Killed by OOM (out of memory) — increase `--mem`
- State `TIMEOUT`: Wall time exceeded — increase `--time`
- State `CANCELLED`: Manually cancelled or node failure

### Preempted Jobs

Jobs in `cpu-preempt` or `gpu-preempt` may be preempted when a condo owner submits jobs to their partition. To automatically restart:

```bash
#SBATCH --requeue
```

Check if a job was preempted:
```bash
sacct -j 8378 --format=JobID,State,ExitCode
# State will show "PREEMPTED"
```

## Quick Reference

```bash
# Monitor
squeue --me                         # Your active jobs
squeue -p cpu-preempt               # Jobs in a partition
scontrol show job 8378              # Full job details
tail -f myjob_8378.out              # Live output

# History
sacct -j 8378                       # Job summary
sacct -u $USER --starttime 2026-01-01  # Recent history

# Cluster
sinfo                               # Partition overview
scontrol show partition g1mig       # Partition details
scontrol show node c-08-01          # Node details

# Manage
scancel 8378                        # Cancel a job
scancel -u $USER                    # Cancel all your jobs
scontrol hold 8378                  # Hold a pending job
scontrol release 8378               # Release a held job
```

## Next Steps

- [Job submission →](slurm.md)
- [Running common programs →](common-programs.md)

## Need Help?

- **Email**: [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)
- **HPC Services**: [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services)
