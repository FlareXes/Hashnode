---
title: "Why I'm Learning Go and Not Rust?"
datePublished: Tue Nov 12 2024 03:39:46 GMT+0000 (Coordinated Universal Time)
cuid: cm3dwlw0v000c09jl6js40qm7
slug: why-im-learning-go-and-not-rust
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1731382638919/715d31fd-48d4-4694-9b1d-ae7e5741461e.png
tags: go, python, learning, rust

---

For years, Python has been my go-to programming language. I used it for Web Development, Cryptography, Data Science, and AI/ML (Before ChatGPT, okay). Python also gave me an edge in the hacking world, since it's widely used for developing tools and exploits.

However, Python has few limitations. For one, you need an interpreter to execute Python code. This becomes an issue during vulnerability assessments, since most target machines don’t have Python preinstalled. To work around this, I often switch to compiled or platform-specific languages like C or PowerShell.

Another issue is that it’s not easy to ship closed-source code in Python 👀. Ya! I'm a big proponent of open-source, but sometimes you need to work with closed-source applications as a software engineer. So, I can't just ignore that.

Lastly, Python can be slow—the same old argument, but it’s still true to some degree. It hasn’t bothered me much because I wasn’t working on projects that required extreme speed. Python has some optimizations (like using C extensions) that make it fast enough for most tasks, especially in AI/ML. So, for me, this is the least problematic of the issues.

I needed a language that’s compiled and fast—Golang and Rust both fit these requirements. Then why Go?

Here’s why:

1. Python’s limitations are clear, but when it comes to development, the sky’s the limit. As I mentioned, I usually work with network-based applications, so I don’t need to get into low-level stuff like kernel modules, which Rust is better suited for.
    
2. Go is cross-compilable, which is a huge advantage for penetration testing across multiple platforms. I can write closed-source tools more easily, and it's fast. Go is also widely adopted in cybersecurity, especially in cloud.
    

Rust has these same benefits, except for cloud computing. But Rust has a steep learning curve and lacks a smooth development experience. For me, the ability to quickly write prototypes is crucial. If I can’t rapidly translate ideas into code, my productivity takes a hit.

3. Maintenance is another factor. No language is as easy to maintain as Python, but Rust makes things more complicated with its unique features. Go, on the other hand, is much simpler when it comes to maintenance and keeps development smooth.
    
4. Speaking of speed, when I’m dealing with tasks like bulk file uploads, the speed difference between Go and Rust is usually negligible. Network bandwidth will always be the higher bottleneck, than the speed of execution in the code itself.
    
5. Concurrency in Go is another key factor. Go’s implementation for handling async operations is far better than Python’s. In Python, threading and multiprocessing still feel like workarounds. In Go, the concurrency model is built into the language and feels much more natural and efficient.
    

So, why GoLang and not Rust?

Because I need a compiled language that can run without the need for an interpreter or compiler dependency. I want to easily develop closed-source tools, with a language that is fast enough and doesn’t require excessive development time. Go is also widely adopted, like Python, and is well-suited for tasks like Web/API Development and Cryptography.

In short, Go strikes the right balance for my needs, while Rust, though powerful, feels less practical for my day-to-day development.