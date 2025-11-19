# PID

A **PID** (Process ID) is a unique numerical identifier assigned by the Linux kernel to every running process. PIDs allow the kernel and userspace tools to refer to, control, and manage processes.

The very first userspace process launched during boot is **init**, which receives **PID 1** and becomes the acestor of all other processes. PID 1 also has special behavior: it reaps orphaned processes.

## PID Assignment

- PIDs are assigned sequentially and reused once a process exits.
- The maximum PID value is controlled by `/proc/sys/kernel/pid_max`.
- The default limit is often 32,768 on 32-bit systems and much higher on modern 64-bit systems (commonly around 4 million), though this depends on kernel configuration rather than architecture alone.

Each process also has **PPID** (Parent Process ID), forming a parent-child hierarchy that the kernel uses to manage lifecycle and signal propagation.

## Process States Related to PIDs

- **Orphan processes**: when a parent exits; they are adopted by PID 1.
- **Zombie processes**: terminated processes that are waiting for their parent to read their exit status; they still occupy a PID.

## How PIDs Are Used

PIDs are central to process control:

- `kill <PID>` sends different kind of signals to a process.
- `pgrep`, `pidof`, and `ps` let you find processes by attributes.
- `.pid` files (commonly in `/run` or `/var/run`) store a program's PID so that other tools know how to interact with it.

Monitoring tools like `top`, `htop`, and `ps` rely on PIDs to display per-process resource usage.

## PIDs and Namespaces

In PID namespaces, PIDs are **not globally unique**. A process can have:

- one PID in its namespace
- a different PID from the host's perspective
- a different PID in each ancestor namespace

This is why processes inside containers see different PID tree than the host.
