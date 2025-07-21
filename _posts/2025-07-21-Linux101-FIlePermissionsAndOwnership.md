---
title: "Linux 101: File Permissions and Ownership"
date: 2025-07-21 20:53:00 +0100
categories: [Linux]
tags: [linux]
image: 
  path: assets/images/Linux101/FilePermissionsAndOwnership/Linux101-FilePermissionsAndOwnership.png
---

Understanding how file permissions and ownership work in Linux is essential for managing security and access on your system. Whether you're working on a personal project or administering a multi-user server, this guide will walk you through the basics.

---

## The Basics of File Permissions

Use the `ls -l` command to view file details:
![ls-permissions](/assets/images/Linux101/FilePermissionsAndOwnership/ls-permissions.png)

The initial string of the `file.txt` line breaks down as:

- `-` → File type (dash means regular file, `d` means directory)
- `rw-` → User (owner) permissions
- `rw-` → Group permissions
- `r--` → Others (everyone else)
- `pedro` : The owner (user)
- `pedro` : The group owner
- `0` : File size in bytes
- `Jul 21 20:15` : Last modified date
- `file.txt` : File name

### Understanding File Permissions

Each permission is represented by a letter:

- `r` (Read): Allows reading the contents of a file or listing the contents of a directory.
- `w` (Write): Allows modifying the contents of a file, or creating/deleting files in a directory.
- `x` (Execute):  
  - For **files**: Allows execution of the file (e.g. running scripts or programs)  
  - For **directories**: Allows entering the directory (`cd`) and accessing files inside (with proper read permissions)


So in this example:

- The owner can read and write
- Group members can read and write
- Others can only read

---

## Ownership: User and Group

Every file and directory on a Linux system has two types of ownership:

- **User (owner):** Typically the person who created the file. They have control over the file's permissions and can modify or delete it, depending on the access granted.
- **Group:** A collection of users who are granted shared permissions to access or modify the file. This is useful for collaborative environments.

Use `ls -l` to see who owns what:

![ls-permissions](/assets/images/Linux101/FilePermissionsAndOwnership/ls-permissions.png)

In this example, the file is owned by the user `pedro` and the group `pedro`.

### Changing Ownership

You can change the ownership of a file or directory using the chown (change owner) command.
Change the owner only:
![chown-user](/assets/images/Linux101/FilePermissionsAndOwnership/chown-user.png)

This assigns ownership of script.sh to `pedro2`.

Change both the owner and the group:
![chown-user-group](/assets/images/Linux101/FilePermissionsAndOwnership/chown-user-group.png)

This assigns ownership to `pedro` and sets the group to `ssh-users`.

Change ownership recursively (for directories and all contents):
```bash
sudo chown -R newuser:newgroup directory/
```

The `-R` (recursive) flag makes chown apply to all files and subdirectories inside the specified directory. This is especially useful when setting up new user environments, web servers, or shared project folders.

## Changing Permissions with chmod

You can change permissions with the `chmod` command, either using **symbolic** or **numeric** mode.

### Symbolic Mode

Symbolic mode uses letters to represent the user categories and permission types, allowing you to add, remove, or set permissions.

The permission types are:

- `r` = **read** permission — allows viewing the contents of a file or listing files in a directory.
- `w` = **write** permission — allows modifying or deleting a file or creating/removing files within a directory.
- `x` = **execute** permission — allows running a file as a program/script or entering a directory.

The user categories are:

- `u` = **user** (the file owner)
- `g` = **group** (users who belong to the file’s group)
- `o` = **others** (everyone else)
- `a` = **all** (user, group, and others)

Examples:

```bash
chmod u+x script.sh   # Add execute permission for the user (owner)
chmod g-w script.sh   # Remove write permission from the group
chmod o=r script.sh   # Set others' permission to read only (removes all others except read)
```

![chmod-symbolic](/assets/images/Linux101/FilePermissionsAndOwnership/chmod-symbolic.png)


### Numeric Mode

In **numeric (or octal) mode**, file permissions are represented using **three digits**, with each digit corresponding to a category of users:

1. User (owner)
2. Group
3. Others

Each digit is a sum of values assigned to the three basic permission types:

- Read (`r`) = 4  
- Write (`w`) = 2  
- Execute (`x`) = 1

By adding these values together, you get a number from 0 to 7 that represents the permission set for each category.

Example:

```bash
chmod 775 script.sh
```

This sets:

- 7 (4+2+1) → Owner: read, write, execute
- 7 (4+2+1) → Group: read, write, execute
- 5 (4+0+1) → Others: read, execute

![chmod-numeric](/assets/images/Linux101/FilePermissionsAndOwnership/chmod-numeric.png)

Common permission sets:

* `644` → User can read/write; others can read
* `755` → User can do everything; others can read/execute
* `700` → Only user has access

---

## Helpful Tool: chmod Calculator

Struggling to convert permissions to numeric or symbolic mode? Use this [interactive chmod calculator](https://chmod-calculator.com/) to visualize and generate the exact commands you need:

It’s a great resource for beginners and pros alike.

---

## Best Practices

* Never give full (`777`) permissions unless absolutely necessary
* Scripts should only be executable if needed
* Use groups to manage shared access between users

---

## Summary

Understanding and managing file permissions is critical in Linux. By using `ls -l`, `chmod`, and `chown`, you can control exactly who has access to what — keeping your system secure and under control.

Would you like a downloadable cheat sheet or command-line permissions calculator next?
