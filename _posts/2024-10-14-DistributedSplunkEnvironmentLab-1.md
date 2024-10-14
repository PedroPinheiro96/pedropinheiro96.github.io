---
title: "Distributed Splunk Environment Lab: Part 1 - VirtualBox/Splunk Installation and Configuration"
date: 2024-10-14 19:45:00 +0100
categories: [Splunk, DistributedEnvironmentLab]
tags: [splunk, linux, lab, virtualbox]
image:  
  path: assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkHeader.png
---

## Lab Overview

In this tutorial, we will walk through the process of setting up a Splunk environment using VirtualBox on a Windows host. We will cover the installation of VirtualBox, the network configuration, and the setup of multiple Splunk servers along with one administration machine. You’ll learn how to download and install Ubuntu and Splunk, and configure SSH public key authentication to enable seamless connections from the administration machine to each Splunk server. Let’s get started!

## Pre Requisites

A license is needed to configure a distributed Splunk environment.

Request a Splunk developer [license here](https://dev.splunk.com/enterprise/dev_license/).

## Windows Configuration

### Enable Virtualisation

To run VirtualBox, ensure that virtualisation is enabled on your system.

1. Open Task Manager.

2. Navigate to the `Performance` tab.

3. Check that `Virtualisation` is marked as `Enabled`.
![TaskManager-Virtualisation](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/TaskManager-Virtualisation.png)

4. If it is not enabled, check [here](https://www.ninjaone.com/blog/how-to-enable-cpu-virtualization-in-your-computer-bios/) or consult your motherboard manual for instructions on enabling virtualisation in your BIOS.

### Disable Memory Integrity 

1. Press the Windows key, type Security and open it.
![Windows-Security](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/Windows-Security.png)

2. Navigate to `Device Security`and click on `Core isolation details`.
![Windows-CoreIsolation](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/Windows-CoreIsolation.png)

3. Disable `Memory Integrity`.
![Windows-MemoryIntegrity](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/Windows-MemoryIntegrity.png)

### Download and Install VC++ 2019 Redistributable

Download the VC++ 2019 Redistributable from [Microsoft](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170).

Select the appropriate architecture and download the file.
![VC-Architecture](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VC-Architecture.png)

Run the executable, agree to the terms, and click `Install`.


## Install VirtualBox

1. Download [VirtualBox](https://www.virtualbox.org/wiki/Downloads).
<br>![VirtualBox-Download](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBox-Download.png)

2. Locate and run the installer.

3. Click `Next`.
<br>![VirtualBoxInstallation](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation.png)

4. Accept the terms and click `Next`.
    <br>![VirtualBoxInstallation-Terms](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Terms.png)

5. Keep the default settings or change the install location, then click `Next`.
<br>![VirtualBoxInstallation-Default](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Default.png)

6. Click `Yes`, to install the network interfaces.
![VirtualBoxInstallation-Network](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Network.png)

7. Click `Yes`, to install missing dependencies.
<br>![VirtualBoxInstallation-Dependencies](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Dependencies.png)

8. Click `Next`.
<br>![VirtualBoxInstallation-Shortcut](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Shortcut.png)

9. Click `Install`.
<br>![VirtualBoxInstallation-Install](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Install.png)

10. Finally, click `Finish` to start VirtualBox. 
![VirtualBoxInstallation-Installed](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxInstallation-Installed.png)

## Configure the VirtualBox Network Adapter

1. Click on `Tools` -> `Networks`.

2. Click on `Create` and select `Yes` to allow Windows to make changes to the device.
![VirtualBox-CreateNetworkAdapter](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBox-CreateNetworkAdapter.png)

3. Right-click on the newly created adapter and select `Properties`.

4. Under the `Adapter` tab, select `Configure Adapter Manually` and configure as follows:
>- IPv4 Address: 10.0.5.1
>- IPv4 Network Mask: 255.255.255.0
![NetworkAdapter-Configurations](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/NetworkAdapter-Configurations.png)

5. Click `Apply` to save your changes.

![NetworkAdapter-Overview](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/NetworkAdapter-Overview.png)

## Create and Configure the Virtual Machines

We will start by creating two virtual machines: 
- One for Splunk (SearchHead1)
- One for administration (optional;You can SSH from your Windows machine.)

### Create the Splunk Virtual Machine (SearchHead1)

1. Click `New`.

2. Set the following under `Name and Operating System`:
>Name: SearchHead1<br>Folder: Select your folder<br>ISO Image: Select the Ubuntu file.<br>Select `Skip Unattended Installation`
![SplunkServer-Config](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkServer-Config.png)

3. Under `Hardware`, configure:
>Base Memory: 1024 MB<br>Processors: 1
![SplunkServer-Hardware](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkServer-Hardware.png)

4. Click `Finish`.

5. Right-click on the VM and select `Settings`.

6. Select `Network`, click on `Adapter 2`, and enable it.<br>Select the one we created earlier.
![EnableAdapter2](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/EnableAdapter2.png)

7. Boot the machine.

8. Click `Next`.
![SplunkVM-Language](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-Language.png)

9. Click `Next`.
![SplunkVM-Accessibility](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-Accessibility.png)

10. Select your keyboard layout and click `Next`.
![SplunkVM-KeyboardLayout](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-KeyboardLayout.png)

11. Select how you are connected to the internet.
![SplunkVM-InternetConnection](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-InternetConnection.png)

12. Select `Install Ubuntu` and click `Next`.
![SplunkVM-InstallUbuntu](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-InstallUbuntu.png)

13. Leave the default `Interactive installation` and click `Next`.
![SplunkVM-InteractiveInstallation](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-InteractiveInstallation.png)

14. Click `Next`.
![SplunkVM-DefaultApps](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-DefaultApps.png)

15. If not using Wi-FI, do not select anything, and click `Next`.
![SplunkVM-Software](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-Software.png)

16. Leave the default `Erase disk and install Ubuntu`.
![SplunkVM-EraseDisk](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-EraseDisk.png)

17. Create your account and unselect `Require my password to log in`. Click `Next`.
![SplunkVM-CreateAccount](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-CreateAccount.png)

18. Select your location and click `Next`.
![SplunkVM-Location](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-Location.png)

19. Review the settings and click `Install.`
![SplunkVM-Review](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-Review.png)

20. Wait for the installation to finish.
![SplunkVM-Install](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-Install.png)

### Create the Administration Virtual Machine

The administration machine is where we will primarily work and use to SSH Into each each Splunk server.

1. Click `New`.

2. Set the following under `Name and Operating System`:
>Name: Ubuntu<br>Folder: Select your folder<br>ISO Image: Select the Ubuntu file.<br>Select `Skip Unattended Installation`
![MainMachine-Config](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/MainMachine-Config.png)

3. Under `Hardware`, configure:
>Base Memory: 4096 MB<br>Processors: 2
![MainMachine-Hardware](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/MainMachine-Hardware.png)

4. Click `Finish`.

5. Boot the machine.

6. Follow the same process as earlier until you reach the `Create Account` menu.

7. You can set your username and password here.
![MainMachine-CreateAccount](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/MainMachine-CreateAccount.png)

### Clone and Configure Splunk Servers :

We will need eight splunk servers:

|Server|IP|
|-----|-----|
|SH1|10.0.5.201|
|IND1|10.0.5.202|
|IND2|10.0.5.203|
|IND3|10.0.5.204|
|DS|10.0.5.205|
|CM|10.0.5.206|
|HF|10.0.5.207|
|UF|10.0.5.208|

To speed up the process of creating virtual machines, clone SH1 for each additional Splunk server.

1. Right-click on the `SearchHead1` VM and select `Clone`.<br>
![SplunkServer-Clone](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkServer-Clone.png)

2. Name it `Indexer1`, choose the directory, and select `Generate new MAC addresses for all network adapters`.<br>
![SplunkServer-Clone-Configuration](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkServer-Clone-Configuration.png)

3. Click on `Finish`.

4. Repeat steps 1-3 until you have created all necessary Splunk VMs.

#### Server Name and IP Configuration

Follow these steps on each server:

1. Upgrade the machines.
```bash
sudo apt-get update && sudo apt-get upgrade
```

2. Set the hostname:
```bash
sudo hostnametctl set-hostname "Server"
```
![SplunkVM-SetHostname](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-SetHostname.png)


3. Confirm if the hostname has been updated.
```bash
hostnamectl
```
![SplunkVM-ShowHostname](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-ShowHostname.png)

4. Edit `50-cloud-init.yaml`:
```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

    Update `IP` according to the server you are configuring and save the file:

    ```bash
    # This file is generated from information provided by the datasource.  Changes
    # to it will not persist across an instance reboot.  To disable cloud-init's
    # network configuration capabilities, write a file
    # /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
    # network: {config: disabled}
    network:
        ethernets:
            enp0s3:
                dhcp4: true
            enp0s8:
                dhcp4: true
                addresses: [10.0.5.<"IP"/24]
        version: 2
    ```
![SplunkVM-StaticIP](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkVM-StaticIP.png)

4. Reboot the machine.

#### SSH Configuration

1. Install the OpenSSH server package on each server:
```bash
sudo apt install openssh-server
```

2. On the administration machine:

    1. Ping `SH1` to check connectivity.
    ![MainMachine-SH1Ping](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/MainMachine-SH1Ping.png)
    
    2. Create an SSH key pair:
    ```bash
    cd ~/.ssh/
    ssh-keygen -t ed25519 -a 777 -f SplunkLab
    ```
    ![MainMachine-Keygen](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/MainMachine-Keygen.png)

    3. Transfer the public key to each server:
    ```bash
    ssh-copy-id -i SplunkLab.pub splunk@10.0.5.<<IP>>
    ```
    ![SSHCopy](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SSHCopy.png)

3. Create the `ssh-users` group and add the `splunk` user to it:
```bash
sudo groupadd ssh-users
sudo usermod -a -G ssh-users $USER
``` 

5. Edit the SSH server configuration:
```bash
sudo nano /etc/ssh/sshd_config
```

6. Update it with the following:

    ```plaintext
    # This is the sshd server system-wide configuration file. See sshd_config(5) for more information.

    # This sshd was compiled with PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

    # The strategy used for options in the default sshd_config shipped with
    # OpenSSH is to specify options with their default value where
    # possible, but leave them commented.  Uncommented options override the
    # default value.

    Include /etc/ssh/sshd_config.d/*.conf

    #Port 22
    #AddressFamily any
    #ListenAddress 0.0.0.0
    #ListenAddress ::

    #HostKey /etc/ssh/ssh_host_rsa_key
    #HostKey /etc/ssh/ssh_host_ecdsa_key
    #HostKey /etc/ssh/ssh_host_ed25519_key

    # Ciphers and keying
    #RekeyLimit default none

    # Logging
    #SyslogFacility AUTH
    #LogLevel INFO

    # Authentication:

    #LoginGraceTime 2m
    PermitRootLogin no
    #StrictModes yes
    #MaxAuthTries 6
    #MaxSessions 10

    PubkeyAuthentication yes

    # Expect .ssh/authorized_keys2 to be disregarded by default in future.
    #AuthorizedKeysFile	.ssh/authorized_keys .ssh/authorized_keys2

    #AuthorizedPrincipalsFile none

    #AuthorizedKeysCommand none
    #AuthorizedKeysCommandUser nobody

    # For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
    #HostbasedAuthentication no
    # Change to yes if you don't trust ~/.ssh/known_hosts for
    # HostbasedAuthentication
    #IgnoreUserKnownHosts no
    # Don't read the user's ~/.rhosts and ~/.shosts files
    #IgnoreRhosts yes

    # To disable tunneled clear text passwords, change to no here!
    PasswordAuthentication no
    #PermitEmptyPasswords no

    # Change to yes to enable challenge-response passwords (beware issues with
    # some PAM modules and threads)
    KbdInteractiveAuthentication no

    # Kerberos options
    #KerberosAuthentication no
    #KerberosOrLocalPasswd yes
    #KerberosTicketCleanup yes
    #KerberosGetAFSToken no

    # GSSAPI options
    #GSSAPIAuthentication no
    #GSSAPICleanupCredentials yes
    #GSSAPIStrictAcceptorCheck yes
    #GSSAPIKeyExchange no

    # Set this to 'yes' to enable PAM authentication, account processing,
    # and session processing. If this is enabled, PAM authentication will
    # be allowed through the KbdInteractiveAuthentication and
    # PasswordAuthentication.  Depending on your PAM configuration,
    # PAM authentication via KbdInteractiveAuthentication may bypass
    # the setting of "PermitRootLogin prohibit-password".
    # If you just want the PAM account and session checks to run without
    # PAM authentication, then enable this but set PasswordAuthentication
    # and KbdInteractiveAuthentication to 'no'.
    UsePAM no

    #AllowAgentForwarding yes
    #AllowTcpForwarding yes
    #GatewayPorts no
    X11Forwarding no
    #X11DisplayOffset 10
    #X11UseLocalhost yes
    #PermitTTY yes
    PrintMotd no
    #PrintLastLog yes
    #TCPKeepAlive yes
    #PermitUserEnvironment no
    #Compression delayed
    #ClientAliveInterval 0
    #ClientAliveCountMax 3
    #UseDNS no
    #PidFile /run/sshd.pid
    #MaxStartups 10:30:100
    #PermitTunnel no
    #ChrootDirectory none
    #VersionAddendum none
    AllowGroups ssh-users

    # no default banner path
    #Banner none

    # Allow client to pass locale environment variables
    AcceptEnv LANG LC_*

    # override default of no subsystems
    Subsystem	sftp	/usr/lib/openssh/sftp-server

    # Example of overriding settings on a per-user basis
    #Match User anoncvs
    #	X11Forwarding no
    #	AllowTcpForwarding no
    #	PermitTTY no
    #	ForceCommand cvs server
    ```

7. Restart the SSH service:
```bash
sudo systemctl restart ssh
```

8. Test the new configuration by establishing an SSH connection to each server from the administration Machine:
```bash
ssh splunk@10.0.5."<IP>">
```
![SSHToSplunkServers](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SSHToSplunkServers.png)

9. If the SSH connection was successful, everything has been configured correctly.
>Note: Enter `y` if prompted to add the new host.

### Administration Machine: Guest Additions

Install Guest Additions to be able to enable the shared clipboard and other features.
> Note: Find out more about it here: ["VirtualBox Guest Additions"](https://www.virtualbox.org/manual/ch04.html#guestadd-intro).

Follow the steps bellow after booting up the machine.

1. Run the following command to install the necessary packages:
```bash
sudo apt install build-essential dkms linux-headers-$(uname -r)
```

2. In the VirtualBox GUI, click on `Devices` -> `Insert Guest Additions CD image`.
![GuestAdditions-Insert](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/GuestAdditions-Insert.png)

3. Open a terminal prompt and navigate to the directory of where the CD drive is mounted:
```bash
cd /media/"user"/VBOx_GAs_7.1.2
```

4. Run the Guest Additions Installer:
```bash
sudo ./VBoxLinuxAdditions.run
```

5. Reboot the machine and enable the shared clipboard if needed.
![MainMachine-SharedClipboard](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/MainMachine-SharedClipboard.png)

### VirtualBox Overview

Group your VMs in VirtualBox for better organization.

1. Select the indexers, right-click, and choose `Move to group` -> `New`.
![VirtualBox-Group](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBox-Group.png)

2. Name it `Indexers`.

3. Group search heads as Search Heads and the cluster manager and deployment server as Managers.

4. Move the forwarder into the Forwarder group.

5. Lastly, select the 3 groups and group them as `Distributed Splunk Environment`.

Your VirtualBox homepage should look like this:

![VirtualBoxOverview](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBoxOverview.png)

### Splunk Download and Installation

Now that SSH is configured on all the servers, we can use the terminal in our administration Machine to install and configure Splunk.

1. SSH into each machine.

2. Download [Splunk](https://www.splunk.com/en_us/download/splunk-enterprise.html):
```bash
wget -O splunk-9.3.1-0b8d769cb912-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/9.3.1/linux/splunk-9.3.1-0b8d769cb912-Linux-x86_64.tgz"
```
![SplunkDownload](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkDownload.png)

3. Install Splunk:
```bash
sudo tar -xf splunk-9.3.1-0b8d769cb912-Linux-x86_64.tgz -C /opt/
```

4. Change ownership of `/opt/splunk` and its directories and files:
```bash
sudo chown -R splunk:splunk /opt/splunk
```
![SplunkOwnership](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkOwnership.png)

5. Start Splunk.
```bash
/opt/splunk/bin/splunk start --accept-license
```

6. Set the admin username and password:
```bash
user: admin
pass: splunkLab
```
![SplunkUserPass](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkUserPass.png)

7. Wait for Splunk to start.
![SplunStart](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkStart.png)

8. Set up Splunk to run on startup::
```bash
sudo /opt/splunk/bin/splunk enable boot-start -user splunk
```
![BootStart](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/BootStart.png)

9. Set the Splunk server name:
```bash
/opt/splunk/bin/splunk set servername <<server>>
```
![SplunkServerName](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkServerName.png)

### Firewall Installation and Configuration

1. Download UFW if you don't already have it.
```bash
sudo apt install ufw
```

2. Add the following rules:
```bash
sudo ufw allow 8000 # Splunk webpage
sudo ufw allow 9997 # Listening port
sudo ufw allow 8089 # Used by Splunkd to communicate with other Splunk instances.
sudo ufw allow 8191 # KVStore
sudo ufw allow 8080 # Indexer Replication
sudo ufw allow 22 # SSH
```

3. Enable the firewall.
```bash
sudo ufw enable
```

4. Confirm if the rules have been added.
```bash
sudo ufw status verbose
```
![UFW-Rules](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/UFW-Rules.png)

### Splunk Homepage

1. Open your browser and go to `http:"server IP":8000` to access each server's Splunk webpage. If any of them fails to connect, check each step again for misconfigurations.
![SplunkWebPage](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkWebPage.png)

## Extra Configuration

If you see the green turtle icon when you boot a VM, follow the steps in [this guide](https://learn.microsoft.com/en-us/troubleshoot/windows-client/application-management/virtualization-apps-not-work-with-hyper-v).

When properly configured, the green turle should disappear, and you should see a `V` icon:
![VirtualBox-Icon](assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/VirtualBox-Icon.png)

## Conclusion

In this post, we have:
- Enabled Windows virtualization.
- Downloaded and Installed VirtualBox.
- Configured a `Host-Only` network adapter.
- Created Ubuntu VMs - 12 Splunk VMs and 1 Administration VM.
- Configured SSH and public key authentication.
- Configured firewall rules.
- Downloaded and Installed Splunk.
- Installed Guest Additions on the administration Machine.
- Set the server name of each Splunk server.

In the next post, we'll install the license, connect the different Splunk servers together, and create a distributed environment.