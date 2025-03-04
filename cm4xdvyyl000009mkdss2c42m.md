---
title: "Dell XPS for Linux & My Personal Experience"
seoDescription: "Explore the pros and cons of using a Dell XPS for Linux. Does this high-performance machine justify its challenges for Linux enthusiasts?"
datePublished: Fri Dec 20 2024 23:30:49 GMT+0000 (Coordinated Universal Time)
cuid: cm4xdvyyl000009mkdss2c42m
slug: dell-xps-for-linux-my-personal-experience-review
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717674477348/dfd1aaed-784f-4b87-8a51-ca1cf517e228.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1734739551162/2aeb2480-98e3-48a9-a106-d021ecb0c3bc.jpeg
tags: linux, linux-for-beginners, xps-9510, wayland, hyprland, dell-xps, xps

---

So, let’s talk about the Dell XPS. Yeah, that pocket-denting investment I (okay, my family) made just so I could learn Active Directory. The real story, I trashed my previous laptop (now server) in a rainstorm during my internship. Active Directory was the perfect excuse to get my hands on the XPS. And honestly? Worth every penny. Sort of. Kind of. (forget I said that).

In this blog, I'll walk you through a list of cons and a few pros (who cares?) of using the Dell XPS as a daily Linux driver. Yep, Linux.

Linux users, I'll start with the software aspects and then cover the hardware in the second half. And don't expect specs review.

Windows users, you won't regret buying an XPS as long as you're cool with the fact that gaming laptops with the same specs cost half as much. Yeah, you read that right—half. That’s it for you, Windows users.

Mac users? Guys, Just switch to anything that is not Apple. If you’re in tech, consider yourself a tech nerd, or even remotely associate with IT, just make the switch. Trust me on this one (don't, people say he is biased).

# What is Dell XPS?

Dell XPS is premium series of laptop has been around since 1993. These machines are powerhouses in both performance and looks. Best OLED screen, smooth trackpad, and speakers that will blow your mind. If you consider "Apple MacBook Pro" the best laptop out there, multiply that by ten, and you will get a Dell XPS. ChatGPT calls it "it’s the crème de la crème of laptops" (what?).

$$MacBook \times 10 = Dell \ XPS$$

Alright, Let’s get to the real talk, that was a joke.

# Software: Linux on XPS

Terrible experience. If you’re new to Linux, consider this a Total Disaster 😄. And it's not just the XPS; any machine with a 4K display will give you a hard time. Tighten your seat belts and brace for the crash (fanboy moments over).

### Speakers won't work

Haha 🤣 nice start. Linux doesn't seem to have audio drivers for the XPS series. Speakers will sound too low, low enough that won't be able to listen in library noise.

But let’s take a moment to appreciate those XPS speakers. They are too good to be true for a laptop. I tried them out on Windows while downloading an Arch ISO 😄.

**Solution:**

Linux is powerful. There's a workaround for these pesky audio issues. You can boost those speakers up to 200% or more. I've got mine maxed out at 150% on my system. But don't expect same sound quality (it's bad, but workable).

I've configured my volume controls with keybindings in Hyprland.

Note: On arch full system upgrade can resolve this issue, but the audio quality still doesn't match that of Windows.

```bash
# Increase Volume
wpctl set-volume -l 1.5 @DEFAULT_AUDIO_SINK@ 10%+

# Decrease Volume
wpctl set-volume @DEFAULT_AUDIO_SINK@ 10%-
```

### Life Savior: GNOME and KDE Desktop Environments

If you’re rolling with desktop environments like GNOME or KDE, you’re in the clear for the most part. Of course, the speakers will still be funky, but nothing too major.  
So, if you’re rocking a mainstream Linux distro like Ubuntu, Fedora, or Manjaro, you’re in the clear because these distribution ships with GNOME or KDE right out of the box. That means the majority of Linux users are cruising just fine, except "I use Arch by the way" pros and bros, of course.

But problems become exponential if you’re dabbling in XFCE, Mate, or dare I say it, window managers like DWM, BSPWM, or Openbox. But before we dive into those problems let’s first understand why these issues exist in the first place for our pros and bros.

## The Root Cause: Why Do We Face These Issues?

These issues are not exclusive to Dell XPS. Every Linux machine rocking a 4K display is in the same boat. Linux’s widely adopted display server is Xorg, an X11 protocol implementation. But X11 was never designed to work with 4K displays.

To redeem X11 problems (4k display & security issues) a new protocol was introduced **Wayland** in 2008. **Wayland** is considered to be the future of Linux's display server and I agree with the statement. But not everything is sunshine and rainbows with Wayland either. It has got its fair share of issues, namely lack of widespread adoption.

Curious about the security side of things? Check out my LinkedIn post about the X11 vulnerability and [*Why Most Linux Distributions Are Insecure?*](https://www.linkedin.com/posts/flarexes_linux-linuxsecurity-security-activity-7109527777854259204-NFwS)

### Wayland: The Hidden Gem

"Wayland is not ready!", that's what you will hear from few seasoned Linux users. I don't agree with this statement completely. Wayland is a game-changer. It has tackled problems that Xorg could only dream of solving. Thanks to Wayland, Linux now supports 4K displays without inherent security vulnerabilities etc.

But, That's doesn't mean it's ready for new Linux users, it's not. Here’s the deal: Any software built on Electron is going to look teared on Wayland, including Chrome, Brave, Discord, VSCode. And don’t even get me started on JetBrains IDE or alike Java applications because many applications are not supported by wayland.

Btw, many Electron-based apps can be run nicely with Wayland. Just run them with `--enable-features=UseOzonePlatform --ozone-platform=wayland` at the end of your launch command. But, Applications are not based on electron and doesn't support wayland will still look teared.

### Why Only GNOME & KDE Works?

GNOME and KDE are massive projects in Linux community with rapid development cycles. Ever wondered why GNOME and KDE seem to have all the answers? Because if you rocking them, you won't face many issues that we are going to discuss later in this blog post.

Here’s the secret sauce: GNOME and KDE use hybrid approach. They seamlessly blend Wayland and X11 to give you the best of both worlds. So, if you’re rocking a 4K display, Wayland takes the wheel. But if an app isn’t ready for the Wayland it will fallback to X11 implementation. This hybrid project is called [*Xwayland*](https://wayland.freedesktop.org/xserver.html), a X Clients under Wayland.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">If you don't use GNOME or KDE and you have 4k display, You will have to switch to Wayland. There is no way around.</div>
</div>

## Wayland on Dell XPS (4K Display)

### Screen Sharing: Nightmare

Screen sharing works on wayland. But you may find yourself attempting to share your screen multiple times. Sometimes it sticks in one go, sometimes it doesn’t.

Estimated Try & Hits: 1-10

### Mic Won't Work - Sometimes

I'm not sure about this one. I’m currently rocking Arch, and everything seems fine. But I had issues initially or maybe it was just a glitch. I haven’t stumbled upon much complaints about XPS mics on internet. So, It's safe to assume they work fine. Do your due diligence to be sure.

### Global Keybinding Won't Work

Global keybinding won't work. What does that mean? This means that applications running in the background cannot respond to your shortcuts unless they are in focus. For example, you cannot start or stop screen recording in OBS-Studio via keybinds unless OBS-Studio is the active window. Similarly, if you press `ALT + SHIFT + T` to open a to-do app, it won't work unless it's focused.

However, shortcuts within individual applications like VSCode would work perfectly fine. This limitation is caused by Wayland's security features, which isolates each window from the others to protect your system from keylogging and screen capture assaults.

Same example explained here: [*Why Most Linux Distributions Are Insecure?*](https://www.linkedin.com/posts/flarexes_linux-linuxsecurity-security-activity-7109527777854259204-NFwS)

**Key Takeaway:** Only global keybindings are affected; application-specific shortcuts are still fully functioning.

### Your Scripts May Not Work

If your scripts relies on X11 utilities, you'll need to rewrite them for Wayland compatibility. Thankfully, many alternatives to X11 utilities are available for Wayland that make the switch easy, like:

| S.No. | X11 | Wayland |
| --- | --- | --- |
| 1. | xclip | wl-copy, wl-paste |
| 2. | rofi | rofi-wayland |
| 3. | xdotool | wtype |

### Chrome Browsers & Other Applications

Electron-based applications work fine on Wayland, and so do Chrome-based browsers, but there's a catch—they take about 2 minutes to start. Yes, you read that right! This is why I always recommend Firefox-based browsers. While Chrome browsers may run with slight display tearing (not that terrible).

Some applications, however, simply won’t work on Wayland, even with display tearing. The solution is to install a desktop environment alongside your window manager. Or you can try heavy configuration headache to make them work 😕.

* [*Brave vs LibreWolf : Privacy and Security Without Conditions 🔏*](https://flarexes.com/brave-vs-librewolf-privacy-and-security-without-conditions)
    

### Fingerprint Senor Works Fine

Yaa! Finally, something that works! Believe it or not, having a fingerprint sensor that works on Linux is a big deal. Just like audio drivers, there aren't many systems out there that support fingerprint unlocking on Linux. But guess what? Dell XPS nails it! So, feel free to enjoy the smooth fingerprint unlocking.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Configuring the fingerprint sensor in Desktop Environments (DE) is way easier than in Window Managers (WM).</div>
</div>

# Let's Talk About Hardware

First things first, I won't bore you with the XPS specs like CPU, GPU, or RAM because, honestly, they vary. And who has the time for that? This section is gonna be short and sweet. Remember, I'm a software person, not the hardware one.

### Speakers: Bad Location

Dell XPS speakers are located on top of the chassis near the keyboard. Not on the sides, not on the back, but right on top. It may seem cool at first, but eventually it turns into a dust magnet. You'll be cleaning them more often than you'd like. I have to say, this might be the best place for better audio quality, but on Linux, we don't have much of a choice in the matter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717674963146/a404db52-9cad-4815-8b6b-dad0e7ad84c9.avif align="center")

### Keyboard: Too Spread Out

Everyone seems to love the XPS keyboards, but after trying out dozens of laptop keyboards, I have to say, this one feels weird. The keyboard is just too spread out. It’s great quality top-notch, but still feels off for some weird reason. It took me days to get the hang of it, whereas normally it only takes hours to adjust.

The internet loves to compare XPS keyboards to MacBook’s, claiming they’re equally good. Quality-wise, there’s hardly any difference, but I have to admit (I don't wanna say this 🤢) - the MacBook keyboard feels much better while typing.

### Screen: Best OLED 4K

Did I mention that XPS has an amazing 4K OLED screen? Sure, it caused a lot of headaches in the software section, but come on, you’ve got to appreciate that stunning display. And let’s face it, eventually, we’re all gonna have to switch to Wayland anyway.

Speaking of the screen, have you noticed those bezels in cover image? They’re so tiny you might miss the camera if you’re not looking closely. Dell really nailed the design here.

### Touchpad: Important Read This

Alright, gather round, folks. We need to have a serious chat about the touchpad situation on the Dell XPS. XPS had got this chronic issue of broken touchpads. Dell doesn't seem to take proper action on this. Maybe it's a design flaw, who knows? If you’ve got a busted piece, get it replaced. 'Cause you have withdrawn some serious cash for this machine.

Mine is broken too. is? The right click works, but it feels wonky compared to the left click. Did I bother to get it replaced? Nah, too lazy for that. I mean, if I can run an entire operating system full of errors, I can handle a dodgy touchpad, right? Arch user, by the way! 🤣

But seriously, you better get it replaced.

**let's talk about touchpad feel.**  
Slipping on ice it's that smooth. But It gets patches for no good reason—again, design flaw, anyone?

**Do you want a compression with Mac?**  
Here we go then, Feels better then MacBooks, it's too soft. But MacBook has better clicking mechanism. By the way, I don't understand after paying so much why dell doesn't use macbook's like motor clicking mechanism? anyway who even bothers clicking on a touchpad these days? (MacBook users 🤣).

### Charger & Charging: Not Impressive

I don't like the charger built because of one reason spotted in the screenshot below. One wrong move and poof - charger is no more. And charging is slow it takes solid two hours to get fully charged. The battery lasts for 6-7 hours while programming without GPU usage.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717675291095/767e1438-686c-4a70-8c8c-5dfb37257c81.png align="center")

### USB Ports: Okayish

The Dell XPS 15 rolls in with four ports. Three USB Type-C ports and one useless SD Card slot. But hey, Dell is not Apple. They ship an external adapter which has a HDMI and a USB slot.

### One Hand Opening: Can't Do That

Have you seen people bragging about opening their laptop lid with one hand or finger? Ya! that's thing in non-tech people (few tech once too). But you can't do that in XPS. Despite its solid build, the design doesn't leave enough space to allow for easy one-finger or one-hand opening. So, You gotta use both hands.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717675331549/19c05214-d270-4235-8e6f-1bfffd2a3350.gif align="center")

### Carbon Fiber: Don't Finger That

Dell XPS is equipped with Carbon Fiber which people really seems to care about but I don't. Carbon fiber make XPS lightweight. I won't necessary call it lightweight machine. And this carbon fiber is externally coated with a rubber-thingy plastic material. Don't touch that, finger that or nail that otherwise it'll peel off and trust me, you don't want that. Why? It won't look good, I know it and I'm telling you. Just jussst keep your hands off from it. OKAY OKAYYY.

Of course, If you're an Arch user, chances are you have more important things to worry about than that stupid, non-sense carbon fiber coating.

# Personal Experience

So, do I regret buying the Dell XPS? Surprisingly, **NO**. This blog post is plotted around challenges that a Linux users may face with high-end machines sporting a 4K display like the XPS. However, what you may not realize is that there's no real competition for the Dell XPS in the market. It’s a powerful, jam-packed machine.

Sure, I had my doubts at first. I mean, who wouldn’t? Suddenly, my favorite Linux OS Archcraft and it's themes were out of the picture, and I was thrown into uncharted territory. But you know what? Sometimes, it’s those unexpected twists that lead us to the greatest discoveries. And let me tell you, this journey with the Dell XPS? It’s been one wild ride, marking the third J curve in my Linux journey (first was switching to Linux, second was switching to window manager). Who knew a laptop could shake things up so much?

#### Hyprland: The J Curve

By now I had two things crystal clear in my mind. One, I have to switch to Wayland. And two, No way I'm turning back to cozy desktop environments like Gnome. Nah, not my style. I was a BSPWM kind of person, craving that minimalist vibe. And then came Hyprland — a Wayland compositor. With its flashy animations and user-friendly documentation, Hyprland had me sold. Looking on the bright side: if it wasn’t for the XPS, I probably wouldn’t have taken the plunge into Wayland, the future of Linux. And you know what? Everything's running smoother than ever now.

# Conclusion

So, here’s the deal with the Dell XPS—it is is a powerful laptop, there are difficulties when using Linux on it. With Wayland, the desktop environments of GNOME and KDE provide improved compatibility through a hybrid method. Projects like Hyprland and Sway are also working to bring that experience with a pure wayland implementation in windows managers.

Overall, once you get the hang of it on Linux, there’s no turning back. I mean, after this bad boy, I haven’t spared a second thought on resources management. And if you want a setup guide for Hyprland than let me know.

I hope you found this helpful. Thanks for sticking around!