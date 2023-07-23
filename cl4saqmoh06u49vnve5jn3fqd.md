---
title: "File Sharing Using cURL"
seoTitle: "Share Files Using cURL"
seoDescription: "THE NULL POINTER allows you to create an HTTP POST request to send a file very quickly from terminal via cURL."
datePublished: Fri Jun 24 2022 10:14:26 GMT+0000 (Coordinated Universal Time)
cuid: cl4saqmoh06u49vnve5jn3fqd
slug: file-sharing-using-curl
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1656067573080/H46nhwNv0.jpg
tags: curl, terminal, cli, fzf, fileshare

---

These days, sharing photographs, videos, and document files is a pretty routine task. If you don't want to use any Google or Microsoft accounts, the procedure can occasionally get very frustrating. Then the only option is to use a website that offers this feature without requiring a login, such as [WeTransfer](https://wetransfer.com/).

But what if you want to share something more quickly or from a terminal then we can use a very interesting and mind-blowing website [THE NULL POINTER](https://0x0.st/). This site allows you to create an HTTP POST request to send a file very quickly. I just have to do

```bash
curl -F 'file=@yourfile.png' https://0x0.st
```

And, this will return a shareable link in the terminal (fast and easy enough).

The full source code is accessible as a [Git Repo](https://git.0x0.st/mia/0x0) If you'd like to run a server and website like this, it is amazing.

## Installing Required Dependencies

cURL comes pre-installed in most Linux distributions but in case you didn't have it installed.

Install on **Ubuntu / Debian**

```plaintext
sudo apt install curl fzf
```

Install on **Arch OS**

```plaintext
sudo pacman -Sy curl fzf
```

## Create A File Sharing CLI Utility 'fshare'

```bash
curl -sS -F "file=@$(find $HOME | fzf)" https://0x0.st | xclip -selection clipboard && xclip -selection clipboard -o
```

In the command above, you simply upload a file to [THE NULL POINTER](https://0x0.st/), copy the shareable link to the clipboard, and then stdout the link on the terminal.

**Explanation**

* `-sS` show any info on stdout only if any error occurred
    
* `-F` allow uploading a file on [THE NULL POINTER](https://0x0.st/)
    
* `"file=@$(find $HOME | fzf)"` grab file location using `fzf`
    
* `xclip -selection clipboard` copy sharable link to clipboard
    
* `xclip -selection clipboard -o` finally also stdout on terminal
    

Now, we can simply create an alias out of this by simply pasting in .zshrc or .bashrc

```bash
alias fshare='curl -sS -F "file=@$(find $HOME | fzf)" https://0x0.st | xclip -selection clipboard && xclip -selection clipboard -o'
```

Rather than an alias, I usually prefer to use a standalone bash script and making it executable. I have **fshare** bash script on my GitHub ([Check Here](https://github.com/FlareXes/Micro-Utils/blob/main/bin/fshare)). Where you can always get updated version of **fshare** there. So to install it run below commands.

```bash
sudo wget https://raw.githubusercontent.com/FlareXes/Micro-Utils/main/bin/fshare -O /usr/local/bin/fshare

sudo chmod +x /usr/local/bin/fshare

fshare
```

But, [THE NULL POINTER](https://0x0.st/) is a free service which means it comes with restrictions some of which are listed below.

0x0.st is not a platform for:

* piracy
    
* extremist material of any kind
    
* malware / botnet C&C
    
* anything related to crypto currencies
    
* tor exit nodes not allowed
    
* any file types:
    
    * application/x-dosexec
        
    * application/x-executable
        
    * application/x-hdf5
        
    * application/java-archive
        
    * android apks and system images
        

Check out their website for more information.

If you don't like these restrictions then spin up your own server because it is open-source and available as a git repo and don't spam on them.