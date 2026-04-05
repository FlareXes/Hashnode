---
title: "How to Manage Dotfiles, Install Scripts, and Backups on Linux"
seoTitle: "Manage Dotfiles and Backups on Linux"
seoDescription: "Learn to manage dotfiles, installation scripts, and backups for a seamless Linux experience. Ensure smooth setups and solid recovery plans"
datePublished: 2024-12-23T23:30:24.187Z
cuid: cm51o6z3v005n09l7a0ghhpbe
slug: how-to-manage-dotfiles-install-scripts-and-backups-on-linux
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734010424187/b7324682-ba74-4e67-8641-2bfcc0bd732e.gif
tags: linux, github, automation, backup, linux-for-beginners, backup-strategy

---

I’ve got a habit of giving my Operating System a fresh start every six months. Crashed storage drives? Pfft, no sweat! I don't configure `.zshrc`, `neovim`, `firewall` neither do I know how to (joking 😎)? This confidence isn’t just blind luck. Seriously, if you want to be a Linux wizard then **managing Dotfiles, Installation Scripts, and Backups** will be the first thing you gonna after reading this blog post. Dotfiles help you to set up your environment just the way you like it. Installation scripts for handling all the grunt of post-installation. And backups? They’re your insurance policy against digital disasters. Trust me, nail these, and you’ll wonder how you ever lived without them!

# Dotfiles

Installing a new OS is a breeze, but getting everything back to your preferred setup? That’s where time turns into a black hole. Spending hours, days, or even weeks configuring your system just to get everything back to where it was. I'm talking to you, window manager guys.

Dotfiles keeps your configuration settings intact, already tailored your `.zshrc` or `.bashrc` with those handy aliases. why start from scratch every time? With dotfiles, you simply copy and paste your saved settings and get back to work.

1. **Create a Git Repository**: This will be your dotfile storage vault.
    
2. **Copy All the Necessary Files**: Move your important config files into the repository.
    
3. **Commit and Push to GitHub**: To ensure your setup is safe, synced, and accessible from anywhere.
    

But what files should you actually commit? And why bother pushing to GitHub? Let’s dive into that next!

### What files to commit?

It's a tough question to answer because everyone’s setup is as unique and configured differently. But here’s a cheat sheet: check out your `$HOME` directory and think about the files you’re constantly tweaking. Here's some common once:

```plaintext
.bashrc
.bash_profile
.profile
.zshrc
.vimrc
.gitconfig
.gitignore
.ssh/
.config/
```

### Why push to GitHub?

It’s all about convenience, having your dotfiles on GitHub makes it easy to access them during a new installation, so you’re not scrambling to share or find files manually. Besides the fact that your GitHub profile will look cooler with those green squares 😉. And here’s a little pro tip: when committing, keep your files in the same directory structure as they are on your system as shown below. It’ll save you from the classic “where did I put that file?” conundrum.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734009561983/94fc537f-8d11-4944-b03f-fda130205924.png align="left")

## Managing Private Dotfiles

It’s a pretty terrible idea to push sensitive files like `.ssh`, `.gnupg`, or API tokens hidden in your `.bashrc` to GitHub. It’s could be a disaster waiting to happen. Here are a few tricks:

* **Keep Them Offline**: Store your secrets in a secure notes app like Obsidian.
    
* **Encrypt and Upload**: Use something like 7z, KeePassXC or VeraCrypt to encrypt before pushing them to GitHub.
    
* Personally, I keep all my secret stuff offline. I’m also constantly double-checking my dotfiles to ensure no sensitive info is getting leaked.
    

Now, you might’ve heard of [GNU Stow](https://www.gnu.org/software/stow/), a nifty tool for managing dotfiles. It’s pretty cool, but here’s the catch: if not used wisely it can expose your secrets. Sure, there are workarounds, but they’re hit or miss. So, I prefer to keep things a bit more low-key.

# Installation Script

So, you’ve just installed your OS and now you’re stuck with chores, setting up everything—installing apps, creating users, enabling firewalls, and so on. And trying to remember every single piece of software you need? Forget it. That’s where installation scripts come in!

An installation script is basically your to-do list for everything that needs to be set up before you can truly start using your computer. These scripts are totally personal; some folks use them for basic stuff like disk partitions, while others tackle everything that needs to be done in post-installation. That’s why some people call them *setup scripts*.

Here’s how I roll with my bash setup script:

* Install packages from Arch repositories, Flatpak, Snap, and GitHub
    
* Sets ZSH as the default shell
    
* Adds the Blackarch Repository for those sweet hacking tools
    
* Enables SystemD Services
    
* Downloads and sets up dotfiles
    

If you’re curious, check out my [Installation Script](https://github.com/FlareXes/arch-hackset): [https://github.com/FlareXes/arch-hackset](https://github.com/FlareXes/arch-hackset)

# Backup

Let's talk about confidence, nothing beats the peace of mind that comes from knowing you can recover your system, even if it’s been *fried in oil*. Everyone’s got their own backup strategy, but let me walk you through mine. The golden rule? *“I Should Be Able To Retrieve My System Data Even If It’s Fried in Oil.”* But let’s start with the basics.

## Situations

**What if your system crashes due to a misconfiguration or an OS update?**

* **Timeshift** is your best friend. It takes snapshots of your system’s current state, so if something goes wrong, you can roll back to a previous state. It's similar to time machine on MacOS.
    
* Timeshift offers two methods: `BTRFS` and `RSYNC`. Btrfs is incredibly fast but only works on Btrfs file systems, and if you’re using dual drives, backups must be on the same drive. That means if drive fails, your backups are toasts.
    
* Rsync is slower and takes more time to back up, but it works with most Linux file systems and allows backups to a different drive. It’s a bit slower but more versatile.
    

**What if your hard drive crashes?**

* Use `RSYNC`, `BORG`, or the UI version of Borg, `Pika Backup`, to backup to a second drive.
    

Here’s a useful `RSYNC` command where `exclude.txt` specifies files to skip. This method copies files but doesn’t create snapshots like Timeshift, so reverting to the exact previous state is off the table.

```bash
rsync -azh --info=PROGRESS2 --delete --exclude-from="exclude.txt" "$HOME/" "/media/Backup/$(date +%F)/"
```

**What if your system gets fried in oil?**

* Easy-peasy, backup to a different machine, cloud storage, or an external drive using `RSYNC`, `BORG`, or `RClone`.
    

**What if there’s a nuclear attack?**

* Are you alive?.... If so, stay that way. Comment below, and I’ll give you the next steps.
    
* **Serious FlareXes From the Future**: If your threat model includes apocalyptic scenarios, it’s time to ditch this casual blog post and look into serious disaster recovery plans from places like Synology, AWS and similar services.
    

### Backup Schedule

Here’s a quick backup guide to keep your data safe, tweak according to your needs.

| Backup Frequency | Number of Backups | Tools | Where |
| --- | --- | --- | --- |
| **Daily** | 2 | Timeshift | Same Drive |
| **Daily** | 1 | Timeshift, RSync, Borg, etc | Secondary Drive |
| **Weekly** | 1 or 2 | Rclone, Rsync, etc | External Systems |

# Wrapping It Up

There you have it! By implementing these practices, you’ll not only keep your Linux setup experience smooth and consistent but it'll also give you the peace of mind.

By managing dotfiles, you ensure that your environment is always just the way you like it, saving time and frustration. Installation scripts will handle the boring, repetitive setup tasks, so you can spend less time configuring and more time creating. And with a solid backup strategy, you’ll be ready for anything, from minor mishaps to major disasters.

So, Hope you liked it. I’ll see you later—till then, goodbye! 👋