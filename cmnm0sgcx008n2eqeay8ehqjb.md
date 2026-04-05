---
title: "Tracing Docker Internals: Socket Communication, Daemon, and Kernel Sharing"
seoTitle: "Docker Internals for Engineers and DevOps"
seoDescription: "Deep dive into Docker internals: how CLI, docker.sock, and dockerd interact with containerd, runc, and the shared Linux kernel."
datePublished: 2026-04-05T17:13:07.284Z
cuid: cmnm0sgcx008n2eqeay8ehqjb
slug: how-docker-works-cli-to-kernel
cover: https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/ad986369-2fcc-4ee0-a437-a339dab4e997.png
tags: linux, docker, devops, hashnode, networking, containers

---

Have you ever run `docker ps` and immediately moved on, as if that command simply floated into the void and came back with container names by magic?

Have you wondered why the Docker CLI sometimes works as `docker ps`, sometimes needing `sudo`?

Docker is not a tiny VM. It is not a mini operating system. It is just a executable binary which sends requests to a root-owned daemon (`dockerd`), which then speaks to the Linux kernel on your behalf. The whole thing is faster than a VM because it reuses the host kernel instead of booting a separate one. That one fact explains a huge amount of Docker behavior.

Let’s build the mental model from the bottom up.

## A quick proof that the kernel is shared

Before we go deeper into Docker internals, we need to establish one key fact:

> Containers share the host kernel.

I’ve already covered this in detail with multiple hands-on PoCs (including kernel modules) in Part 1:

👉 [Modify the Host Kernel from a Docker Container](https://flarexes.com/can-you-modify-the-host-kernel-from-a-docker-container)

So instead of repeating everything, here’s a quick summary of what we observed:

*   **Same boot ID**  
    `/proc/sys/kernel/random/boot_id` is identical on host and container
    
*   **Same kernel version**  
    Even with different distros (Ubuntu host + Arch container), `uname -r` matches
    
*   **Same kernel logs**  
    `dmesg` shows identical kernel buffer output
    
*   **Kernel modification from container**  
    A privileged container can load a kernel module and the host immediately reflects it
    

These progressively demonstrate that containers are not running their own kernel. They are operating directly on the host kernel.

If you want the full step-by-step breakdown (with code, compilation, and kernel-level demos), check out Part 1:

👉 [Modify the Host Kernel from a Docker Container](https://flarexes.com/can-you-modify-the-host-kernel-from-a-docker-container)

**If you prefer seeing this live instead of just reading logs:**

Here’s a full walkthrough of the exact setup, compilation, and kernel behavior from inside the container.

%[https://www.youtube.com/watch?v=lfA4surFhCM] 

## What `docker ps` actually does

Now that we know containers share the kernel, the next question becomes: how do Docker commands actually reach and control it?

When you run: `docker ps`

the Docker CLI does not inspect containers directly. It talks to a daemon.

The CLI is basically a client. The daemon, `dockerd`, is the server.

The path looks like this:

```plaintext
docker CLI -> Unix socket -> dockerd -> containerd -> runc -> Linux kernel
```

Docker is not a giant monolith doing everything itself. It is more like a dispatcher.

A nice mental model is this:

*   `docker` CLI = the remote control
    
*   `dockerd` = the receptionist
    
*   `containerd` = the operations manager
    
*   `runc` = the one that actually sets up the container process
    
*   Linux kernel = the stage on which the whole play happens
    

## Tracing the socket connection with `strace`

This is where the topic becomes fun.

In my own traces, `strace -f -e trace=network docker ps` showed the CLI creating a Unix socket and connecting to `/var/run/docker.sock`, then the daemon side accepted the connection. The Docker service status also showed `TriggeredBy: docker.socket`, and `dockerd` was launched with `-H fd:// --containerd=/run/containerd/containerd.sock`, which matches the systemd socket-activation setup perfectly.

![](https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/73fcc913-c1f5-489c-aa04-b708e262dd3b.png align="center")

The key line from the client side (refer above image) looked like this:

```shell
socket(AF_UNIX, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
connect(3, {sa_family=AF_UNIX, sun_path="/var/run/docker.sock"}, 23) = 0
```

That tells you several important things:

*   it is a Unix domain socket
    
*   it is local IPC, not TCP
    
*   the CLI connects to Docker through `/var/run/docker.sock`
    

On the daemon side, attaching `strace` to `dockerd` showed:

![](https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/da0c38bc-d3d9-4a59-ac0d-57683fcb61e3.png align="center")

```shell
accept4(5, {sa_family=AF_UNIX}, [112 => 2], SOCK_CLOEXEC|SOCK_NONBLOCK) = 18
getsockname(18, {sa_family=AF_UNIX, sun_path="/run/docker.sock"}, [112 => 19]) = 0
```

That is the server accepting the client connection.

So now we have both ends of the story:

```plaintext
docker CLI  ->  connect()
dockerd     ->  accept()
```

That is not theory. That is the actual syscall trail.

## What is Docker’s socket really doing?

`/run/docker.sock` is a Unix socket, not a file you read like a log. It is a local communication endpoint. The Docker CLI opens a connection to it, sends HTTP requests over it, and gets responses back.

Yes, HTTP over a Unix socket. A little weird, but very effective.

You can even bypass the CLI and talk directly to the daemon:

```plaintext
curl --unix-socket /run/docker.sock http://localhost/containers/json
```

That will return the same kind of JSON the CLI uses internally.

That one command teaches a lot:

*   Docker CLI is just a client
    
*   dockerd exposes an API
    
*   the API lives on a Unix socket
    
*   the CLI is not “doing container work” itself
    

Docker is, in a sense, an HTTP API with a very friendly command-line face.

## SystemD socket activation for DockerD

![](https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/9f5c0dec-b328-4561-a216-0c6c46db844b.png align="center")

You may have seen something like this in `systemctl status docker.service`:

```plaintext
TriggeredBy: ● docker.socket
```

That means systemd is managing the socket and can start `docker.service` when there is activity on it.

The status also often shows dockerd started like this:

```plaintext
/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

That `fd://` part means dockerd receives an already-open file descriptor from systemd instead of creating the socket from scratch.

The flow is:

```plaintext
systemd creates /run/docker.sock
docker CLI connects
systemd starts dockerd
systemd passes the socket FD to dockerd
dockerd accepts and serves the request
```

One important correction: systemd does not “proxy” the API traffic. It listens on the socket, starts the service when needed, and hands the socket over. After that, dockerd handles communication directly.

## How does Docker communicate root-owned services (DockerD, ContainerD, etc...)?

This is where `sudo` and permissions become interesting.

The Docker daemon usually runs as root. That is because it needs root privileges to create namespaces, mount filesystems, set cgroups, manipulate networking, and start processes correctly.

The client, however, is just a user-space program. It needs permission to talk to the socket.

That means there are two common ways to use Docker:

*   run `docker` with `sudo`
    
*   add your user to the `docker` group
    

If your user can access `/run/docker.sock`, your user can command the daemon. That is a very big deal.

A subtle but important security lesson: access to the Docker socket is almost equivalent to root on the host. If you can tell a root-owned daemon what to do, you can usually make it do root-level things. That is why the `docker` group is not “just another group.”

A safe mental model:

```plaintext
your user -> docker CLI -> root-owned dockerd -> kernel
```

The CLI is not the power. The socket is the power.

## Why the daemon needs containerd and runc

Docker used to feel like one big thing. Internally, it is more modular now.

The current mental chain is:

```plaintext
docker CLI
  -> dockerd
    -> containerd
      -> runc
        -> Linux kernel
```

What each component does:

*   **docker CLI**: sends requests
    
*   **dockerd**: manages the Docker API and container lifecycle
    
*   **containerd**: container lifecycle and orchestration backend
    
*   **runc**: low-level OCI runtime that creates the actual container process
    
*   **kernel**: namespaces, cgroups, mounts, networking
    

This split is why Docker integrates well with other systems. It also explains why the daemon is not doing everything itself.

## Common misconceptions worth killing politely

| Misconceptions | Reality |
| --- | --- |
| **A container has its own kernel** | No. It uses the host kernel. |
| **Docker CLI creates containers** | No. It talks to dockerd. |
| `sudo docker` **makes the container root** | Not exactly. It gives your user permission to talk to a root-owned daemon via Unix socket `/run/docker.sock`. |
| **Privileged containers are just regular containers** | No. They are much closer to host-level access and dangerous. |
| **Docker socket is just a file** | No. It is a Unix domain socket, a live endpoint for inter-process communication between Docker and DockerD |

## Why this matters in real life

If you debug containers, build platforms, secure hosts, or run production workloads, you need this mental model.

It helps you understand:

*   why Docker works without a VM
    
*   why a container can still hurt the host if misconfigured
    
*   why `docker.sock` is sensitive
    
*   why `--privileged` is dangerous
    
*   why systemd, namespaces, cgroups, and the kernel all matter together
    

In other words, this is the difference between “I use Docker” and “I understand Docker.”

If I had to reduce the whole article to one diagram, it would be this:

```plaintext
User
  |
  v
docker CLI
  |
  v
/run/docker.sock
  |
  v
dockerd
  |
  v
containerd    >>>>> Not Covered
  |
  v
runc          >>>>> Not Covered
  |
  v
Linux kernel
  |
  +--> namespaces
  +--> cgroups
  +--> mounts
  +--> networking
```

## 🎥 Want to go deeper?

If you found this useful, I’ve also put together a full video walkthrough covering everything step by step, including tracing Docker commands with `strace` and understanding the CLI → daemon → kernel flow.

%[https://www.youtube.com/watch?v=lfA4surFhCM] 

## Key takeaways

*   Docker containers share the host kernel.
    
*   The Docker CLI talks to `dockerd` over `/run/docker.sock`.
    
*   `strace` can show the actual `connect()` and `accept()` syscalls.
    
*   systemd socket activation can start Docker through `docker.socket`.
    
*   `dockerd` delegates real container work to `containerd` and `runc`.
    
*   Access to `docker.sock` is effectively high privilege.
    
*   `--privileged` containers can do very dangerous things because they can reach deep into the shared kernel.
    

Thanks for reading.