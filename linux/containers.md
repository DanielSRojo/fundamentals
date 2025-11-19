# How Containers Work

Linux containers are not a single kernel feature. They are the combination of two primitives:

- **namespaces** -> isolate what a process can see
- **cgroups** -> limit what a process can use

Together, they create the illusion of separate systems while still running on the same kernel.

Containers are just normal Linux processes with:

- restricted views of the system (namespaces)
- restricted access to resources (cgroups)
- optionally an isolated root filesystem (rootfs)
- application-level tooling layered on top (Docker, Podman, containerd, Kubernetes)

Containers do *not virtualize* hardware. They share the host kernel.

## 1. Namespaces: Isolation (What processes see)

A container gets its own instance of various kernel resources. Some examples:

- Its own **PID tree**: has a `PID 1` inside the container.
- Its own **network stack**: interfaces, routing, firewall rules.
- Its own **mount namespace**: a private filesystem layout.
- Its own **UTS namespace**: custom hostname.
- Its own **IPC namespace**: isolated shared memory and message queues.
- Its own **user namespace**: root inside, mapped to an unprivileged UID outside.

This explains why processes inside a container cannot "see" host processes, host interfaces, or the host filesystem (unless explicitly bind-mounted).

## 2. cgroups: Resource Control (What processes can use)

cgroups place limits or accounting on:

- CPU scheduling quotas/shares
- Memory usage
- I/O bandwidth
- PIDs
- Hugepages, RDMA, and other resources

This ensures one container cannot starve others or exhaust the node.

Example: Kubernetes enforces pod limits by writing to files under `/sys/fs/cgroup/` for each container.

## 3. The Filesystem: rootfs + chroot + mount namespace

A container usually has its own root filesystem:

```bash
/bin
/etc
/lib
/usr
/var
```

This is provided by an image (layers of tar archives).The *mount namespace* makes that filesystem appear as the only one the container has.

Tools like Docker or containerd set up:

- overlay mounts
- bind mounts (volumes)
- read-only roots
- masked paths

The kernel sees no "image"; it only sees a tree of mounts created in a mount namespace.

## 4. PID 1 and the "mini init" problem

Inside a PID namespace, the first process becomes **PID 1**, which has special kernel semantics:

- it does not receive default signal handling
- it must reap zombie processes
- it sets the lifecycle for the namespace

This is why good container images often use a tiny init system (e.g. `tini`, `dumb-init`) instead of running the main app directly as PID 1.

## 5. The container runtime stack

The stack is generally:

Container orchestrator (Kubernetes) -> Container runtime (containerd/CRI-O/Docker Engine) -> Runtime Interface (runc/crun) -> Kernel (namespaces + cgroups + capabilities)

- **runc** (or crun) is what actually calls `clone()` + namespace flags + cgroup setup.
- **containerd** or **docker** manages images, lifecycle, metadata.
- **Kubernetes** talks to containerd through CRI (Container Runtime Interface).

The kernel ultimately runs the process with the required isolation.

## 6. Containers are processes with constraints

> A container is just a process with some additional configuration applied.

There is no virtualization layer, no hypervisor, no separate kernel. This is why containers start fast, use little overhead, and behave like normal processes - they are.

Running `ps auxf` container processes sitting under a supervisor (e.g. containerd-shim) can be seen, proving they are part of the host's process tree.

## 7. Integration with /proc, /sys and namespaces

Inside a PID namespace, `/proc` is automatically namespaced:

- `/proc` only displays processes in that namespace
- monitoring tools inside see an isolated system

This reinforces the illusion of a separate operating system.
