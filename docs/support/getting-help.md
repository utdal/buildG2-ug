# Getting Help

## HPC Support Portal

The primary way to get help is through the HPC Services page:

1. Visit [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services)
2. Locate **Ganymede 2** (or the relevant system/topic)
3. Click the link that most closely matches your request

The services page has specific links for:

- Account requests and access issues
- Storage and quota requests
- Software installation requests
- Job and performance issues
- General HPC questions

## Email Support

If unsure which link to use, email directly:

**[circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)**

### Writing a Good Support Ticket

Include the following to get a faster, more accurate response:

1. **System**: Ganymede 2 (G2)
2. **What you were trying to do**: Brief description of your goal
3. **What happened**: The error message or unexpected behavior
4. **Job ID** (if applicable): From `squeue --me` or `sacct`
5. **Relevant commands**: The exact commands you ran
6. **Error output**: Contents of your `.err` file or terminal output

**Example of a good ticket**:

> I'm trying to run a PyTorch training job on G2 (job ID 8501). The job fails immediately with an error in the `.err` file:
>
> `RuntimeError: CUDA error: no kernel image is available for execution on the device`
>
> I loaded `cuda/12.4` and my PyTorch was installed with `conda install pytorch pytorch-cuda=12.4 -c pytorch -c nvidia`.
> The job script uses `--partition=gpu-preempt --gres=gpu:1`.

## Self-Service Resources

Before submitting a ticket, check:

- **This user guide** — search for your topic
- **Orientation slides** — available at [hpc.utdallas.edu/systems-resources/ganymede2/](https://hpc.utdallas.edu/systems-resources/ganymede2/)
- **Cluster status commands**:
  ```bash
  sinfo                     # Partition and node states
  squeue --me               # Your running/pending jobs
  sacct -j <jobid>          # Completed job details
  module avail              # Available software
  ```

## Common Issues and Quick Fixes

| Issue | Quick fix |
|-------|-----------|
| Job pending too long | Check `squeue -j <id> -l` for reason; try a different partition |
| Job fails immediately | Check the `.err` file for error messages |
| Out of memory | Increase `--mem` in your job script |
| Job timed out | Increase `--time` up to partition maximum |
| Module not found | Run `module avail` to check spelling/availability |
| Permission denied | Check file permissions with `ls -l` |
| Quota exceeded | Run `mfsgetquota -H ~` to check usage |
| Cannot connect | Verify you're on UTD network or VPN |

## Orientation and Training

New users should review the **G2 Orientation slides** for a comprehensive introduction. The latest version is always available at:

[hpc.utdallas.edu/systems-resources/ganymede2/](https://hpc.utdallas.edu/systems-resources/ganymede2/)

The CIRC team also periodically offers workshops and orientation sessions — check the HPC website for upcoming events.

## Important Links

| Resource | URL |
|----------|-----|
| HPC Services | [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services) |
| G2 Resources | [hpc.utdallas.edu/systems-resources/ganymede2/](https://hpc.utdallas.edu/systems-resources/ganymede2/) |
| Open OnDemand | [g2-ood.circ.utdallas.edu](https://g2-ood.circ.utdallas.edu/) |
| Policies & Guidelines | [hpc.utdallas.edu/policies-and-guidelines/](https://hpc.utdallas.edu/policies-and-guidelines/) |
| VPN Setup | [atlas.utdallas.edu](https://atlas.utdallas.edu/TDClient/30/Portal/Requests/ServiceDet?ID=167) |
| Support Email | [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu) |
