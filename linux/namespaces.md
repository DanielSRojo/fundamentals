# Namespaces

Linux **namespaces** are a kernel feature that provides isolation by giving processes their own logical view of a system or resource.

They are one of the two fundamental primitives behind containers (the other being [cgroups](cgroups.md)). If **cgroups** limit how much a process can use, namespaces limit what a process can see.

## What they are

A **namespace** wraps a global system resource in an isolated layer, so that processes inside that namespace see their own instance of the resource, while processes outside see the normal global one. This creates an illusion of a "separate system" for those processes.

A namespace is simply a data structure in the kernel, and each process has references to the namespace it belongs to:

- Every process holds a pointer to the namespaces it is part of.
- Different processes can be in different namespaces.
- Processes inherit namespaces from their parents.

> [!NOTE]  
> A "container" is just a group of processes whose namespace pointers all point to the same sets of namespaces.

Namespaces are the reason containers can:

- have their own filesystem
- have their own network stack
- run `PID 1` inside them
- have different hostnames
- run as root inside but stay unprivileged outside

> [!NOTE]
> Not all namespace types create a full separated system. Some isolate only specific resources (UTS, TIME).

## Types of Namespaces

Linux currently has eight main namespace types:

| **Namespace** | **Isolates** | **Illusion of...** |
| - | - | - |
| **PID** | Process IDs | Your own process tree (own init, own PID 1) |
| **UTS** | Hostname & domain | Your own hostname |
| **NET** | Network stack | Your own interfaces, routes, firewall, ports |
| **MNT** | Mount points | Your own filesystem layout |
| **IPC** | System V IPC, POSIX message queues | Your own shared memory segments/semaphores |
| **USER** | User & group IDs, capabilities | Your own root user (UID 0 mapped to non-root on the host) |
| **CGROUP** | Cgroup hierarchy | Your own cgroup subtree |
| **TIME** | System clocks | Your own system time (monotonic & boot time offsets) |

> [!NOTE]
> Mnemotip: UPNMICT (Ut's Probably Not Magic In Containers Today)

User namespaces enable unprivileged containers.

## Examples

### PID

The `unshare` command creates a new namespace and then executes the specified program:

```bash
unshare -p -f --mount-proc bash
```

If we execute then `ps -ef`, we'll see `PID 1 = bash`, as this namespace has its own PID hierarchy.

### Network

A new network namespace can be created with the `netns` subcommand of the `ip` command:

```bash
sudo ip netns add testns
```

To run a command inside it:

```bash
sudo ip netns exec testns ip a
```

Which will show a completely separate network stack, with only the `lo` interface by default.

### Mount

To create a new mount table:

```bash
sudo unshare -m bash
mount | grep tmpfs
```

Mounts here won't appear in the host's table.
