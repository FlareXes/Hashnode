---
title: "Can You Modify the Host Kernel from a Docker Container?"
seoDescription: "Explore the fascinating relationship between Docker containers and the host kernel in this hands-on article. Discover why Docker is faster than virtual mach"
datePublished: Mon Feb 16 2026 03:36:45 GMT+0000 (Coordinated Universal Time)
cuid: cmlomhq55000902l57cih9rlj
slug: can-you-modify-the-host-kernel-from-a-docker-container
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1771187350709/002a295d-1eee-45d7-8602-ebef12bea7ab.png
tags: linux, docker, hashnode, containers, kernel

---

Can we prove Docker shares the same kernel as the host machine?

Yes — and we can prove it hands-on.

This was actually a random thought. I didn’t research existing proofs or blog posts because I wanted to reason from first principles using my understanding of Linux and Docker. Heck, I don’t even know if there are any. So, there may be more sophisticated PoCs out there, but this one is based purely on Linux and Docker fundamentals I know. So anyone with basic CLI knowledge can follow along.

## Why is Docker Faster than Virtual Machines?

Lame question for this topic. I know but I got commentators to cover.  
But let’s cover the basics in five lines for anyone wondering why we’re even here.

Here’s my least-effort explanation:  
The short answer is ***Docker shares the kernel of the host system***.

Docker containers do **not** boot their own operating system. They do **not** run their own kernel. They are simply isolated processes running on the host’s kernel.

That’s the core idea.

Now let’s prove it in four different ways.

<div data-node-type="callout">
<div data-node-type="callout-emoji">👻</div>
<div data-node-type="callout-text">It’s safer to run these experiments inside a Virtual Machine. We’ll eventually modify the kernel, and you don’t want to risk breaking your primary system.</div>
</div>

## 1\. Observational Proof: Identical Boot ID

Every time a Linux system boots, it generates a unique boot ID. You can find it here: `/proc/sys/kernel/random/boot_id`. So, if a container truly shares the host kernel, both should show the same boot ID.

And, yep! that is the case.

**Host Machine:**

```bash
cat /proc/sys/kernel/random/boot_id
```

**Docker Container:**

```bash
docker run --rm archlinux:latest cat /proc/sys/kernel/random/boot_id
```

As shown in below screenshot they both share the same boot ID.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1771208143758/37d64d11-1507-4329-90af-752da63aecdf.png align="center")

This was easy mode. However, it does not strictly guarantee it. For example, alternative container runtimes like gVisor can virtualize certain kernel interfaces and potentially invalidate this test or PoC.

## 2\. Cross-Distribution Check: Same Running Kernel Version

This one is actually real good. I’m going to use two different Linux flavors just to make the contrast obvious.

Like Such:

* Host system = Ubuntu
    
* Container = Arch Linux
    

You can swap them however you like the distro choice doesn’t matter.

Logic is simple: since both resources allegedly share same kernel then they must show the same Linux kernel version even though they’re different operating systems.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1771208505260/bcf84db7-628f-4bc7-9605-a84c304b9faf.png align="center")

If you’ve fiddled around with different Linux distros, you probably know that each distro typically has its own kernel build format. If Arch were running its own kernel, you’d expect something like:

```bash
~ uname -r
6.18.x-arch1-2   # Arch Linux Kernel Version Format
```

But instead, you see the Ubuntu kernel version.

But Is This Enough? Not really. This only proves both environments report the same kernel version. Doesn’t mean they’re literally share the same kernel.

*“What if Docker somehow copies the host kernel into the container?”*

That would still result in the same version number without necessarily sharing the same running kernel instance. So again — strong evidence. But not really.

## 3\. Runtime Evidence: Shared Kernel Log Buffer

Now we’re getting serious. Because if Docker shares the same kernel instance, then both environments must share the same kernel log buffer. That means `dmesg` output should be exactly the same.

By default, containers cannot access kernel logs. So we must tell docker to grant permission.

**Start Container With *SYSLOG* Capability**

```bash
docker run --rm --cap-add SYSLOG ubuntu:latest bash -c "dmesg | tail -20"
```

You could also use `--privileged` instead of `--cap-add SYSLOG`, but that grants far more access than we need.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1771208969809/33c6adfd-c42a-46a9-9618-f24489b23e78.png align="center")

See, the logs are not just similar — they are identical. This is a very strong evidence.

Could This Still Be Fake? Theoretically, one might argue:

“*What if Docker is exposing /dev/kmsg to containers?”*

Even in that case, the container would still be reading the host’s live kernel message buffer which means there is no separate kernel instance running inside the container.

If you really wanted to push this further, you could even compare checksums of the full `dmesg` output from host and container. But at that point, we’re just mathematically proving what’s already practically obvious.

But we’re not stopping here.

Time for final boss.

<div data-node-type="callout">
<div data-node-type="callout-emoji">🤔</div>
<div data-node-type="callout-text">After thought, my counter logic isn’t that logical here. Checksum match is a strong proof. If Docker were mounting <code>/dev/kmsg</code>, we could easily verify that.</div>
</div>

## 4\. Definitive Proof: Mutating the Kernel from Inside a Container

Let’s go for the head of the guy who’s so skeptical that he thinks Docker has nothing better to do than lie to people.

Login is simple: modifying the kernel from inside the container should affect the host.

So let’s test exactly that. We’ll insert a kernel module from inside the container and observe its effect on the host.

I’ll go with a good old simple “Hello World” kernel module that prints something into the kernel log buffer. Just enough to prove a point. You could take this further and experiment with things like modifying the network stack, blocking traffic, or other kernel-level changes.

We’ll need a bit more configuration for this step.

**Start a Privileged Container With Module Access**

```bash
docker run -it --privileged \
-v /lib/modules:/lib/modules \
-v /usr/src:/usr/src \
ubuntu:latest /bin/bash
```

**Why mount** `/lib/modules` **and** `/usr/src`**?**

* `/lib/modules` contains kernel modules for the running kernel.
    
* `/usr/src` contains kernel headers required to compile modules.
    
* We mount them so the container can compile modules against the host kernel.
    

Without this, module compilation would fail.

Install required tools inside the container

```bash
apt update && apt install nano kmod build-essential linux-headers-$(uname -r)
```

Create a filename `hello_kernel.c` and paste below code.

```c
#include <linux/module.h>
#include <linux/init.h>
#include <linux/kernel.h>

MODULE_LICENSE("GPL");

static int __init hello_init(void)
{
    printk(KERN_INFO "[hello_kernel] Hello, kernel world!\n");
    return 0;
}

static void __exit hello_exit(void)
{
    printk(KERN_INFO "[hello_kernel] Goodbye, kernel world!\n");
}

module_init(hello_init);
module_exit(hello_exit);
```

The above code prints *“\[hello\_kernel\] Hello, kernel world!”* into the kernel log buffer when the module is inserted, and prints *“\[hello\_kernel\] Goodbye, kernel world!”* when the module is removed.

Create a filename `Makefile` in the same directory and paste below code.

```makefile
obj-m += hello_kernel.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

<div data-node-type="callout">
<div data-node-type="callout-emoji">👉</div>
<div data-node-type="callout-text">Keep in mind filename matters if you don’t know how and why. Keep them same as me.</div>
</div>

Once that is done. Let’s compile them inside container with below `make` command.

```bash
make
```

The “make“ command will produce multiple files we only care about `hello_kernel.ko`. It’s kernel object file.

Let’s insert the module in the kernel and as soon as I do that, it would print *“\[hello\_kernel\] Hello, kernel world!“* in kernel’s log buffer.

```bash
insmod hello_kernel.ko
```

Now run `dmesg` on host system and inside container you would see both places hello message printed as show in the screenshot.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1771210191436/a75a36e4-cac1-4a11-9301-fefb5040bbf8.png align="center")

To see goodbye message remove the module from container with below command.

```bash
rmmod hello_kernel
```

Voila! we modified Linux kernel from container which proves that ***Docker containers share the kernel of the host system.***

## But the more interesting part is:

Since the host and Docker container are sharing the same kernel, the module we inserted from inside the container is visible on the host system as well.

Which means we can remove the module from the host system too, and that change will immediately reflect inside the Docker container also.

Because again — there is only one kernel.

**On Host System:**

```bash
lsmod | grep hello_kernel
```

```bash
rmmod hello_kernel
```

```bash
dmesg | tail -5
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1771210706444/1505fdee-ad68-4d90-885d-87a79765c7c1.png align="center")

## Bye Bye!

There are may be cleaner, more formal, or more academically rigorous demonstrations out there and if you know one, I’d genuinely like to see it. This was simply a practical exploration based on how Linux works and how Docker is designed.

But from an engineering standpoint, after inserting a kernel module from inside a container and seeing it reflected instantly on the host, there isn’t much ambiguity left.

Docker doesn’t emulate a kernel. It doesn’t clone one. It shares one.

If you enjoy breaking systems apart and understanding what’s *actually* happening underneath the abstraction layer, you might also find these interesting:

* [Hacking Discord: Why Does Session Hijacking Exist & How it Works?](https://flarexes.com/why-does-session-hijacking-exist-how-it-works-cookies-vs-http-headers)
    
* Most Viewed: [Hyprland Getting-Started: Configure Screen Lock, Authentication and More](https://flarexes.com/hyprland-getting-started-configure-screen-lock-brightness-volume-authentication-and-more).
    

Thanks For Your Time.