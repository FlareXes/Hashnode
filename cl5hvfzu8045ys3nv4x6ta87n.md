## Parrot OS Weird Docker Installation Issues

Parrot OS is a privacy-focused penetration testing operating system. Which comes bundled with many pentesting or hacking tools. But, sometimes we need to install something else like `Docker`. Which prompts a wired issue. Something like that.

```txt
E: The repository 'https://download.docker.com/linux/debian ara Release' does not have a Release file.
N: Updating from such a repository can't be done securely and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```
If you are getting such an error, Then this is probably an issue of `VERSION_CODENAME`. So, Let's resolve this now!

# Docker Installation On Parrot OS

**Step 1:** Update the package index and install required dependencies.

```bash
sudo apt update
```

```bash
sudo apt install ca-certificates curl gnupg lsb-release
```

**Step 2:** Add Docker Official GPG Key.

```bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

> **Note:** It's always recommended to perform *Step 2* from the Docker office site because sometimes (rarely) gets changed. [Check from here](https://docs.docker.com/engine/install/debian/)

**Step 3:** Setup The Docker Repository (The Problem ☠️)

```bash
echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Now, If you try to update your system now, it wouldn't work. And you have an error like below.

```txt
E: The repository 'https://download.docker.com/linux/debian ara Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```

**Step 4:** Resolve The Issues

Open The File `/etc/apt/sources.list.d/docker.list`.

```bash
sudo vim /etc/apt/sources.list.d/docker.list
```

And, replace `ara` (Second last word) with `bullseye`. Something like that.

```txt
deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bullseye stable
```

`bullseye` is the codename for `Debian 11`. If you're using any other version of Debian like 10 or 9 then use their codename `Buster` or `Stretch` respectively.

**Step 5:** Install Docker Engine

Now, the Error may have gone. So we can install Docker. Before that update the apt package index.

```bash
sudo apt update
```

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

> Receiving a GPG error when running `apt-get update`?
>
> Then Run the following command, `sudo chmod a+r /etc/apt/keyrings/docker.gpg`

That's all. Docker is installed on your system.

# In Short Docker Installation
```bash
sudo apt update

sudo apt install ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Now, Open `/etc/apt/sources.list.d/docker.list` and replace `ara` with `bullseye` (For Debian 11).

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $cat /etc/apt/sources.list.d/docker.list

# Replacing
# deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian ara stable
# With

deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bullseye stable
```

Then, just update and install.

```bash
sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

# Issues Explanation
If you look at the **Step 3** command closely. Then you'll see that it's actually trying to get your Operating System codename using `lsb_release -cs`. A Codename refers to the nickname of the current version of your OS. In this case of Parrot OS, you'll see that it's `ara` because Parrot isn't a Debian Operating System it is based on Debian 11. And, the Docker installation guide is about `Debian OS`, not Debian-based OS. So, you have to struggle a bit.

# One More Example, ***LibraWolf Web-browser***
[LibreWolf](https://librewolf.net/) is a fork of Firefox, focused on privacy, security and freedom (One Of My Favourite Browser). Here you'll also see the same issues. I'm showing you that because application syntax varies for each case. For example, LibraWolf uses the `if-else` condition to find your OS version or codename. Let's see how it works.

**Step 1:** Find distro codename and stdout.
```bash
distro=$(if echo " bullseye focal impish jammy uma una " | grep -q " $(lsb_release -sc) "; then echo $(lsb_release -sc); else echo focal; fi)
echo $distro
```
If you run the above command on Parrot OS will return `ara`. Which is wrong, it should be `bullseye`. For that revise **Step 1** again.

**Step 1 (revised):** Add distro codename manually.
```bash
export distro="bullseye"
```

**Step 2:** All Set, add and install the LibraWolf accordingly.
```bash
echo "deb [arch=amd64] http://deb.librewolf.net $distro main" | sudo tee /etc/apt/sources.list.d/librewolf.list

sudo wget https://deb.librewolf.net/keyring.gpg -O /etc/apt/trusted.gpg.d/librewolf.gpg

sudo apt update

sudo apt install librewolf -y
```

So, these tiny issues can happen and waste a few hours of your life. In the past, this did waste my time. Hopefully, didn't happen to you (waste of time 😅). That's it for today. Programming always helps. Goodbye!