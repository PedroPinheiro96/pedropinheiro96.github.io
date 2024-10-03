---
title: "Pi-Hole Installation and Configuration"
date: 2024-10-02 00:00:00 +0100
categories: [RaspberryPi]
tags: [Ad Blocker, Raspberry Pi, Network, Pi-Hole]
image: 
  path: /assets/images/PiHole-Configuration-Installation/header.png
---

In today's digital landscape, online ads can be more than just an annoyance; they can slow down your browsing experience and invade your privacy. Enter Pi-hole, a powerful network-wide ad blocker that transforms your home network into a cleaner, faster space. By acting as a DNS sinkhole, Pi-hole intercepts and blocks unwanted content before it reaches your devices.

In this post, we'll guide you through the straightforward process of installing and configuring Pi-hole. Whether you're a tech novice or a seasoned pro, this step-by-step tutorial will help you reclaim your online experience and enjoy a more streamlined internet. Let’s dive in!

### Prerequisites

Before we get started, ensure that you have the following:
- A Raspberry Pi (any model will do, but Raspberry Pi 3 or later is recommended)
- An SD card (at least 8GB)
- A power supply for your Raspberry Pi

>I have IPv6 disabled on my network so we'll only be configuring Pi-Hole for an IPv4 network.
{: .prompt-info }

## Step 1: OS Installation

First, download the Raspberry Pi Imager from the official [Raspberry Pi website](https://downloads.Raspberrypi.org/imager/imager_latest.exe).

1. Select your Model: Choose your Raspberry Pi model from the list.
![RaspberryPiImager](assets/images/PiHole-Configuration-Installation/RaspberryPiImager.png)

2. Choose the OS: Select Raspberry Pi OS Lite.
![RaspberryPiImager](assets/images/PiHole-Configuration-Installation/RaspberryPiImager-1.png)

3. Edit Settings: Click on `Next`, then `Edit Settings`:
![RaspberryPiImager](assets/images/PiHole-Configuration-Installation/RaspberryPiImager-2.png)

4. In the `General` tab, set the `hostname` to Raspberrypi and enter your desired `username` and `password`:
![RaspberryPiImage-General](assets/images/PiHole-Configuration-Installation/RaspberryPiImager-3.png)

5. Enable SSH.<br>
![RaspberryPiImager](assets/images/PiHole-Configuration-Installation/RaspberryPiImager-4.png)

6. Write to SD Card: Click `Save` and wait for the OS to be written to the SD card.

7. Power Up: Once finished, insert the SD card into the Raspberry Pi and power it on.

## Step 2: SSH configuration

1. Connect to the Raspberry PI via SSH.

2. Create the a group for SSH users:
``` bash
sudo groupadd ssh-users
```

3. Add your current user to the `ssh-users` group. This will later be used to restrict who can SSH.
``` bash
sudo usermod -a -G ssh-users $USER
```

4. Set Up Public Key Authentication. 
- Generate a key pair on your local machine and transfer the public key to the Raspberry Pi. To do this from a Windows machine:
``` bash
ssh-keygen -t ed25519 -a 777
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh user@PiIPaddress "cat >> .ssh/authorized_keys"
```

5. In the SSH session, add the following to `/etc/ssh/sshd_config` to disable password authentication, root login and enable public key authentication:
``` bash
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
UsePAM no
X11Forwarding no
AllowGroups ssh-users
```

6. Restart the SSH service:
``` bash
sudo systemctl restart ssh
```

>Password authentication is now disabled, and SSH is only possible using the private key that was created in the earlier step.
{: .prompt-info }

## Step 3: Network Configuration

Now, we need to assign a static IP address to the Raspberry Pi. The steps will vary depending on your router.

1. Log into your router's admin page.

2. Look for the static IP address configuration page.

3. Set the Raspberry Pi's IP address to a static one.
![StaticIP](assets/images/PiHole-Configuration-Installation/RouterStaticIP.png)

## Step 4: Install Pi-Hole

1. To install Pi-Hole, run the following command:
```bash
sudo curl -sSL https://install.pi-hole.net | bash
```
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation.png)

2. Press `Enter` to continue.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-2.png)

3. Press `Enter` again as the static IP address has already been configured.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-3.png)

4. Select the Internet Interface.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-4.png)

5. Select the desired Pi-Hole Upstream Provider.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-5.png)

6. Select `Yes` to include the default blocklists.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-6.png)

7. Select `Yes` to enable the Admin Web Interface.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-7.png)

8. Select `Yes` to enable the Web Server.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-8.png)

9. Select `Yes` to enable Log Query for later analysis and tuning.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-9.png)

10. Set the Privacy Mode and click `Continue`.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-10.png)
You can learn more about the different privacy levels here: <https://docs.pi-hole.net/ftldns/privacylevels/>.<br><br>
Pi-Hole is now installed!
![PIInstallation](assets/images/PiHole-Configuration-Installation/PIInstallation-11.png)

11. To change the admin page password, run:
```bash
pihole -a -p
```

12. To access the admin page, go to `http://RaspberrypiIP/admin`.
![PIInstallation](assets/images/PiHole-Configuration-Installation/PiHomepage.png)<br><br>

## Step 5: Set the Raspberry PI as the Router's DNS Server.

Now, we need to make the Pi-Hole the default DNS server for our network.
There are various methods for doing this, but I've chosen to configure the router to use it as the DNS server.

1. Log in to the router admin page. 

2. Navigate to your router's DNS settings page, and enter the Raspberry Pi's IP in the primary and secondary DNS IP fields. 
If your router does not allow you to configure the same IP for both the primary and secondary DNS server, leave the secondary field empty.
![DNSServer](assets/images/PiHole-Configuration-Installation/DNSRouter.png)

## Step 6: Pi-Hole Hardening

### UFW

1. Start by installing Uncomplicated Firewall (UFW).
```bash
sudo apt install ufw
```

2. Set UFW's default policies (Deny In, Allow Out).
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

3. Open the SSH port to the local network only.
```bash
sudo ufw allow from 192.168.0.0/16 to any port 22 proto tcp
```

4. Open Pi-Hole's Web Dashboard (Port 80 by default) to the local network only.
```bash
sudo ufw allow from 192.168.0.0/16 to any port 80 proto tcp
```

5. Open Pi-Hole's DNS (Port 53) to the local network only.
```bash
sudo ufw allow from 192.168.0.0/16 to any port 53 proto tcp
sudo ufw allow from 192.168.0.0/16 to any port 53 proto udp
```

6. Open Pi-Hole's DHCP server (IPV4: 67).
Note: Only do this if you are using Pi-Hole's DHCP server.
```bash
sudo ufw allow from 0.0.0.0 to any port 67 proto udp
```

7. Open Port 4711 for Pi-Hole's FTL (from localhost only).
```bash
sudo ufw allow from 127.0.0.0/8 to any port 4711 proto tcp
```

8. Enable/reload UFW.
```bash
sudo ufw enable
```
or
```bash
sudo ufw reload
```

9. To view current rules:
```bash
 sudo ufw status verbose
```
or
```bash
  sudo ufw status numbered
```

### Disable Wireless Devices

If you are providing network connectivity to your Raspberry Pi via an ethernet cable, you should consider disabling unused wireless functionality.

1. Execute the following commands in the terminal:
``` bash
sudo nano /boot/firmware/config.txt
```

2. Add the following lines to the file:
``` bash
[all]
# Disable wireless services
dtoverlay=disable-wifi
dtoverlay=disable-bt
```

3. Exit and reboot your Pi:
``` bash
sudo reboot
```

### Enable Automatic Security Updates

> It's important to note that while automating updates is important, they can cause issues at some point. New bugs or incompatibilities can be introduced that break software.
{: .prompt-warning }

1. To install unattended-upgrades on your system, run:
```bash
sudo apt-get install unattended-upgrades
```

2. Once you’ve installed unattended-upgrades, test that everything is working using a dry run. This checks for software that can be updated but stops short of actually downloading and installing any updates.
To trigger a dry run, run the following:
```bash
sudo unattended-upgrade -d -v --dry-run
```

3. Lastly, to enable unattended-upgrades so it automatically runs on your system, run:
```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

4. Click on `Yes` when you’re presented with the following screen:
![UnattendedUpgrades](assets/images/PiHole-Configuration-Installation/UnattendedUpgrades.jpeg)

## Step 7: Extra block lists

1. Log in to the Pi-Hole interface.

2. Click on `Adlists`.
![Blocklist](assets/images/PiHole-Configuration-Installation/Blocklist.png)

3. Enter the following addresses one by one and press `Add`.
```
https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
https://raw.githubusercontent.com/uBlockOrigin/uAssets/master/filters/quick-fixes.txt
https://raw.githubusercontent.com/uBlockOrigin/uAssets/master/filters/badware.txt
https://raw.githubusercontent.com/uBlockOrigin/uAssets/master/filters/unbreak.txt
https://raw.githubusercontent.com/uBlockOrigin/uAssets/master/filters/privacy.txt
https://easylist.to/easylist/easyprivacy.txt
https://github.com/liamengland1/mischosts/blob/master/social/tiktok-regex.list
https://pgl.yoyo.org/adservers/serverlist.php?hostformat=adblockplus&mimetype=plaintext
https://raw.githubusercontent.com/AdguardTeam/AdguardFilters/master/MobileFilter/sections/adservers.txt
https://raw.githubusercontent.com/AdguardTeam/AdguardFilters/master/MobileFilter/sections/general_elemhide.txt
```

## Testing

To test if the Pi-Hole is working as expected, we can use this tool: [AdBlock tester](https://d3ward.github.io/toolz/adblock)

With the Pi-Hole disabled, 65 ads were blocked:

![Before-Pi-Hole](assets/images/PiHole-Configuration-Installation/BeforePiHole.png){: width="300" height="450"}

With the Pi-Hole enabled, 133 ads were blocked:

![After-Pi-Hole](assets/images/PiHole-Configuration-Installation/AfterPiHole.png){: width="300" height="450"}

Thank you for following this guide. Your ad-free experience starts now!