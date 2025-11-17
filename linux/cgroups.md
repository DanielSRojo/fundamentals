# Control Groups

Linux **cgroups** (control groups) are a kernel feature that lets you *limit*, *isolate*, and *account* for the resource usage of processes. They are one of the fundamentals building blocks behind containers, [systemd](systemd.md), and many resource management tools.

## What they do

**cgroups** allow the system to control how much CPU, memory, I/O, [PIDs](pid.md), and other resources a group of processes can use. Processes get placed into a **cgroup**, and the kernel enforces the rules.

## Capabilities

Hard and soft limits can be set:

- **CPU**: shares, quotas, periods.
- **Memory**: max memory, swap limits.
- **I/O**: block device bandwidth.
- **PIDs**: limit how many processes a group can spawn.
- (v1) **freezer**: suspend/resume processes.

## Monitoring

You can inspect how much memory, CPU, or I/O a group is using by reading its cgroup files.

## Isolation

Processes inside a **cgroup** cannot exceed the resource limits assigned to it, which is crucial for containers isolation and sandboxing.

## Control Groups v1 vs v2

### cgroups v1

- Resources are managed by separate controllers, each with its own hierarchy.
- More flexible but more complex and easier to misconfigure.

### cgroups v2

- Unified hierarchy (all controllers under one tree).
- Simpler and more consistent.
- Better memory management semantics.
- Most modern distros default to it.
- Stricter rules about delegation (who can create/manage sub-cgroups).

> [!NOTE] Docker and Kubernetes support cgroups v2.

## Where cgroups live

They are exposed through virtual filesystems:

On v1:

```shell
/sys/fs/cgroup/cpu/
/sys/fs/cgroup/memory/
/sys/fs/cgroup/blkio/
```

On v2:

```shell
/sys/fs/cgroup/
```

Controls are applied by writing to files in these directories.

> [!NOTE] These files only exist in memory.

## v2 Example

Create a new cgroup:

```bash
sudo mkdir /sys/fs/cgroup/testgroup
```

Limit memory to 200MB:

```bash
echo 200M | sudo tee /sys/fs/cgroup/testgroup/memory.max
```

Add a process to the group (PID 1234):

```bash
echo 1234 | sudo tee /sys/fs/cgroup/testgroup/cgroup.procs
```

With this configuration, the process cannot exceed 200MB memory. If it tries to allocate beyond that, the kernel will deny the allocation, and depending on configuration, it may be OOM-killed (behavior depends on `memory.oom.group`).
