---
title: "Cached vs Non-Cached DNS: A Lazy Experiment That Got Interesting"
datePublished: 2026-04-11T03:11:19.851Z
cuid: cmntrd0kp00yd29ka09kz6b2c
slug: cached-vs-non-cached-dns-a-lazy-experiment-that-got-interesting
cover: https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/8c676c1f-ad8f-4f2e-9544-2b06727d4ee8.png
tags: dns, python, networking

---

I was bored and just wanted to code something to spark the life, of course.

And, to fight it, my brain thought, ***"how fast really is my DNS provider?"***

![](https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExMHBkYWdoMHZzcXU3NzdsZDc3am80eTk0eHRtajhpanc5ZnN2aWNueSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/8Zh3Wd7KZEWrnh0g1y/giphy.gif align="center")

No architecture diagram. No benchmarking framework. No “this is how DNS testing should be done.” In fact, I didn’t even check how DNS benchmarking is *usually* done in the real world.

So, I casually started coding and end up with two scripts, and honestly, the results were way more interesting than I expected.

Before you even read further, I’d actually recommend:

👉 Go through the scripts  
👉 Run them yourself  
👉 Then come back

Repo: [https://github.com/FlareXes/fastest-dns](https://github.com/FlareXes/fastest-dns)

Because, the results depend a lot on *your* network, your routing, your ISP… everything.

## The Idea

The idea was simple:

**Take a few popular public DNS resolvers:**

*   Cloudflare (1.1.1.1)
    
*   Google (8.8.8.8)
    
*   Quad9 (9.9.9.9)
    

**Test them in two scenarios:**

*   Cached DNS
    
*   Non-cached DNS
    

**And measure:**

*   Average latency
    
*   Median latency
    
*   P95 latency
    

No fancy tooling. Just Python + `dig`.

## 🛑 **Check Before You Proceed (So,** Don’t Skip)

Before you get excited and hit run, a few things that can completely ruin your results:

> **First - Make Sure Nothing Is Overriding Your DNS**

If you’re using tools like: VPNs, Custom DNS clients like Portmaster, or OS-level DNS overrides.

Then there’s a good chance your DNS queries are being intercepted or rerouted.

Even if your script says:

```shell
dig @1.1.1.1 example.com
```

Your system *might still ignore that* and use something else. If that happens, your results are basically fake. You’ll think you’re benchmarking Cloudflare but you’re not.

> **Second - Don’t Abuse Public DNS Servers (Seriously)**

Don’t go wild with queries. These are public DNS services. Don’t hammer them with insane request rates.

The script already behaves nicely:

```python
time.sleep(random.uniform(0.5, 1.0))
```

Keep it reasonable. Respect the service.

> **Third - You Can Tweak Everything**

This isn’t a rigid benchmark. It’s more like a playground. You can change pretty much everything:

```python
SERVERS = {
    "Cloudflare": "1.1.1.1",
    "Google": "8.8.8.8",
    "Quad9": "9.9.9.9"
}

RUNS = 20
```

Want to:

*   Add your ISP’s DNS? Go ahead.
    
*   Increase runs to 100? Sure.
    
*   Change delays between queries? Do it.
    
*   Swap domains? Totally fine.
    

This is meant to be explored, not followed blindly.

## **Simple Script — Talking to DNS**

Both scripts revolve around one simple idea, run `dig`, extract the query time.

Here’s the core piece:

```python
def query_dns(server, domain):
    result = subprocess.run(
        ["dig", f"@{server}", domain],
        capture_output=True,
        text=True
    )
    match = re.search(r"Query time: (\d+)", result.stdout)
    return int(match.group(1)) if match else None
```

Second, I tracked Metrics that actually matters

1.  **Average (Looks Useful, Lies Sometimes)**  
    Add everything and divide by number of runs
    
    But here’s the problem:  
    If one or two queries spike hard (which happens in DNS), they can pull the average up significantly. So you might think: “This DNS is slow”.
    
    When actually: 90% of queries were fast and 2 were just unlucky.
    
2.  **Median (What You Actually Experience Most of the Time)**  
    It ignores extreme spikes. So if you want to know, what users usually experience how stable the resolver is.
    
3.  **P95 (Worst Case)**  
    It tells you: “What do the slowest 5% of queries look like?”
    

## **Phase One — Cached DNS**

I started with cached DNS.

The idea here is simple: query the same domain repeatedly and let the resolver cache do its job.

Here’s the key part of the script :

```python
# Warm-up queries
for _ in range(3):
    query_dns(ip, DOMAIN)
```

### **Why Warm-Up Matters**

This primes the cache.

Without it:

*   First few queries would be slower (cold start)
    
*   Results would be inconsistent
    

After warm-up, every query hits cached data.

## **Second Phase — Non-Cached DNS**

You cannot force public DNS resolvers to skip cache. So, I forced DNS resolver to miss the cache by generates a new long domain every time.

```python
def random_domain():
    name = ''.join(random.choices(string.ascii_lowercase + string.digits, k=12))
    return f"{name}.com"
```

*   These domains are almost guaranteed to not exist
    
*   no cached answer exists
    
*   The resolver has to do full DNS resolution
    

It’s a bit hacky but it works surprisingly well.

Now the resolver has to actually *do its job*:

1.  Ask root servers
    
2.  Ask `.com` TLD servers
    
3.  Ask authoritative servers
    

Each step adds: Network hops, Latency, Variability.

One thing I almost ignored, I added random delays between queries to:

*   prevent artificial bursts
    
*   avoid hammering servers
    

```python
time.sleep(random.uniform(0.5, 1.0))
```

## **Final Thoughts**

I don't any for this one, thanks for the reading.