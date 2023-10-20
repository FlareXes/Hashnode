---
title: "Cisco Packet Tracer Installation In Arch OS"
seoTitle: "how to install cisco packet tracer in arch os"
seoDescription: "Cisco Packet Tracer is really a handful to learn many computer science concepts. But, installing it on Arch based system require few extra steps."
datePublished: Fri Oct 21 2022 03:37:12 GMT+0000 (Coordinated Universal Time)
cuid: cl9hxy56z000009mobbagdm14
slug: cisco-packet-tracer-installation-in-arch-os
canonical: https://flarexes.com/cisco-packet-tracer-installation-in-arch-os
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1666322960109/M7iJ-xMzN.png
tags: linux, archlinux, installation, cisco, packet-tracer

---

Cisco Packet Tracer should be a must-have tool for any Network Engineer. Not only for Network Engineers, packet tracer is really a handful tool to learn many computer science concepts. Despite being such an amazing software, there is no official support for many Linux distros including **Arch**. But, installing packet tracer on Arch-based distro would only take 5 steps.

1. Download Cisco Packet Tracer **.deb** from [Cisco Netacad](https://www.netacad.com/). You will find this under the **Resources** Section. Download *Ubuntu Desktop Version*.
    

> Note: You have to have an account on [Netacad](https://www.netacad.com/) to download Packet Tracer

1. Download or clone the build files from [packettracer](https://aur.archlinux.org/packages/packettracer) AUR.
    

```sh
git clone https://aur.archlinux.org/packettracer.git
```

1. Move the **.deb** file into the cloned packettracer directory where your build files are. Now, your folder structure should appear as below.
    

```plaintext
 .
├──  CiscoPacketTracer_820_Ubuntu_64bit.deb
├──  packettracer.sh
└──  PKGBUILD
```

1. Let's build the package. After executing the below command, it will output a *.pkg.tar.zst* file. Build-time dependencies and temporary files will be removed after installation.
    

```sh
makepkg -sirc
```

1. Only do this step if Packet Tracer isn't installed by now on your system. Or, if you didn't use **\-i** flag in the above command. So, this below command actually takes *.pkg.tar.zst* file as an argument to manually install it.
    

```sh
sudo pacman -U package_name-version-architecture.pkg.tar.zst
```

That's it. Still stuck! Check out [Arch Wiki On Packet Tracer](https://wiki.archlinux.org/title/PacketTracer).