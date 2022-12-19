# Automating Two-Factor Authentication with pass-OTP and xdotool

If you spend a lot of time in front of a computer and prefer to use a keyboard over a mobile phone, you may have encountered the hassle of TOTP (Time-based one-time password) authentication. Entering the TOTP code from your mobile phone manually everytime you need to authenticate can be a hassle. Yes, We can get an autofill TOTP functionality from services like `1password`, and `Bitwarden` but they all are paid. Most 2FA service providers require you to share your email address and phone number, which may be a concern for some people who value their privacy. This is bit of a concern for me too. Hmm! So, I decided to automate this problem using my bash skills.

![demo passrofi](https://cdn.hashnode.com/res/hashnode/image/upload/v1671350533746/nsTponRcr.gif align="center")

Before we deep dive, If you aren't using any sort of **2FA** to secure your online accounts, then it's bad not using 2FA leaves your accounts vulnerable to unauthorized access. So just start using one. I like to use **Authy** before I wrote this utility. `Authy` is the only trusted player in the TOTP 2FA market which provide support for desktop application including Linux, Windows and macOS. While Google and Microsoft also offer 2FA solutions, their support for desktop applications is more limited compared to Authy.

> ***Don't Waste Time:*** If you've already set up `pass-otp` or using it then straight jump into the [Automating Pass OTP](#automating-pass-otp) section.

# What is a Time-based one-time password (TOTP)
Ya! good question simply say, It is a much better and more secure way to handle your account OTPs. `Time-based` mean, in every 30 seconds your new OTP will be generated. And, This works offline too.

Watch this video to know more about **why your 2-Factor-Authentication method is NOT secure!!**

%[https://odysee.com/@NaomiBrockwell:4/most-secure-2fa:7]

and this video has shown **how you can setup 2FA Authenticator WITHOUT Scanning a QR Code!**

%[https://odysee.com/@AllThingsSecured:9/how-to-setup-2fa-authenticator-without:f]

# Passrofi, The Best 2FA Authenticator
## Advantages
1. Don't require **E-Mail Address** or **Phone Number**
2. No copy-paste is required. It auto-fills the OTP
3. GPG encrypted that means better security and privacy

## Disadvantages
1. Suitable for Linux
2. Initial setup is hassle

## Installation of requirements.txt
- pass
- pass-otp
- zbar
- git
- gpg

**Arch-based OS**
```
sudo pacman -S pass pass-otp zbar git
```

**Debian-based OS**
```
sudo apt install pass pass-otp zbar-tools git
```

## Let's first setup pass-otp normally
1. Create a GPG keypair
```
$ gpg2 --full-generate-key
```

Select option 1 (RSA and RSA) for the key type.

```
Please select what kind of key you want:

   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)

Your selection? 1
```

Now, choose 4096-bit key size and specify key expiry. I'll go with `0` that means 'never expires'.
```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits

Please specify how long the key should be valid.

        0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years

Key is valid for? (0) 0
Is this correct? (y/N) y
```

Enter a name, e-mail address and then confirm with 'O'. Don't worry about real information. Just enter something meaningful.
```
GnuPG needs to construct a user ID to identify your key.

Real name: FlareXes
Email address: test@gmail.com
Comment: 
You selected this USER-ID:
    "FlareXes <test@gmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

Lastly, you'll be prompted for a password to encrypt your TOTP credentials so choose wisely.

This was one time process. In future, we will export and store them in a safe place may be in the cloud in an encrypted format. Then we can use these keys by importing them on a different computer.

2. Initiate pass database
Initialize pass database by providing the GPG keypair name or e-mail address.

You can see the name and email address used while creating GPG keypair by listing your secret keys. Check in `uid` section.
```
$ gpg --list-secret-keys

/home/flarexes/.gnupg/pubring.kbx

--------------------------------

sec   rsa2048 2019-04-25 [SC]
DEB0D4AD41CC2612B1944D448D22D6610B2F6067
uid           [ultimate] FlareXes <test@gmail.com>
ssb   rsa2048 2019-04-25 [E]
```

So, now I have an email to create pass database. Let's create one by
```
$ pass init test@gmail.com
```

That's all!

## How to add TOTP codes in pass-otp?
1. To set up 2FA using a QR code, you have to save a picture of the QR code or take a screenshot of it. If you are unable to save the QR code, you can take a screenshot. In Firefox, you can take a screenshot by right-clicking on the page and selecting 'Take a Screenshot' from the context menu.

2. Once you have the screenshot, you can add it to pass. It is always good to check if your screenshot is working. For that
```
$ zbarimg -q --raw screenshot.png

otpauth://totp/totp-secret?secret=AAAAAAAAAAAAAAAA&issuer=totp-secret
```
If you see something like a URL then you're good to go.

3. Finally, Add it pass-otp
```
$ zbarimg -q --raw screenshot.png | pass otp insert twitter
```

4. To see TOTP generated by pass-otp type
```
pass otp twitter
```
this should show your otp

# Automating pass-otp
After adding all your accounts to `pass-otp` you want to open the terminal and type `pass otp <account name>` again and again.
So, ya! it's time we are going to automate this stuff. 

## **If you're a `rofi` guy just like me**
First, Install `rofi`
```
sudo pacman -S rofi
```

1. Set your environment variable `LANG` to "en_US.UTF-8". Otherwise, it won't work properly.
```bash
export LANG="en_US.UTF-8"
```

2. Extract all the account names you have registered into `pass-otp` till now.
```bash
entries=$(ls ~/.password-store | cut --delimiter="." --fields=1)
```

3. Select your account name
```bash
selected_entry=$(echo "$entries" | rofi -dmenu -p "OTP: ")
```

4. Retrieve the otp for the selected entry from the `pass-otp`.
```bash
otp=$(pass otp "$selected_entry")
```

5. Type the otp into the active window.
```bash
xdotool type --window getactivewindow "$otp"
```

## **If you're a `dmenu` guy**
First, Install `dmenu`
```
sudo pacman -S dmenu
```
For `dmenu` whole script will be same except **step 3**. Replace step 3 with the below line.
```bash
selected_entry=$(echo "$entries" | dmenu -p "OTP: ")
```
Else would be the same.

***Complete script for rofi***

```bash
#!/bin/sh

# Set the language to US English to ensure that rofi and xdotool
# display and type the password correctly.
export LANG="en_US.UTF-8"

# List the files in the password-store and extract the names of entries
# without the .gpg extension.
entries=$(ls ~/.password-store | cut --delimiter="." --fields=1)

# Use rofi to display a menu of the otp entries, and allow the user to
# choose one of the entries.
selected_entry=$(echo "$entries" | rofi -dmenu -p "OTP: ")

# Retrieve the otp for the selected entry.
otp=$(pass otp "$selected_entry")

# Copy the retrieved otp on clipboard.
echo -n "$otp" | xclip -selection clipboard

# Type the otp into the active window on the system using xdotool.
xdotool type --window getactivewindow "$otp"
```

Save it as `passrofi` under `/usr/local/bin`, then give `passrofi` executable permission.

```
$ sudo chmod +x /usr/local/bin/passrofi
```

You can always find an updated version of `passrofi` on my GitHub [@FlareXes](https://github.com/FlareXes/Micro-Utils/blob/main/bin/passrofi).

## Binding keyboard shortcut with `passrofi`
I use `sxhkdrc` to bind my keyboard shortcuts. So, open the `sxhkdrc` file in a text editor and paste the below lines

```text
# Pass-Otp
alt + shift + l
  /usr/local/bin/passrofi
```

![demo passrofi](https://cdn.hashnode.com/res/hashnode/image/upload/v1671350533746/nsTponRcr.gif align="center")

# Backup And Sync Stored OTPs And GPG Keys
For backup, we will use `git` and `GitHub`. So, create a *private repository* at your GitHub account for example `passotp-backup`. Then, copy the ssh url and add remote.

1. Initiate git repo
```
$ pass git init
```

2. Add remote
```
$ pass git remote add origin git@github.com:<github account>/passotp-backup.git
```

3. Push to GitHub every time you add a new OTP
```
$ pass git push -u origin master
```
or 
```
$ pass git push
```

You also need to backup your GPG keys. And store them somewhere safe. For that, you need to export them.

Export keys with specific email.
```
gpg --export --export-options backup --output public.gpg test@gmail.com
```

```
gpg --export-secret-keys --export-options backup --output private.gpg test@gmail.com
```

```
gpg --export-ownertrust > trust.gpg
```

## Sync with different computer
Move all the exported keys to a different computer. Afterthat, import them one by one.

```
gpg --import public.gpg
```

```
gpg --import private.gpg
```

```
gpg --import-ownertrust trust.gpg
```

We can check everything has been imported properly by using the --list-secret-keys option.

```
gpg --list-secret-keys
```

Now, Importing OTPs from GitHub.

```
pass git init
```

```
pass git remote add origin git@github.com:<github account>/pass-otp.git
```

```
pass git pull
```

That's it now you're good to go.

# Conclusion
`Pass` and `Pass Otp` are two amazing utilities that are only used by very few people around the world. Those few people include privacy and security conscious individuals or computer nerds. But, this `passrofi` thing can be addictive once you start using it and get the game. If you are still stuck and want to see a proper walkthrough then I got you covered, I'll also created a video version of this blog on my youtube channel [FlareXes](https://youtube.com/FlareXes_) very soon, so that stay tuned.

If you find this process hard to set up then no problem you can watch step-by-step guide on YouTube. But remember, we should always use better 2fa systems like TOTP or Security Keys like Yubico. I recommend `Authy` due to its flexibility but `Authy` is closed-source and ask for personal information. From now I'll gonna stick with `passrofi`. A better and suckless way to handle 2FA.

Thanks for hanging around!
