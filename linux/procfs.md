# Procfs

The **proc filesystem** (`/proc`) is a virtual filesystem provided by the Linux kernel that exposes internal kernel data structures as files and directories. It does not store data on disk; instead, it acts as a real-time interface to the kernel, allowing users and processes to inspect and interact with the system.

`/proc` is essential for understanding how Linux exposes system information and process state.

## What it is

`/proc` is a **pseudo-filesystem** that:

- reflects the current state of the kernel
- provides information about **running processes**
- exposes **system configuration knobs** (via `/proc/sys`)
- allows userspace tools to read kernel data in a uniform, file-based way

Almost everything in `/proc` is generated on demand. Reading a file triggers kernel code that formats and returns the data.

## Why it exists

`/proc` exists to give userspace:

- a standardized way to read kernel data
- a way to tune kernel parameters without rebooting
- a simple interface for process information
- a mechanism compatible with Unix philosophy ("everything is a file")

Without `/proc`, tools like `ps`, `top`, `free`, `uptime`, or `ip` would be impossible or far more complex.

## Process Directories

For each running process, there is a directory named after its PID:

```bash
/proc/<pid>
```

This directory exposes information about that process:

| **File** | **Meaning** |
| --- | --- |
| `cmdline` | Command used to start the process |
| `cwd` | Symlink to current working directory |
| `environ` | Environment variables |
| `exe` | Symlink to the executing binary |
| `fd/` | Open file descriptors |
| `maps` | Memory mappings |
| `stat` | Process statistics (used by `ps`) |
| `status` | Human-friendly process metadata |

Example:

```bash
cat /proc/self/cmdline
```

Shows the command line of the current process (`self` refers to command's own PID).

## Kernel information and system data

`/proc` also exposes system-wide kernel information:

| **Path** | **What it shows** |
| --- | --- |
| `/proc/cpuinfo` | CPU model, cores, flags |
| `/proc/meminfo` | RAM usage (used by `free`) |
| `/proc/uptime` | System uptime and idle time |
| `/proc/loadavg` | Load averages |
| `/proc/version` | Kernel version and build info |
| `/proc/filesystems` | Supported filesystem types |

These files are all read-only and reflect live kernel state.

## Kernel tuning: /proc/sys

Under `/proc/sys` lives **sysctl**, the kernel runtime configuration interface:

```shell
/proc/sys/net/ipv4/ip_forward
/proc/sys/kernel/pid_max
/proc/sys/vm/swappiness
```

Values can be read or modified:

```bash
cat /proc/sys/vm/swappiness

echo 10 | sudo tee /proc/sys/vm/swappniess
```

The last command works like:

```bash
sysctl -w vm.swappiness=10
```

This allows many kernel parameters to be changed at runtime.

## Procfs and Containers

When using namespaces -especially PID namespaces- `/proc` becomes namespaced as well:

- Inside a PID namespace, `/proc` shows only the processes inside the namespace.
- Tools like `ps` or `top` use `/proc` to list processes, so the illusion is complete.

This is why containers feel like separate systems: their `/proc` is a filtered view of the host's `/proc`.

## Why procfs is important

`proc` is foundational for:

- process inspection
- system monitoring
- debugging
- container isolation
- kernel configuration
- tool interoperability (everything from `ps` to Kubernetes relies on it)

It is one of the mos important interfaces between kernel space and user space.
