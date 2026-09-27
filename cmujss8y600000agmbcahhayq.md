---
title: "Wait, That's an IP Address?"
datePublished: 2026-09-27T12:31:24.785Z
cuid: cmujss8y600000agmbcahhayq
slug: ip-address-obfuscation-decimal-hex-octal
cover: https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/dea2173d-4c8a-4b96-b5b5-daa0ce700a16.png
tags: security, research, networking, cybersecurity, obfuscation, ipv4, ip-address, browser-security, security-research, ip-address-parsing

---

I'll be honest. After years being in cybersecurity, finding out mid-malware-analysis read that `45.39.21646` is a completely valid, resolvable IP address. Genuinely stopped me for a second. My initial assumption was that it could be an obfuscated IP address that the malware would reconstruct later in the execution chain. It wasn't.

It's actually a **40+ year old feature of the networking stack**. I suspect many of us have gone years without realizing this behavior has been quietly sitting at the intersection of network history, parser design, and real, current CVEs. So I decided to go all the way down this rabbit hole.

## Yes, That's an IP Address

An IPv4 address at its core is just a **32-bit unsigned integer**. The dotted-quad notation (`192.168.0.1`) is a *human convenience layer* on top of that integer. It's not the only one, and it was never the only one the parsing code accepted.

The classic "shorthand" parser (more on its origin in a second) accepts the address in **1, 2, 3, or 4 parts**, and whatever the *last* part is, it absorbs however many bits are left over:

| Parts | Format | Bit-width of last part | Example | Resolves to |
| --- | --- | --- | --- | --- |
| 4 | `a.b.c.d` | 8 bits each | `192.168.0.1` | `192.168.0.1` |
| 3 | `a.b.c` | last part = 16 bits | `45.39.21646` | `45.39.84.142` |
| 2 | `a.b` | last part = 24 bits | `127.1` | `127.0.0.1` |
| 1 | `a` | whole thing = 32 bits | `755396366` | `45.6.111.14` |

And each individual number, in any of those forms, can independently be written in:

*   Decimal: `84`
    
*   Octal: any value with a leading `0`, so `0124` = 84 decimal
    
*   Hexadecimal: any value with a leading `0x`, so `0x54` = 84 decimal
    

Not just that you can also mix those bases within the same address. `0x2d.39.0124.216` is a legal way to write `45.39.84.216` (first octet hex, second decimal, third octal, fourth decimal that looks octal but isn't because no leading zero this is exactly the kind of ambiguity that causes bugs, which we'll get to).

### Walking through an example

`45.39.21646` has 3 parts, so the last number (`21646`) is a 16-bit value that gets split into the final two octets:

```plaintext
21646 = 84 × 256 + 142
       → high byte = 84
       → low byte  = 142
```

Result: `45.39.84.142`

Converted to other notations, that same address is:

*   Full decimal integer: `757552270`
    
*   Full hex: `0x2D27548E`
    
*   Full octal (per-octet): `055.047.0124.0216`
    

All four of these strings, along with `45.39.21646`, are **the exact same destination**.

## So Why Does This Exist?

According to my research, "no one actually knows" why IPv4 ended up with multiple valid textual notations. There's a lot of speculation around it, but very little hard evidence.

I came to this conclusion - it started unintentionally because how early Unix/BSD networking code implemented IPv4 parsing. Then they kept it as a **convenience** for network administrators. In the early Internet, addressing was classful. Before CIDR notation (`/24`) replaced class-based routing in 1993, the first octet determined how much of an address represented the network and how much represented the host.

Under the **classful addressing** scheme (pre-1993, before CIDR — see [RFC 791](https://www.rfc-editor.org/rfc/rfc791) and later [RFC 1518](https://www.rfc-editor.org/rfc/rfc1518)), IP address space was split into Class A, B, and C networks based on the value of the first octet:

*   **Class A**: first octet = network, remaining 24 bits = host
    
*   **Class B**: first two octets = network, remaining 16 bits = host
    
*   **Class C**: first three octets = network, remaining 8 bits = host
    

If you were an admin sitting on a Class A network like `127.0.0.0/8` (yes, the loopback block is a Class A network) and you wanted to refer to host `1` on that network, typing `127.0.0.1` was needlessly verbose when the system already knew network `127` was 24 bits of host space. So `127.1` became a legitimate, useful shorthand. This is exactly why `127.1` still resolves to `127.0.0.1` today: it's not a coincidence or a bug, it's the shorthand rule working as designed, more than 40 years after classful addressing was formally retired by CIDR in [RFC 1519](https://www.rfc-editor.org/rfc/rfc1519) (1993).

### The BSD parser that never really disappeared

This behavior was implemented in the C standard library function `inet_aton()` ("ASCII to network"), written for **BSD** at UC Berkeley as part of the original Berkeley Sockets API . The same API that essentially every modern operating system's networking stack still descends from. `inet_aton()` deliberately accepted decimal, octal, and hex notation, and the 1/2/3/4-part shorthand, as a single lenient parser. It shipped in BSD Unix, got copied into System V, into Linux's glibc, into Windows' `inet_addr()`, and from there, into essentially every general-purpose IP parser written for the next four decades including the ones inside your browser.

> Surprisingly: **RFC 791**, the 1981 spec that originally defined the Internet Protocol, never actually mandates dotted-decimal text notation at all. It defines the address as 32 bits of binary. Dotted-quad is a documentation and tooling convention that emerged from BBN/ARPANET-era practice, not a formally specified wire format — which is a big part of why so many different, mutually-inconsistent parsers sprang up to read it.

## Who Decides What an IPv4 Address Looks Like?

This is the crux of why the trick still works today: **there is no single authoritative spec for "what counts as a valid IPv4 address string."** Different documents, written decades apart for different purposes, disagree and different pieces of software each pick a different one to implement.

| Spec | Year | What it says about IPv4 text notation |
| --- | --- | --- |
| **RFC 791** – Internet Protocol | 1981 | Defines the address as a 32-bit binary field. Says nothing about text notation at all. |
| **RFC 1123** – Requirements for Internet Hosts | 1989 | Explicitly warns about the ambiguity between a hostname and an IP literal, and says implementations need extra logic to remove ambiguities — an early acknowledgment of exactly this problem. |
| **POSIX.1-2001** | 2001 | Introduced `inet_pton()`, which converts an address from its standard text presentation form. For IPv4, that means four-part dotted-decimal notation and not the octal, hexadecimal, or abbreviated forms accepted by older interfaces such as `inet_aton()`. |
| **RFC 3986** – URI: Generic Syntax | 2005 | Defines an IPv4 address in URI syntax as exactly four decimal octets, each in the range 0–255. It also explicitly acknowledges that many implementations accept other, platform-dependent IPv4 forms through routines such as `gethostbyname()` and `inet_aton()`. |
| **WHATWG URL Standard** | living standard, IPv4 parsing formalized ~2015 | Defines a concrete IPv4 parsing algorithm for URLs that preserves the legacy forms: one to four parts, hexadecimal, and octal. The parser records those non-decimal forms as validation errors but still converts them to an IPv4 address for compatibility. |

Here's the interesting part: **the "official" internet URI standard (RFC 3986) is stricter than what every browser on the planet actually does.**

RFC 3986 says that an IPv4 literal in a URI uses four decimal octets. However browsers don't implement RFC 3986's IPv4 grammar. Instead they implement the **WHATWG URL Standard**, which is a different document maintained by browser vendors, and which explicitly preserves the old BSD leniency because too much of the existing web depended on it by the time anyone tried to standardize it.

And we can see exactly when this became an explicit web-platform standardization problem.

In July 2014, Simon Sapin opened [W3C Bug 26431](https://www.w3.org/Bugs/Public/show_bug.cgi?id=26431), "Define IPv4 parsing." The issue points out that browsers were already encountering "many interesting variations" of IPv4 syntax and asks the specification to define what should be accepted, normalized, or rejected. At the time different browsers and operating-system networking APIs were handling the same strings differently, which could make equivalent URLs look different or non-equivalent URLs appear similar.

The issue was resolved in 2015, when the IPv4 parser was added to the URL specification.

So you end up with a genuinely strange landscape:

*   Strict parsers (`inet_pton`, most modern security-conscious libraries) → **reject** anything but four decimal octets.
    
*   Legacy parsers (`inet_aton`, `getaddrinfo`'s numeric-host path, browser URL parsers) → **accept** the full mess of hex/octal/shorthand.
    
*   Web URL parsers → own standardized behavior through **WHATWG**, which intentionally preserves these legacy forms for web compatibility.
    

Now the problem is not simply that one specification is "right" and another is "wrong." The problem is that the same string can cross multiple software layers, and those layers may apply different parsing rules before they arrive at the same 32-bit destination address.

That's the mismatch we'll keep coming back to throughout this article.

## IPv6: Same Idea, Different Rules

I already knew about this one. IPv6's multiple textual representations are fairly well-known compared with the IPv4 tricks we've just looked at. With IPv6, the ambiguity comes from a different place: representation and compression, not the decimal/octal/hex base-switching we saw with IPv4.

### The compression rules

An IPv6 address is a 128-bit value, normally written as eight 16-bit groups, or "hextets", in hexadecimal, separated by colons. [RFC 4291](https://www.rfc-editor.org/rfc/rfc4291) defines the basic textual representation and allows leading zeros within each 16-bit field to be omitted.

For example:

`2001:0db8:0000:0000:0000:0000:0000:0001`

can be shortened to:

`2001:db8:0:0:0:0:0:1`

and then further compressed to:

`2001:db8::1`

Here are the important rules:

*   **Leading zeros within a 16-bit group are optional.** `0db8` and `db8` represent the same value.
    
*   **A consecutive run of zero groups can be replaced with** `::`**.** RFC 4291 allows `::` to represent one or more consecutive 16-bit zero groups, and it can appear only once in an address.
    
*   **Hexadecimal letters are case-insensitive.** `2001:DB8::1` and `2001:db8::1` are the same address. RFC 5952 later recommends lowercase for canonical representation.
    
*   `::` by itself represents the **unspecified address**, `::/128`. It is not the same as `::1`, which is the **loopback address**. RFC 4291 explicitly defines both representations.
    

That's already a lot of equally-valid spellings for one address before we even get to IPv4.

And this is where IPv6 gets interesting for the rest of our story. The problem appears when different layers normalize, compare, display, or parse those different representations differently.

The real rabbit hole starts when IPv4 gets embedded inside IPv6.

### Where IPv4's tricks reappear inside IPv6

IPv6 has several mechanisms that carry IPv4 information inside a 128-bit IPv6 address. The important distinction is that these mechanisms serve different purposes: some are operating-system representations, some were designed for IPv6 transition, and others are used by translation mechanisms such as NAT64.

*   **IPv4-mapped IPv6 addresses** (`::ffff:a.b.c.d`): used to represent an IPv4 node as an IPv6 address, particularly in dual-stack APIs and sockets.
    
*   **IPv4-compatible IPv6 addresses** (`::a.b.c.d`): an older IPv6 transition mechanism that RFC 4291 later deprecated.
    
*   **NAT64 IPv4-embedded addresses**: the well-known prefix is `64:ff9b::/96`, followed by the embedded IPv4 address. For the `/96` form, the IPv4 address occupies the low-order 32 bits of the IPv6 address.
    

All three are different mechanisms, but they share one interesting property: an IPv4 address can appear inside an IPv6 representation.

Here's the part that's easy to miss. Take the IPv4 address:

```plaintext
169.254.169.254
```

Those four octets are:

```plaintext
169 = 0xA9
254 = 0xFE
169 = 0xA9
254 = 0xFE
```

So the same 32 bits can be represented either as four decimal octets: `169.254.169.254`

or as two 16-bit hexadecimal groups:

```plaintext
a9fe:a9fe
```

Once those 32 bits are placed into an IPv4-mapped IPv6 address, you therefore get two different textual representations of the **same 128-bit IPv6 address**:

```plaintext
::ffff:169.254.169.254
::ffff:a9fe:a9fe
```

They look completely different, but they represent exactly the same address.

You can verify this via `ping`.

```plaintext
   ~ ping ::ffff:169.254.169.254
PING ::ffff:169.254.169.254 (::ffff:169.254.169.254) 56 data bytes

6 packets transmitted, 0 received, 100% packet loss, time 5130ms


   ~ ping ::ffff:a9fe:a9fe
PING ::ffff:a9fe:a9fe (::ffff:169.254.169.254) 56 data bytes

3 packets transmitted, 0 received, 100% packet loss, time 2028ms
```

And that's where this becomes interesting from a security perspective.

Imagine one component does this:

```plaintext
"Does the string contain 169.254.169.254?"
```

while another component parses the address and canonicalizes the same destination as:

```plaintext
::ffff:a9fe:a9fe
```

The two components are now making a security decision based on different textual representations of the same underlying address.

That's the pattern we've been following throughout this article: **the dangerous part isn't that multiple representations exist. The dangerous part is when different layers disagree about how those representations should be interpreted, compared, or normalized.**

## The Actual Parsing Functions

If you're on Linux, you have (at least) three different C-level ways an IP string gets turned into bits, and they behave differently on purpose:

### `inet_aton()` - the legacy, lenient parser

```c
#include <arpa/inet.h>
int inet_aton(const char *cp, struct in_addr *inp);
```

From the Linux man page (`man 3 inet_aton`): accepts the 1/2/3/4-part shorthand and decimal/octal/hex per-part notation described above. This is the function whose behavior we've been describing this whole time. It is considered legacy and its use in new code is generally discouraged specifically because of this leniency.

### `inet_pton()` - the strict, modern parser

```c
#include <arpa/inet.h>
int inet_pton(int af, const char *src, void *dst);
```

It works for IPv4 and IPv6, and only accepts the exact canonical dotted-quad form - four parts, decimal only, each 0–255, no leading zeros. `inet_pton(AF_INET, "45.39.21646", &addr)` **fails**. This is the function you want in any code that makes a security decision based on a string that claims to be an IP address.

### `getaddrinfo()` - what your CLI tools actually call

```c
#include <netdb.h>
int getaddrinfo(const char *node, const char *service,
                 const struct addrinfo *hints, struct addrinfo **res);
```

This is the modern, protocol-agnostic resolution API that tools like `ping`, `curl`, and `ssh` call. Internally, when the string looks purely numeric, glibc's resolver falls back to the **legacy,** `inet_aton`**\-style parsing** for backward compatibility.

Think when you run: `ping 151587081`, will it do DNS resolution?

No, the input can be treated as a numeric IPv4 address rather than a DNS name, and the legacy parser can turn that single integer into its corresponding 32-bit address. No DNS lookup is needed to interpret the number itself. You can easily verify this behavior via **WireShark** or **tcpdump**.

```plaintext
   ~ ping -c1 151587081
PING 151587081 (9.9.9.9) 56(84) bytes of data.
64 bytes from 9.9.9.9: icmp_seq=1 ttl=59 time=111 ms

--- 151587081 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 110.642/110.642/110.642/0.000 ms
```

And this legacy behavior is not just sitting in old code nobody touches anymore. **NetworkManager's** `nm-shared-utils.c` has explicitly documented that it intentionally uses `inet_aton()` in a controlled manner to preserve support for legacy IPv4 address forms that inet\_pton() does not accept.

## Why Does `ping` Works but the Browser Searches?

This is the sharpest version of the question, and it deserves a proper answer because it trips people up constantly:

> Why does `ping 755396366` happily resolve to `45.6.111.14`, but typing `755396366` into a browser's address bar sends it to your **search engine** — while `45.39.21646` gets treated as a **navigable address** in the same browser?

The answer is that a browser's address bar (the "omnibox") does something `ping` never has to do: **it must first guess whether your input is a URL or a search query, before any IP parsing even happens.**

`ping`**'s path require one stage:**

`ping` has no such ambiguity. Its argument is expected to be a hostname or address, so:

```plaintext
ping 755396366
        ↓
getaddrinfo()
        ↓
numeric IPv4 parsing
        ↓
45.6.111.14
```

**The browser's path require two stages:**

1.  **Input classification happens first.** The omnibox has to decide what the user is trying to enter: a URL, a search query, or something ambiguous. Chromium's own source code makes this distinction explicit. If there is no scheme, username, port, or known TLD, a single-word input can be treated as ambiguous because it could be an intranet hostname or simply a search term. Chromium therefore defaults these ambiguous single-word inputs to search. **"localhost"** is an exception. ([Chromium source](https://chromium.googlesource.com/chromium/src/%2B/b9c645c0b167a38b8f93b6c9e9f5a6a2f3e854ae/components/omnibox/browser/autocomplete_input.cc) and [Chromium Omnibox documentation](https://chromium.googlesource.com/playground/chromium-org-site/%2B/master/user-experience/omnibox/index.md))
    
    *   `755396366` → a single numeric token with no scheme or dots → treated as ambiguous input → search is the default behavior.
        
    *   `45.39.21646` → contains dots and looks structurally like a host → can be treated as a URL candidate.
        
2.  **The URL parser then determines what that URL actually means.** Once the input is being handled as a URL, the WHATWG URL parser applies its host-parsing algorithm. That parser still supports the legacy IPv4 forms we've been discussing, including hexadecimal, octal, and 1-, 2-, and 3-part notation. So `45.39.21646` can ultimately be parsed and normalized to `45.39.84.142`. ([WHATWG URL Standard](https://url.spec.whatwg.org/))
    

Firefox had its own interesting location-bar edge case here. [Bugzilla #1154245](https://bugzilla.mozilla.org/show_bug.cgi?id=1154245), reported against Firefox 37, documented that entering `127.0.0.1/1` caused Firefox to perform a search instead of loading `http://127.0.0.1/1`, while `127.0.0.1/x1` loaded normally. The bug specifically affected IP addresses followed by paths containing only numbers. Chromium 37 handled the same URL correctly. Mozilla fixed the issue in **Firefox 40**.

**In the gist:**

The difference isn't the IP parser. It's the extra decision layer in the browser.

`ping` assumes you're giving it a host. The browser's omnibox first has to decide whether your input is a search query or a URL. A bare number like `755396366` is too ambiguous and doesn't look much like a URL, so browser's omnibox treats it as a search by default.

Add a little URL structure:

```plaintext
http://755396366/
```

and the ambiguity disappears. It's now explicitly a URL, so the browser passes it into the WHATWG URL parser, which can interpret the numeric IPv4 form and resolve it to the same 32-bit address.

## Don't `@` Me: URL Obfuscation Through Schema Abuse

Notation games aren't limited to the IP address itself. The surrounding URL syntax offers its own obfuscation surface, and it's worth knowing where the boundary between "this article's topic" and "a different, related trick" actually sits.

### The `userinfo@host` trick

[RFC 3986](https://www.rfc-editor.org/rfc/rfc3986) defines a URL's authority component as `[userinfo@]host[:port]`. Everything before an `@` is credentials (historically used for HTTP basic-auth URLs like `http://user:pass@example.com/`); everything after is the real host. A URL like:

```plaintext
http://google.com@1157586937/
```

visually reads like a trip to `google.com`, but `google.com` is just being sent as a (discarded) username, the actual destination is the decimal-integer IP after the `@`. This has been documented in real phishing/malware infrastructure by Google/Mandiant's threat intelligence team.

It's a URL-structure trick layered *on top of IP notation*, not a variant of it, and stacking the two (as real campaigns have done) compounds the confusion for a human glancing at the link. That's why this belongs in the article: the `@` trick and the IPv4 notation trick are two separate layers of obfuscation that can be stacked together.

## Hands-on, Break It Ourselves

Fire up a terminal. Every command below is safe to run (we're only touching loopback and public DNS resolvers you already trust).

**Confirm the shorthand resolves correctly:**

```bash
# All four of these ping the exact same address (127.0.0.1)
ping -c1 127.0.0.1
ping -c1 127.1
ping -c1 2130706433
ping -c1 0x7f000001
```

**Watch** `curl` **do the same thing:**

```bash
curl -v http://0x08080808/          # decimal-octet hex form of 8.8.8.8
curl -v http://134744072/           # full 32-bit decimal form of 8.8.8.8
```

**Do the conversion yourself in Python 3 (the strict way, using** `ipaddress`**):**

```bash
python3 -c "import ipaddress; print(int(ipaddress.IPv4Address('45.39.84.142')))"
# 757552270

python3 -c "import ipaddress; print(ipaddress.IPv4Address(757552270))"
# 45.39.84.142
```

**Do the conversion the legacy way (using** `socket`**, which mirrors libc's** `inet_aton`**/**`inet_ntoa`**):**

```bash
python3 -c "
import socket, struct
n = 757552270
print(socket.inet_ntoa(struct.pack('!I', n)))
"
# 45.39.84.142
```

**See the strict parser reject what the legacy one accepts:**

```bash
python3 -c "import socket; socket.inet_pton(socket.AF_INET, '45.39.21646')"
# socket.error: illegal IP address string passed to inet_pton
```

**Confirm what** `getent` **(which goes through NSS/**`getaddrinfo`**) does with a numeric string:**

```bash
getent hosts 755396366
```

**Try the IPv4-mapped IPv6 collapse from section: IPv6: Same Idea, Different Rules:**

```bash
python3 -c "
import ipaddress
a = ipaddress.IPv6Address('::ffff:169.254.169.254')
print(a.exploded)     # shows the pure hex-group form
"
# 0000:0000:0000:0000:0000:ffff:a9fe:a9fe
```

**Watch it on the wire.** If you have `tcpdump` available, run this in one terminal:

```bash
sudo tcpdump -n icmp
```

...and `ping -c1 755396366` in another. You'll see the ICMP packets carrying the plain, canonical dotted-quad address (`45.6.111.14`) proof that the obfuscation is purely a *string-parsing* trick. On the wire, in the actual IP header, there has only ever been one 32-bit number. The "weirdness" lives entirely in the human-facing text layer.

## How Do We Defend Against This?

If you build or review anything that makes a security decision based on a string that's supposed to be an IP address, the important rule is simple: don't make that decision on the raw string.

Parse it, validate it, canonicalize it, and make the security decision on the address representation that will actually be used for the connection.

### The principles

1.  **Don't rely on a regex for IPv4 semantics.** A rule such as `\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}` only recognizes one visual shape. It doesn't understand decimal integers, hexadecimal, 1/2/3-part notation, or the different meanings that some parsers assign to leading-zero octets.
    
2.  **Canonicalize before you compare.** Parse the untrusted input into its actual address representation before applying allow-lists, deny-lists, subnet checks, or other access-control rules. *Comparing raw strings means you're comparing formatting, not the destination*.
    
3.  **Prefer strict parsers for security-sensitive input.** In C, `inet_pton()` accepts IPv4 only in dotted-decimal form, unlike `inet_aton()` and `inet_addr()`, which accept the broader legacy numbers-and-dots syntax. Python's `ipaddress` module also rejects leading-zero IPv4 strings in current versions, with the strict behavior introduced in Python 3.9.5 and 3.8.12.
    
4.  **Reject ambiguous leading zeros rather than guessing.** A string such as `012.0.0.1` is exactly the sort of input that can mean different addresses to different parsers. [CVE-2026-69192](https://github.com/beaugunderson/ip-address/security/advisories/GHSA-mwp4-54f8-5fhr) is a current example: the affected `ip-address` library interpreted `012` as decimal `12`, while the WHATWG URL parser, `inet_aton()`, and `getaddrinfo()` interpreted it as octal `10`.
    
5.  **Validate the destination that will actually be connected to.** Don't assume that checking the original hostname or URL string is enough. If possible, resolve hostnames, validate the resulting addresses, account for redirects and DNS rebinding, and ensure the socket ultimately connects to an address that satisfies your policy. The CVE-2026-69192 advisory explicitly calls out this distinction.
    
6.  **Normalize IPv4-mapped IPv6 addresses too.** A filter that understands `::ffff:169.254.169.254` but doesn't recognize the equivalent `::ffff:a9fe:a9fe` can still be bypassed. [CVE-2026-33975](https://github.com/twentyhq/twenty/security/advisories/GHSA-vrcj-hv2q-c58m) is a real 2026 example of exactly that normalization mismatch in an SSRF protection layer.
    

The general rule is:

```plaintext
untrusted string
       ↓
     parse
       ↓
canonical address
       ↓
security policy
       ↓
connect to that validated destination
```

The important part is that the **parser used for the security decision and the representation used for the connection cannot disagree about the destination**.

## One Last Ping

This blog obviously doesn't cover every corner of it. There is a lot more to explore, especially when you start asking how these representations show up in real network traffic, whether we can reliably capture them on the wire, and what changes when HTTPS gets involved. It's not impossible, but it's not as straightforward either. **I'll leave that part to you for now. Let me know if you wanna go down that rabbit hole.**

For me, though, the craziest part is still the original trick.

The fact that I can type:

```text
ping 1280054549
```

and have it actually reach:

```text
76.76.21.21
```

still feels slightly ridiculous.

Thanks for coming along with me on this journey. And if you spot something in my research that I've got wrong, misunderstood, or could have explained better, **feel-free to call it out. I'll genuinely be happy to learn something new.**