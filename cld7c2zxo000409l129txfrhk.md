# How Hackers Automatically Spoof MAC Address ?

There are plenty of reasons to spoof a ***Media Access Control*** address or MAC address. If you are in a monitored environment. It will be difficult to identify you with random MAC address. If somebody blocked you from connecting to WiFi. Then maybe they have used MAC whitelisting or blacklisting. Which can be bypassed if you spoof the MAC address. And, of course, randomizing the MAC address of the network interface can provide you some security and privacy benefits. It is not a foolproof solution but it's a solution. Which is easy to set up. Everyone should spoof their MAC address whenever possible it does not make sense that every device you ever connected needs to remember that for a long. In **Windows**, you can use something like **Technitium**, a free utility to spoof MAC address instantly. I'll go with **GNU/Linux**. So, Let's straight jump into the ***SYSTEMD*** 🫢

# Building blocks
In short, ***Systemd*** is a service manager that runs in the background. It has some amazing capabilities like starting and stopping daemons. Systemd uses ***Units***, a file which includes all the instructions that systemd requires.

If you want to know more about ***Systemd***, then DigitalOcean has an amazing article on [Understanding Systemd Units and Unit Files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files) you should really check this out.

## Pre-requisite
First, we need a utility called `macchanger` which actually changes the MAC address. Then we will automate that using systems units.

**Arch OS**
```
sudo pacman -S macchanger
```

**Debian/Ubuntu**
```
sudo apt install macchanger
```

Change MAC address
```
macchanger -e <interface>
```

Replace `<interface>` with your network interface. e.g. `macchanger -e wlan0`. You can see your network interface and MAC address with `ifconfig`.

# Auto spoof MAC address on every reboot
1. Create a unit file `/etc/systemd/system/macspoof@.service`.

2. Paste these lines in `/etc/systemd/system/macspoof@.service`.

    ```systemd
    [Unit]
    Description=macchanger on %I
    Wants=network-pre.target
    Before=network-pre.target
    BindsTo=sys-subsystem-net-devices-%i.device
    After=sys-subsystem-net-devices-%i.device

    [Service]
    ExecStart=/usr/bin/macchanger -e %I
    Type=oneshot

    [Install]
    WantedBy=multi-user.target
    ```

    > **Note:** Be aware of case-sensitive while writing units.

3. Enable the service by appending the network interface to the service. For example, I want to change my `wlan0` mac address on every boot.
    
    ```
    sudo systemctl enable macspoof@wlan0.service
    ```

4. Restart your system to changes take effect or do `sudo systemctl start macspoof@wlan0.service`

Done, now keep reading If you want to know what is happening in this unit file.

## Breaking down the unit file from `step 2`
In summary, this is a systemd service unit file template that can create multiple instances designed to change or spoof the MAC address on every system boot using a GNU utility called `macchanger`.

1. `[Unit]` section contains basic information about the unit file and its relations with other units.

2. `Description=macchanger on %I` is a short description of the unit file where `%I` will be replaced with the instance name you specified while enabling the service. Like `macspoof@wlan0.service` here `%I` will be replaced with `wlan0`.

3. `Wants=network-pre.target` specifies that the service should be started after the *network-pre.target* unit. *network-pre.target* unit indicates that the basic network interface configuration is completed and the network is up.

4. `Before=network-pre.target` specifies that the service should be started before the *network-pre.target* unit.

    > ***Note -*** The combination of "Wants" and "Before" options ensures that the service is started as soon as possible after the network is up and running.
    >
    > This makes sure the MAC address is randomized before the DHCP client requests an IP address and before connecting to the internet.

    `Step 3` and `Step 4` are optional but more focused on increasing your privacy and security.

5. `BindsTo=sys-subsystem-net-devices-%i.device` and `After=sys-subsystem-net-devices-%i.device` specifies a dependency and are used to ensure that the service executed only after the network interface is fully initialized and available. `%i` is the same as `%I` but with an escaped instance name.

    I kinda didn't get `%i` vs `%I` but that's what the documentation says. Let me know if you know the difference.

6. `[Service]` section includes the command to execute. Under which User and Group it should run etc.

7. `ExecStart=/usr/bin/macchanger -e %I` specifies the command to change the MAC address. `-e` means change only the last three octets. This option is useful when you want to change the MAC address but not the OUI (organizationally unique identifier) of the manufacturer who built your system. You can use `-r` instead of `-e` to completely change the entire MAC address.

8. `Type=oneshot` specifies that the service is short-lived and should exit once it is complete.

9. `[Install]` specifies how and when service should be installed.

10. `WantedBy=multi-user.target` specifies the service should start at `multi-user.target` level which means multiple users can interact with the system.

That's all, Now you know what exactly this unit file is doing.

Not to mention, There are plenty of other ways you can spoof MAC address. If you don't like this method, arch wiki has some good stuff you can check [MAC address spoofing](https://wiki.archlinux.org/title/MAC_address_spoofing). I like this method because I just need to copy and paste a few lines then enable the service and never worry about it again.

# Summary
Randomizing the MAC address of the network interface has some security and privacy benefits. But sometimes it is tedious to do it manually. So we automated that using Systemd. Some people like Systemd and some don't. But it provide multiple purposes to use it. I hope you have learned something about systemd, MAC spoofing and unit files. The above script is taken from arch wiki so check out Arch Wiki to know more about [MAC address spoofing](https://wiki.archlinux.org/title/MAC_address_spoofing) stuff. 

So, that is it for today. Keep annoying your network administrator. And, Thanks for reading or listening!

\- BYE
