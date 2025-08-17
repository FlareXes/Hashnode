---
title: "Fix Missing Directories & External Drives in Thunar File Manager on Hyprland"
seoDescription: "Fix missing directories and external drives in Thunar on Hyprland by installing gvfs and xdg-user-dirs for a complete experience"
datePublished: Sat Aug 16 2025 16:27:58 GMT+0000 (Coordinated Universal Time)
cuid: cmeeh0r8p000102jpat9q7wli
slug: fix-missing-directories-and-external-drives-in-thunar-file-manager-on-hyprland
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/rFnKnVz6XmQ/upload/74616ca77af3624e4c31dc83e755135a.jpeg
tags: software-development, linux, technology, hashnode, hyprland

---

If you're running **Hyprland** (or another minimal window manager) and notice that your file manage like **Thunar** doesn't show your usual *Documents, Downloads, Pictures,* or external drives in the sidebar - That's because you are simply missing a couple of packages that a full desktop environment (like GNOME) would normally install by default.

## Quick Fix

Just install the following:

```bash
sudo pacman -S gvfs xdg-user-dirs
```

* **gvfs**: Lets you do things like move files to the trash, access network shares, and mount USB drives easily in your file manager. In most cases, this alone solves the issue.
    
* **xdg-user-dirs**: Creates standard folders like "Documents", "Downloads", "Pictures", etc., in your home directory, according to the XDG ([Freedesktop.org](http://Freedesktop.org)) specifications.
    

## How I Debugged It

When I first switched to Hyprland, I hit this problem. Searching online didn’t help, so I tried installing **Nautilus** (GNOME’s file manager) just to test.

To my surprise, not only did Nautilus work - it also fixed Thunar. That was the clue.

To dig deeper I checked Nautilus' dependencies:

```plaintext
❯ pacman -Si nautilus                                                                                                             20:35 
Repository      : extra
Name            : nautilus
...

Depends On      : ...  gvfs  ...  xdg-user-dirs-gtk  ...

...
Download Size   : 2.39 MiB
Installed Size  : 13.59 MiB
Validated By    : SHA-256 Sum  Signature
```

With the help AI explanation on dependencies, among the many packages, two stood out. That's what **Nautilus** was pulling `gvfs` and `xdg-user-dirs`, which **Thunar** quietly relies on but doesn't require by default.

## Takeaway

If you’re using Thunar in Hyprland (or any barebones WM) and the sidebar looks empty:

1. Install `gvfs` (and optionally `xdg-user-dirs`).
    
2. Restart Thunar.
    
3. Enjoy your standard folders and USB drives showing up again.
    

Hopefully this saves you from the same rabbit hole I went down. Minimal WMs are powerful, but they don’t hold your hand — sometimes you need to add the missing plumbing yourself.

**I'm grateful you took the time to read what I've shared. Until next time —** [**FlareXes**](https://www.linkedin.com/in/flarexes/)

**Further Reading**  
If you’re exploring Hyprland further, you might also enjoy my [step-by-step guide on configuring essentials like screen lock, brightness, and authentication](https://flarexes.com/hyprland-getting-started-configure-screen-lock-brightness-volume-authentication-and-more).