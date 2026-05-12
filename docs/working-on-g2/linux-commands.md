# Linux Commands Crash Course

## Overview

G2 runs Linux. All interaction with the cluster happens through a Unix command-line shell — typically **Bash**. This page summarizes the essential commands you need to get started.

## The Bash Shell

Bash is the program that:

- Accepts your commands and runs programs
- Manages your environment variables
- Can execute commands from a script file

When you log in to G2, you are placed in a Bash shell session on a login node.

## Essential Commands

### Working with Directories

| Command | Description | Example |
|---------|-------------|---------|
| `ls` | List directory contents | `ls -lh ~/scratch` |
| `pwd` | Print current working directory | `pwd` |
| `cd` | Change directory | `cd ~/scratch` |
| `mkdir` | Create a directory | `mkdir myproject` |
| `rmdir` | Remove an empty directory | `rmdir olddir` |

```bash
# List files with details and human-readable sizes
ls -lh

# List hidden files too
ls -la

# Change to your scratch directory
cd ~/scratch

# Go back to home
cd ~

# Go up one directory
cd ..
```

### Working with Files

| Command | Description | Example |
|---------|-------------|---------|
| `cp` | Copy a file | `cp data.txt data_backup.txt` |
| `mv` | Move or rename a file | `mv old.txt new.txt` |
| `rm` | Delete a file | `rm tempfile.txt` |
| `grep` | Search file contents for text | `grep "error" output.log` |
| `cat` | Print file contents | `cat job.sh` |
| `less` | View file page by page | `less output.log` |
| `head` | Show first N lines | `head -20 output.log` |
| `tail` | Show last N lines | `tail -50 output.log` |
| `wc` | Count lines, words, bytes | `wc -l output.log` |

```bash
# Copy a directory recursively
cp -r myproject/ myproject_backup/

# Remove a directory and all contents (careful!)
rm -rf olddir/

# Search for "error" in all .log files
grep -r "error" *.log

# Follow a file as it grows (useful for monitoring job output)
tail -f crunch_out.txt
```

### File Editor

```bash
# nano: simple, beginner-friendly interactive editor
nano myscript.py

# vi/vim: powerful but steeper learning curve
vi myscript.py
```

### Permissions

```bash
# Check file permissions
ls -l filename

# Make a script executable
chmod +x myscript.sh

# Private file (owner read/write only)
chmod 600 sensitive.key

# Private directory
chmod 700 private_dir/
```

## Remote Login and Data Transfer

| Command | Description |
|---------|-------------|
| `ssh` | Open a secure shell on a remote computer |
| `scp` | Copy files to/from a remote computer |
| `rsync` | Synchronize files between systems, resumable |

```bash
# Log in to G2
ssh netID@ganymede2.utdallas.edu

# Copy a file to G2
scp myfile.c netID@ganymede2.utdallas.edu:~/work

# Copy a file from G2
scp netID@ganymede2.utdallas.edu:~/scratch/results.dat ./

# Sync a directory (resumable, shows progress)
rsync -avzP mydir/ netID@ganymede2.utdallas.edu:~/scratch/mydir/
```

## Environment Variables

Environment variables store values that Bash and other programs use at runtime.

| Variable | Purpose |
|----------|---------|
| `PATH` | Directories Bash searches to find programs |
| `LD_LIBRARY_PATH` | Directories searched for shared libraries |
| `HOME` | Your home directory path |
| `USER` | Your username |

```bash
# Print a variable's value
echo $PATH
echo $HOME

# Set a variable for the current session
export MY_VAR=hello

# Add a directory to PATH
export PATH=$PATH:~/bin
```

## Useful Utility Commands

```bash
# Show full path to a command
which python
which gcc

# Show what shared libraries a binary needs
ldd ./myprogram

# Check disk usage
du -sh ~/scratch
du -sh ~/scratch/* | sort -h

# Check system load and uptime
uptime

# See who is logged in
who

# Show running processes
top

# Compress/decompress
gzip large_file.txt
gunzip large_file.txt.gz
tar czf archive.tar.gz directory/
tar xzf archive.tar.gz
```

## Job Control

```bash
# Run a command in the background
./myscript.sh &

# Bring background job to foreground
fg

# List background jobs
jobs

# Kill a background job (use job number from `jobs`)
kill %1
```

## Shell Tips

```bash
# Repeat last command
!!

# History search (press Ctrl+R, then type)
# Ctrl+R

# Command completion with Tab key
ls ~/scr<Tab>     # expands to ~/scratch

# Redirect output to a file
./myprogram > output.txt

# Redirect both stdout and stderr
./myprogram > output.txt 2>&1

# Pipe output to another command
cat output.log | grep "error" | wc -l
```

## Next Steps

- [Module system →](modules.md)
- [Available software →](software.md)
- [Submit your first SLURM job →](../running-programs/slurm.md)

## Need Help?

- **Email**: [circ-assist@utdallas.edu](mailto:circ-assist@utdallas.edu)
- **HPC Services**: [hpc.utdallas.edu/services](https://hpc.utdallas.edu/services)
