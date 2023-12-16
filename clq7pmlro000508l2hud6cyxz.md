---
title: "Linux Firejail: Securely Throw Untrusted Applications Behind Bars"
datePublished: Sat Dec 16 2023 07:01:35 GMT+0000 (Coordinated Universal Time)
cuid: clq7pmlro000508l2hud6cyxz
slug: linux-firejail-securely-throw-untrusted-applications-behind-bars
canonical: https://flarexes.com/linux-firejail-securely-throw-untrusted-applications-behind-bars
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1702709625069/abb4aed9-f8f3-42c1-b9dd-8dba8bb7fd1c.jpeg
tags: linux, security, privacy, sandbox, firejail

---

Sandboxing or Containerization are always considered the ultimate weapons for high Privacy and Security threat models. The most renowned privacy and security tools like **Whonix, Qubes OS, Tail** and **Docker** are focused on Sandboxing one way or another. What they actually do is effectively isolating various components of applications like network interfaces and file system to prevent unwanted connections.

To get started, let's delve into how **Firejail** operates and then explore how seamlessly you can incorporate it into your security toolkit.

# What is Sandboxing?

Sandboxing is a security mechanism for running programs and processes inside an isolated environment with limited access to resources. What happens in the Sandbox stays in the Sandbox; I mean, it won't affect your host machine, just like virtual machines. The concept of sandboxing is not new; many of us unknowingly engage with sandbox-enabled programs daily. Web browsers, Snap or Flatpak packages, and Electron-based software like Discord and VSCode use sandboxing for security purposes.

LiveOverflow has an insightful video explaining Browser Sandboxing.

%[https://www.youtube.com/watch?v=StQ_6juJlZY] 

# Why use Firejail?

The most secure way to live in a virtual world is to live in a virtual machine. Of course, That's not practical for everyday users, and that's why we have tools like Qubes and Whonix. But to be honest, they can also be somewhat daunting for the average user.

And most of us don't require these high threat model tools. We aren't hiding from Governments or intelligence agencies like Edward Snowden. Instead, we're concerned about more common scenarios. Picture your friend who recently discovered malware and unknowingly sent one within a PDF. In these situations, what we need is a middle ground like **Firejail** that strikes a balance between security and usability. A solution that seamlessly integrates into our everyday work environment.

## How do I use Firejail?

So let's be real, I don't use Firejail for every single application. I mainly leverage Firejail for two specific purposes:

1. For Viewing Documents or PDFs
    
2. Running Untrusted Applications, Especially GitHub Packages
    

## Why not AppArmor, Snap or Flatpak?

### AppArmor

1. Unlike Firejail, AppArmor demands some initial configurations.
    
2. However, AppArmor stands as a viable alternative to Firejail, and we can delve into its merits—feel free to comment below if interested.
    

### Snaps & Flatpak

1. Both Offer Limited Control Over Applications.
    
2. While not every application comes with a Snap or Flatpak package, Firejail outshines by running any application in a sandbox, irrespective of packaging.
    
3. Flatpak, due to package size concerns, isn't my preference.
    
4. Unlike Firejail, Snaps & Flatpak lack internal access to the sandbox.
    
5. Simplicity is on the side of Snaps & Flatpak, making them more beginner-friendly.
    

Someone on internet said **"Snaps and Flatpaks ensure security based on developers' intentions, whereas Firejail is an additional layer you can incorporate into an application for enhanced security"**. Notably, Flatpak can be fine-tuned with [Flatseal](https://flathub.org/apps/com.github.tchx84.Flatseal).

# How does Firejail work?

Firejail is a command-line utility that uses security profiles. It comes bundled with thousands of well-known software profiles, giving Firejail a significant advantage over other similar tools. These profiles are located under `/etc/firejail`, contain crucial specifications dictating an application's behavior. For instance, my PDF viewer is confined to the Downloads directory, with no internet access. Moreover, Firejail extends its functionality to custom profiles, enabling users to run lesser-known software within a protected environment.

Running applications under the Firejail Sandbox is quite easy; just prefix your command with "firejail". E.g.

```plaintext
$ firejail firefox                       # starting Mozilla Firefox
$ firejail vlc                           # starting VideoLAN Client
```

The underlying technology that powers Firejail and other similar programs like Docker, Flatpak, Snaps is [**Linux Namespaces**](https://lwn.net/Articles/531114/).

LiveOverflow also has an amazing video about Namespaces, definitely worth checking out.

%[https://www.youtube.com/watch?v=-YnMr1lj4Z8] 

# Firejail Basics and Workflow

## Installation

Firejail can be conveniently retrieved from the official Linux repository using your distro's package manager.

**Arch Linux**

```bash
sudo pacman -S firejail
```

**Debian/Ubuntu Linux**

```bash
sudo apt install firejail
```

## Sandboxing Firefox

The following command will open Firefox in a sandbox environment with a specific set of requirements.

```bash
firejail --x11 --private --net=eth0 --dns=1.1.1.1 --dns=9.9.9.9 --hosts-file=~/adblock firefox --no-remote
```

* Default Setup
    
    * `--no-remote` -&gt; Prevents opening new tabs or windows attached to the existing Firefox process.
        
* Private Browser Setup
    
    * `--private` -&gt; Initiates Firefox with an empty home directory, resulting in a factory default browser configuration.
        
    * `--dns=1.1.1.1` -&gt; Specifies a custom DNS configuration for your sandbox.
        
* Network Setup
    
    * `--net=eth0` -&gt; Assigns a random, unused IP address from the specified interface.
        
    * `--hosts-file=~/adblock` -&gt; Adds a hosts file implementing an adblocker.
        
* X11 Sandbox
    
    * `--x11` -&gt; Prevents X11 keyboard loggers and screenshot utilities from accessing the X11 server.
        

By default, Firejail assigns random IP and MAC addresses to your sandbox, disappearing once the sandbox is closed. Firejail can run multiple applications in parallel, each with a different IP address.

## Sandbox Internal Access

Firejail also provides a way to verify that Firefox is indeed running inside a sandbox.

List all running sandboxes:

```bash
   ~ firejail --list
26893:flarexes::firejail --private=/home/radowoo/Downloads --dns=9.9.9.9 firefox --no-remote
```

Attach to the Firefox sandbox using its ID.

```bash
   ~ firejail --join=26893
```

This allows you to initiate a shell within the Firefox sandbox.

## Firejail Profiles

Profiles offer a streamlined approach to lengthy Firejail commands. For instance, in the case of Firefox, Firejail already includes a dedicated profile. As highlighted earlier, Firejail is equipped with an extensive library of pre-configured profiles for well-known applications.

Executing the Firefox command with its profile is straightforward:

```bash
   ~ firejail --profile=firefox firefox
```

Simple enough right? All the pre-bundled profiles can be found at `/etc/firejail`, If you ever find the need to tailor your security measures.

### Custom Profiles

When Firejail lacks an application-specific profile, take matters into your own hands by creating one. For a more organized workflow, it's advisable to store custom profiles under `~/.config/firejail` instead of the present working directory.

#### Step 1: Build a Custom Profile

Launch a terminal and execute the following command. This builds a custom profile named `my-app.profile` for the application `my-app`:

```bash
   ~ firejail --build=my-app.profile my-app
```

This command runs my-app in a sandboxed environment, recording the system calls it makes.

#### Step 2: Edit and Refine the Custom Profile

Refining your profile involves the following steps:

1. Open the `my-app.profile` file using a text editor.
    
2. Compare the generated profile with existing similar profiles in the `/etc/firejail` directory.
    
3. Select the necessary features, referring to [Arch Docs](https://man.archlinux.org/man/firejail-profile.5).
    

#### Step 3: Launch the Application with the Custom Profile

After refining the custom profile, launch the application using it with the following command:

```bash
   ~ firejail --profile=~/.config/firejail/my-app.profile my-app
```

This command initiates the application `my-app` within a sandboxed environment, harnessing the tailored security measures you've crafted.

# Conclusion

In a world full of digital risks, securing our online activities is crucial. Firejail stands out as a flexible solution, offering a middle ground between high-security options like Qubes or Whonix and the practical needs of everyday use cases.

With a simple prefix to commands, users can launch applications in a secure sandbox effortlessly. Whether it's browsing documents, viewing PDFs, or cautiously running applications from untrusted sources.

In essence, Firejail, a Linux command-line utility focused on enhancing privacy and security through sandboxing. We have explored how Firejail operates using security profiles, its installation process, and examples of sandboxing applications like Firefox. Whether you desire a straightforward approach or a more sophisticated, Firejail can do both.

### Resources

If You Wanna Study Firejail In-Depth Check Resources Below:

1. Fixing X11 Vulnerability:
    
    * [X11 Guide](https://firejail.wordpress.com/documentation-2/x11-guide/)
        
    * [Probably the best X11 sandboxing guide out there!](https://wiki.gentoo.org/wiki/User:Sakaki/Sakaki%27s_EFI_Install_Guide/Sandboxing_the_Firefox_Browser_with_Firejail)
        
2. Sandboxing AppImage:
    
    * [AppImage Support](https://firejail.wordpress.com/documentation-2/appimage-support/)
        
3. Profiles:
    
    * [Building Custom Profiles](https://firejail.wordpress.com/documentation-2/building-custom-profiles/)
        
    * [All Profiles Feature](https://man.archlinux.org/man/firejail-profile.5.en)
        
4. Firejail Guide On Linux Capabilities:
    
    * [Linux Capabilities Guide](https://firejail.wordpress.com/documentation-2/linux-capabilities-guide/)
        
5. Video Guides:
    
    * [Firejail Video Guides](https://odysee.com/@netblue30:9?view=content)
        

---

Thanks For Reading, Bye 👋