---
title: "cURL An Alternative To Many Applications. Here Is How?"
datePublished: Mon Jul 11 2022 06:33:41 GMT+0000 (Coordinated Universal Time)
cuid: cl5gdc82r0at3nunvd5aa47pm
slug: curl-an-alternative-to-many-applications-here-is-how
canonical: https://flarexes.com/curl-an-alternative-to-many-applications-here-is-how
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1657520550106/98bsljhMo.png
tags: curl, linux, terminal, cli, linux-for-beginners

---

The world is very dependent on browsers for even doing simple tasks. File sharing, checking public IP addresses, weather forecast, internet speed tests and other stuff that we IT forks do sometimes like - using neofetch, cheatsheets and DNS checks etc, are not really suckless ways to use. Because why you wanna download thousands of lines of code just to check system configuration (neofetch), this is bloated. So, today we'll see a suckless way to achieve this whole stuff from terminal using **cURL**. Let's go ahead and do some less bloated IT ninja stuff.

# Public IP Address

Getting a current network public IP address. Sometimes seems important or we just wanna know. So, don't need open the browser. Just try

```bash
curl http://ifconfig.co/ip
```

This will simply return your public IP on stdout.

## Get Reboust Info From Public IP Address

Curious about what possibly website see just from your IP address? If yes, I gotch you. Type...

```bash
curl -s http://ip-api.com/json?fields=66846719
```

Or, If you want any specific information. Use below command

```bash
curl -s http://ip-api.com/json?fields=continent,continentCode,country,countryCode,region,lon,currency,isp
```

You can add more comma-separated fields to specifically get what you want.

**Possible Fields**

* status
    
* message
    
* continent
    
* continentCode
    
* country
    
* countryCode
    
* region
    
* regionName
    
* city
    
* district
    
* zip
    
* lat
    
* lon
    
* timezone
    
* offset
    
* currency
    
* isp
    
* org
    
* as
    
* asname
    
* reverse
    
* mobile
    
* proxy
    
* hosting
    
* query
    

### Json Fix?

All the above robust commands will return a JSON representation which needs to be formed to look good. So, for that, we need a tool, `jq`. You can install it depending on your platform.

**For Debian/Ubuntu Based OS**

```bash
sudo apt install jq
```

**For Arch Based OS**

```bash
sudo pacman -Sy jq
```

It's also available for another operating system [check here](https://stedolan.github.io/jq/download/).

So, let's print the public info in proper JSON format.

```bash
curl -s http://ip-api.com/json?fields=66846719 | jq
```

This will output something like that

```JSON
{
  "query": "24.48.0.1",
  "status": "success",
  "continent": "North America",
  "continentCode": "NA",
  "country": "Canada",
  "countryCode": "CA",
  "region": "QC",
  "regionName": "Quebec",
  "city": "Montreal",
  "zip": "H1K",
  "lat": 45.6085,
  "lon": -73.5493,
  "timezone": "America/Toronto",
  "isp": "Le Groupe Videotron Ltee",
  "org": "Videotron Ltee",
  "as": "AS5769 Videotron Telecom Ltee",
  "asname": "VIDEOTRON",
  "reverse": "modemcable001.0-48-24.mc.videotron.ca",
  "proxy": false
}
```

# Get DNS provider

```bash
curl -L http://edns.ip-api.com/json | jq
```

This will return your DNS provider and DNS server IP address.

# Quick URL Shorten

```bash
curl -F url=<link> https://shorta.link
```

Replace `<link>` with the actual URL. This will return a shorted URL. Something like that.

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $curl -F url=https://google.com https://shorta.link

https://shorta.link/SQeLX4_d
```

You can also pass slug as a parameter (slug refers to a random string at the end of shorted URL).

```bash
curl -F url=<link> -F slug=<any string> https://shorta.link
```

For Example

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $curl -F url=https://flarexes.com -F slug=flarexes https://shorta.link

https://shorta.link/flarexes
```

# Better Way To *File Transfer*

This one is my favourite even though I actually wrote an article on that, [File sharing from terminal which suck less](https://flarexes.com/file-sharing-via-curl). Let's see, How does it work?

Working is very simple we gonna use [0x0.st](https://0x0.st). This site accepts the location of a file that you wanna share and then return a shareable link.

```bash
curl -F 'file=@<file location>' https://0x0.st
```

It would be like this

```txt
┌─[✗]─[radowoo@SecOS]─[~]
└──╼ $curl -F 'file=@/home/radowoo/Desktop/test' https://0x0.st

https://0x0.st/o1oR.txt
```

This is the best way. I ever found to share the files. Don't forget to check out the [File Sharing Via cURL](https://flarexes.com/file-sharing-via-curl) for more info.

# Cheatsheets

A very simple and quick way to see a cheatsheet about many commands. For that we gonna use [cheat.sh](http://cheat.sh/).

The only cheat sheet you need Unified access to the best community-driven documentation repositories in the world.

```bash
curl cheat.sh/<any command>
```

Let's have a glance

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $curl cheat.sh/file

# file
# Determine file type.
# More information: <https://manned.org/file>.

# Give a description of the type of the specified file. Works fine for files with no file extension:
file filename

# Look inside a zipped file and determine the file type(s) inside:
file -z foo.zip

# Allow the file to work with special or device files:
file -s filename

# Don't stop at the first file type match; keep going until the end of the file:
file -k filename

# Determine the mime encoding type of a file:
file -i filename
```

Simple sort and sweet. Their home page is also very cool and informative. Have a look!

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $curl cheat.sh
      _                _         _    __                                        
  ___| |__   ___  __ _| |_   ___| |__ \ \      The only cheat sheet you need    
 / __| '_ \ / _ \/ _` | __| / __| '_ \ \ \     Unified access to the best       
| (__| | | |  __/ (_| | |_ _\__ \ | | |/ /     community driven documentation   
 \___|_| |_|\___|\__,_|\__(_)___/_| |_/_/      repositories of the world        
                                                                                
+------------------------+ +------------------------+ +------------------------+
| $ curl cheat.sh/ls     | | $ cht.sh btrfs         | | $ cht.sh lua/:learn    |
| $ curl cht.sh/btrfs    | | $ cht.sh tar~list      | | Learn any* programming |
| $ curl cht.sh/tar~list | |                        | | language not leaving   |
| $ curl https://cht.sh  | |                        | | your shell             |
|                        | |                        | | *) any of 60           |
|                        | |                        | |                        |
+-- queries with curl ---+ +- own optional client --+ +- learn, learn, learn! -+
+------------------------+ +------------------------+ +------------------------+
| $ cht.sh go/f<tab><tab>| | $ cht.sh --shell       | | $ cht.sh go zip lists  |
| go/for   go/func       | | cht.sh> help           | | Ask any question using |
| $ cht.sh go/for        | | ...                    | | cht.sh or curl cht.sh: |
| ...                    | |                        | | /go/zip+lists          |
|                        | |                        | | (use /,+ when curling) |
|                        | |                        | |                        |
+---- TAB-completion ----+ +-- interactive shell ---+ +- programming questions-+
+------------------------+ +------------------------+ +------------------------+
| $ curl cht.sh/:help    | | $ vim prg.py           | | $ time curl cht.sh/    |
| see /:help and /:intro | | ...                    | | ...                    |
| for usage information  | | zip lists _            | | real    0m0.075s       |
| and README.md on GitHub| | <leader>KK             | |                        |
| for the details        | |             *awesome*  | |                        |
|            *start here*| |                        | |                        |
+--- self-documented ----+ +- queries from editor! -+ +---- instant answers ---+
                                                                                
[Follow @igor_chubin for updates][github.com/chubin/cheat.sh]
```

# Random Jokes

Sometimes, We just want that. Sometimes 🤔

```bash
curl https://icanhazdadjoke.com
```

That's set...

# Text To QR Code

I don't know how many of you will do that. But yaa! we have an option for that. You can generate a QR code which will consist of text or string? And, Of course, this works perfect on the terminal.

```bash
curl qrenco.de/this_is_my_text
```

You can also share multiple lines like that

```bash
printf "two\nlines" | curl -F-=\<- qrenco.de
```

But, The way I prefer is to read text from a file

```bash
cat myfile.sh | curl -F-=\<- qrenco.de
```

# Weather In Console

Stop using an application to check the weather. That ask for location. I don't want that. We have a better way or the right way to <s>check</s> curl the weather!

Just do...

```bash
curl wttr.in/<your location>

# Example `curl wttr.in/country+reagion+city`
```

No permission granting required

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $curl wttr.in/USA+Boston

Weather report: USA+Boston

     \  /       Partly cloudy
   _ /"".-.     21 °C          
     \_(   ).   ↑ 22 km/h      
     /(___(__)  16 km          
                0.0 mm         

Location: Boston, Suffolk County, Massachusetts, United States [42.3554334,-71.060511]

Follow @igor_chubin for wttr.in updates
```

[wttr.in](https://wttr.in) provides you more control over how you wanna see the weather information for that check out their [GitHub Repo](https://github.com/chubin/wttr.in).

# Execute Script Without Downloading Them

Have you ever used neofetch? Or Did you ever try to test your internet speed? If yes! Then, stop doing that because you can achieve these simple tasks from the command line via cURL.

But, You may think. Why would I encourage you not to download neofetch? Because I'm not a big fan of tools that just gives some system info using an 11k line of code. Yes, that's true. I think we can get this information via creating our own script but If you still want that 11k line of codes, then use them without downloading. Here is how.

```plaintext
curl -sL https://raw.githubusercontent.com/dylanaraps/neofetch/master/neofetch | bash
```

Above command will simply fetch the neofetch script from GitHub temporarily and then execute it from bash. And, This will be removed when it's done.

But, There are several awesome utilities on GitHub. That you may wanna use some time just like *Internet Speed Test*. For that, just do

```plaintext
curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python -
```

# Make Thing Simpler

All the above commands are not supposed to be typed every time you use them. Instead, these all should be aliased into a `.zshrc` or `.bashrc` files. So, I did that for you.

```bash
# Returns Public IP Address
alias myip='curl -4 http://ifconfig.co/ip'

# Returns Public Information In JSON
alias ipinfo='curl -s http://ip-api.com/json?fields=66846719 | jq'

# Returns Current DNS Info In Json
alias mydns='curl -sL http://edns.ip-api.com/json | jq'

# Returns Shorted URL
alias shorturl='function _e(){ curl -F "url=$1" https://shorta.link; };_e'

# Returns Shorted URL With Specified Slug
alias slugurl='function _e(){ curl -F "url=$1" -F "slug=$2" https://shorta.link; };_e'

# Share File
# Check out File Sharing Via cURL (https://flarexes.com/file-sharing-via-curl) for more better alias to share files
alias fshare='function _e(){ curl -F "file=@$1" https://0x0.st; };_e'

# Returns Cheatsheet Of Specified Command
alias cheat='function _e(){ curl cheat.sh/$1; };_e'

# Random Jokes That Wouldn't Make You Laugh
alias joke='curl https://icanhazdadjoke.com'

# Text To QR Code
alias tqr='function _e(){ printf "$1" | curl -F-=\<- qrenco.de; };_e'

# See Current Weather
alias weather='curl wttr.in/USA+London'

# Useful Scripts
alias neofetch='function _e(){ curl -sL https://raw.githubusercontent.com/dylanaraps/neofetch/master/neofetch | bash; };_e'
alias netspeed='function _e(){ curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python3 -; };_e'
```

> **Note:** Many of these services store your data to work properly ( Who Knows? ) except a few ones.

Want more websites like that! Then check out [Awesome Console Services](https://github.com/chubin/awesome-console-services) GitHub repository which maintains such services. So, that's set for now. Good Bye!