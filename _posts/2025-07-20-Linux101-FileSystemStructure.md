---
title: "Linux 101: File System Structure"
date: 2025-07-20 10:00:00 +0100
categories: [Linux]
tags: [linux]
image: 
  path: assets/images/Linux101/FileSystemStructure/Linux101-FileSystemStructure.png
---

When you first explore Linux, one of the most important things to grasp is its **file system hierarchy**. Unlike Windows, which uses separate drives like `C:\` or `D:\`, Linux organizes everything under a single, unified directory tree starting at `/`, known as the **root directory**.

Let’s take a guided tour of the core Linux directories and what roles they play in the system.

---

## Exploring the Major Directories

### `/` – The Root of It All

The root directory, represented simply as `/`, is the starting point of the entire file system. Every file, folder, and device is nested somewhere underneath it. It’s the anchor of Linux’s structure — no matter what device or partition you’re working with, it all connects here.

---

### `/bin`

This directory holds essential user command-line tools like `ls`, `cp`, `mv`, `rm`, and `echo`. These are the basic commands needed for a functioning user environment, and they’re available to all users, even in single-user mode or during system recovery. Without `/bin`, you wouldn’t be able to interact with the system at even the most basic level.

---

### `/boot`

Here you’ll find everything the system needs to boot up. That includes the Linux kernel, initial RAM disk images, and bootloader files such as GRUB configuration files. This directory is critical to the system's startup process — damage or misconfiguration here can prevent the machine from starting at all.

---

### `/dev`

The `/dev` directory is where Linux stores device files. These aren’t actual files in the traditional sense, but rather interface points for hardware devices like hard drives (`/dev/sda`), partitions (`/dev/sda1`), USB sticks, and input devices. Linux treats devices as files, allowing programs and scripts to interact with them easily through standard file operations.

---

### `/etc`

This is the heart of system-wide configuration. Every major piece of software that runs on your system — from the network stack to the display manager — likely has a configuration file somewhere in `/etc`. You'll find files like `/etc/passwd` for user account details, `/etc/hosts` for hostname lookups, and configuration folders for services like Apache, SSH, and cron.

---

### `/home`

User accounts each get their own directory inside `/home`, such as `/home/pedro`. This is your personal space where your documents, downloads, desktop settings, and application data live. Users typically have full control over their own `/home` directory, making it the primary place you'll work from in day-to-day use.

---

### `/lib` and `/lib64`

These directories contain shared libraries — code that programs can use to avoid reinventing the wheel. For example, if several programs need to print text to the screen, they might all use the same shared library. `/lib` contains libraries for essential system binaries, while `/lib64` holds the 64-bit versions. These libraries are vital for the execution of most binaries in `/bin` and `/sbin`.

---

### `/media` and `/mnt`

These are mount points — places in the file system where other file systems (like USB drives, SD cards, or external hard drives) are attached. `/media` is often used for automatically mounted devices, especially on desktop systems, while `/mnt` is commonly used for temporary manual mounts by system administrators. When you plug in a USB stick, it usually appears under `/media/username/device-name`.

---

### `/opt`

Software that doesn’t come with the standard Linux distribution — especially third-party or commercial software — often gets installed here. For example, if you download a proprietary application from a vendor, it may live in `/opt/appname/`. This keeps custom software isolated from the rest of the system and makes uninstallation or updates easier.

---

### `/proc`

This is a virtual file system — meaning the files here don’t exist on disk, but are created on the fly by the kernel. `/proc` contains a live view of your system: information about running processes, hardware stats, and kernel settings. For instance, `/proc/cpuinfo` gives detailed information about your processor, while `/proc/[PID]` folders represent each active process.

---

### `/root`

Don’t confuse this with `/`, the root directory. `/root` is the home directory for the **root user**, the system administrator with full control over everything. Unlike regular users, whose files live under `/home`, the root user has a separate space here to keep sensitive configurations and administrative tools isolated.

---

### `/run`

This is a temporary directory that stores runtime data — things like process ID files, system sockets, and status information about running services. It's created fresh at every boot, meaning anything stored here disappears when the system restarts. Modern Linux systems use `/run` to replace older methods of tracking system state during operation.

---

### `/sbin`

Like `/bin`, this directory contains executable programs — but these are meant for system administration tasks. Commands like `fsck` (filesystem check), `reboot`, and `iptables` (firewall management) live here. Most of these commands require root privileges, and they’re crucial for maintaining, repairing, or configuring the system.

---

### `/srv`

This directory is designed to hold data served by your system — such as web content, FTP files, or databases. For example, a web server might store its public HTML files in `/srv/www/`. It provides a consistent place for service data, especially on servers where keeping things organized and accessible is critical.

---

### `/tmp`

As the name implies, `/tmp` is used for temporary files. Applications and users can store data here that doesn't need to persist across reboots. Since the contents of `/tmp` are typically wiped on restart, it’s best to only use this directory for truly temporary tasks like socket files, quick scripts, or cached data.

---

### `/usr`

Despite its name, `/usr` doesn’t refer to user-specific files. Instead, it stands for **Unix System Resources** and contains most of the user-facing programs and data. This includes system-wide binaries (`/usr/bin`), libraries (`/usr/lib`), documentation (`/usr/share`), and system management tools (`/usr/sbin`). Think of `/usr` as a second level of the OS — above the raw core — where most standard software lives.

---

### `/var`

Short for "variable," this directory contains files that are expected to grow and change as the system runs. Logs are a big part of this — you'll find them in `/var/log/`. Email inboxes, printer spool files, cached updates, and databases also live here. If your system suddenly runs out of space, `/var` is often the first place to check.

---

## Final Thouths

Once you understand how Linux organizes its files, you’ll start to see its elegance and simplicity. Every file has a place, and every place has a purpose. Whether you're troubleshooting a boot issue, configuring a service, or just exploring your system, knowing where to look is half the battle.