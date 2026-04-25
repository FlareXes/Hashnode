---
title: "Thunar Setup on Arch Linux: Enable Trash Support, Image & Video Preview, and Archive Features"
seoDescription: "Quickly fix missing Thunar features on Linux. Enable trash, thumbnails, external drives, and archive support with this complete setup guide."
datePublished: 2026-04-25T03:24:01.251Z
cuid: cmodrz9ep004z2blngxzyboli
slug: thunar-setup-on-arch-linux-enable-trash-support-image-video-preview-and-archive-features
cover: https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/5362fdab-eed8-4835-84c0-8ccec0f3feba.png
tags: linux, archlinux, file-manager, thunar

---

If you just want Thunar to work properly without digging into details, run:

```bash
sudo pacman -S thunar gvfs tumbler ffmpegthumbnailer thunar-archive-plugin
```

Optional (but useful):

```bash
sudo pacman -S thunar-volman thunar-media-tags-plugin
```

What this fixes immediately:

*   gvfs → Enables trash + USB/external drive mounting
    
*   tumbler → Image thumbnail previews
    
*   ffmpegthumbnailer → Video previews
    
*   thunar-archive-plugin → Right-click extract/compress
    

That’s it. Restart Thunar (or log out/in), and everything should work.

If you work with large files, limit preview size to improve performance.

Go to: ***Edit → Preferences → Display***

And Adjust: Thumbnail size Maximum file size for previews

## Now, Details

When you install Thunar without a desktop environment like GNOME or KDE, you’re missing critical background services.

These services handle: File system integration, Device mounting, Thumbnail generation, Archive handling. Thunar itself is intentionally minimal. It relies on external tools to stay lightweight.

On Arch Linux, many tools are installed in a **minimal state by default**. Extra features are provided through **optional dependencies**, not installed automatically.

If something feels missing in any tool, check its optional dependencies:

```plaintext
pacman -Si <package-name>
```

You’ll see a section like:

```plaintext
Optional Deps   : <package>: <what it enables>
```

That’s usually where missing features come from.

💡Example: Thunar

```plaintext
~ pacman -Si thunar
Repository      : extra
Name            : thunar
Version         : 4.20.8-3

....

Depends On      : desktop-file-utils  libexif  hicolor-icon-theme  libnotify  pcre2  libgudev  exo  libxfce4util  libxfce4ui

👇 HERE 👇
Optional Deps   : catfish: file searching
                  gvfs: trash support, mounting with udisk and remote filesystems
                  tumbler: thumbnail previews
                  thunar-volman: removable device management
                  thunar-archive-plugin: archive creation and extraction
                  thunar-media-tags-plugin: view/edit ID3/OGG tags

.....

Validated By    : SHA-256 Sum  Signature
```

**Pro Tips for a Better Thunar Experience**

*   **Custom Actions:** Add right-click shortcuts for scripts
    
*   **Keyboard Shortcuts:** Speed up navigation
    
*   **Terminal Integration:** Open terminal in current directory
    

## TL;DR

With just a few packages, Thunar transforms from barebones to fully functional.

| Feature | Minimal Install | After Setup |
| --- | --- | --- |
| Trash | ❌ | ✅ |
| USB Drives | ❌ | ✅ |
| Image Preview | ❌ | ✅ |
| Video Preview | ❌ | ✅ |
| Archive Support | ❌ | ✅ |