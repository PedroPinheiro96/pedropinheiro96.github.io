---
title: "Linux 101: Navigating The Command Line"
date: 2025-07-21 20:52:00 +0100
categories: [Linux]
tags: [linux]
image: 
  path: assets/images/Linux101/NavigatingTheCommandLine/Linux101-NavigatingTheCommandLine.png
--- 

If you're new to Linux, the terminal might feel like an intimidating wall of text — but it's one of the most powerful tools at your fingertips. Mastering the command line opens up full control of your system and is essential for using Linux efficiently.

In this post, we’ll break down the basics of navigating the Linux command line: how to move between directories, list files, understand where you are, and use some essential tools along the way.

---

## Understanding the Command Prompot

Before diving into the world of Linux commands, it's important to understand the very first thing you’ll see when you open a terminal: the **command prompt**. It might look something like this:
```bash
pedro@ubuntu:~$
```
At first glance, this might seem cryptic—but it's actually giving you useful information at a glance. Let’s break it down:

- `pedro` – This is the username of the person currently logged in.
- `@ubuntu` – This shows the hostname, or the name of the computer you’re working on. In this case, it's called "ubuntu".
- `~` – The tilde represents the home directory of the user. So ~ is shorthand for `/home/pedro`.
- `$` – This symbol indicates that you're a regular user. If you were logged in as the root (or superuser), you'd see a `#` instead.

So when you see `pedro@ubuntu:~$`, it's telling you:

You're logged in as `pedro` on a machine named `ubuntu`, you're in your `home` directory, and you're ready to type a command.

## Where Am I? - whereami

The very first thing you'll want to know is **where you are** in the file system. Use the `pwd` command to find your **Present Working Directory**:

![pwd](/assets/images/Linux101/NavigatingTheCommandLine/pwd.png)

## Who Am I? - whoami

After knowing where you are in the filesystem, the next useful thing to know is who you are—that is, what user account you're operating under. Use the whoami command:

![whoami](/assets/images/Linux101/NavigatingTheCommandLine/whoami.png)

This command returns the current user that is logged into the shell session. It's especially useful when working on multi-user systems or using elevated privileges like sudo.

## Listing Files – ls

To view the contents of a directory, use `ls`:

```bash
ls             # List files in current directory
ls -l          # Long listing (shows permissions, size, date)
ls -a          # Show hidden files
ls -lh         # Human-readable sizes
``` 

![ls](/assets/images/Linux101/NavigatingTheCommandLine/ls.png)

## Moving Around – cd

To move between directories in Linux, you'll use the cd (change directory) command:

```bash
cd /etc         # Go to the /etc directory
cd ..           # Move up one directory
cd ~/Documents  # Go to Documents in your home folder
cd              # Go to your home directory
```

![cd](/assets/images/Linux101/NavigatingTheCommandLine/cd.png)

Special symbols to remember:

- `~` – Your home directory (e.g., /home/pedro)
- `.` – The current directory
- `..` – The parent directory (one level up)

These shortcuts make navigation faster and more intuitive, especially when you're working deep within a folder structure.

## Absolute vs Relative Paths

When using linux commands, you can use either absolute paths or relative paths. Understanding the difference is key to efficient command-line navigation.

### Absolute Path

An absolute path starts from the root of the file system (/) and specifies the full location of a directory or file, no matter where you currently are.

```bash
cd /var/log
```

This command takes you directly to the /var/log directory, regardless of your current location.

### Relative Path

A relative path is based on your current location in the file system. It does not begin with a `/`.
```bash
cd ../Downloads
```

This moves you to the Downloads directory that is one level up from your current directory.

Here’s a quick comparison:

| Current Directory          | Command         | Resulting Directory                         |
| -------------------------- | --------------- | ------------------------------------------- |
| /home/pedro                | cd /etc         | /etc (absolute path)                        |
| /home/pedro                | cd Documents    | /home/pedro/Documents (relative path)       |
| /home/pedro/docs/tutorials | cd ../../images | /home/pedro/images (relative path using ..) |


## Viewing File Contents – cat, less, head, and tail

Once you’ve found your way around the filesystem, you’ll often want to look inside files—whether it's reading a config file, checking logs, or reviewing output from a script. 

Here are the most commonly used tools to view file contents from the command line:

### cat - Display the Entire File

Use `cat` (short for concatenate) to quickly print the entire contents of a file to the terminal. Great for small files, but not ideal for long ones since it just dumps everything at once.

![cat](/assets/images/Linux101/NavigatingTheCommandLine/cat.png)

### less

`less` is perfect for browsing long files. It opens the file in a scrollable view—use the `arrow keys` or `Page Up/Down` to navigate. Press `q` to quit and return to the prompt.

![less](/assets/images/Linux101/NavigatingTheCommandLine/less.png)

### head

Displays the first 10 lines of a file (by default). Useful when you only need to check the top part of a file—like a header or summary.

![head](/assets/images/Linux101/NavigatingTheCommandLine/head.png)

You can show more lines with the `-n` flag:

```bash
tail -n 20 file.txt
```

### tail

Shows the last 10 lines (by default) of a file—commonly used to check recent log entries or file endings.

![tail](/assets/images/Linux101/NavigatingTheCommandLine/tail.png)

### Text Editors

You can also use a text editor line `nano` to read a file.
```bash
nano file.txt
```

![nano](/assets/images/Linux101/NavigatingTheCommandLine/nano.png)


## Clearing the Screen and Using Autocomplete

The command line offers some handy shortcuts to keep your workflow smooth and efficient. Here are a few small but powerful tricks:

### Clean Up Your Terminal - clear
```bash
clear
```

This command clears the terminal screen, giving you a fresh view without closing the session.
You can also press `Ctrl + L` as a quick keyboard shortcut to achieve the same thing.

### Autocomplete with Tab

Typing long file or folder names? Just hit the `Tab` key to auto-complete as you type:

- Type part of a command or filename, then press Tab.
- If there’s only one match, it completes automatically.
- If there are multiple matches, press Tab twice to see a list of suggestions.

This saves time and helps avoid typos—especially in long directory paths or file names.

## Useful Directory Commands

Now that you know how to move around and view files, here are some essential commands to create, copy, move, and delete files and directories.

### Create a Directory
```bash
mkdir myfolder
```

Creates a new folder named myfolder in the current directory.

### Remove a Directory
```bash
rmdir myfolder
```

Deletes an empty directory. It won’t work if the folder contains files.

### Remove a Directory and Its Contents
```bash
rm -r myfolder
```

Deletes a directory and everything inside it—use this carefully!
The -r flag stands for recursive, meaning it deletes all nested files and folders too.

### Copy a File
```bash
cp file1 file2
```

Makes a copy of file1 and names the copy file2.

### Move or Rename a File
```bash
mv file1 newname
```

Moves file1 to a new location or renames it to newname if no path is specified.

###  Move a File into a Directory
```bash
mv notes.txt ~/Documents/
```

Moves notes.txt into your Documents folder.
If a file with the same name already exists there, it will be overwritten without warning.

## Finding Files and Searching Text – find and grep

Whether you're trying to locate a missing file or search through thousands of lines in a log, Linux gives you powerful tools to find exactly what you need—quickly and efficiently.
Here are some essential commands:

### Locate Files by Name - find
```bash
find . -name "file.txt"
```

Searches for a file named file.txt, starting in the current directory (.) and going through all subfolders.

Want to search elsewhere?
```bash
find /home/username -name "*.log"
```

Finds all .log files inside /home/username.

### Search for Text Inside Files - grep

The `grep` command searches for lines containing specific text within a file.

Search for specific words:
![grepTEST](/assets/images/Linux101/NavigatingTheCommandLine/grepTEST.png)

This will display all lines in `file.txt` that contain the exact word `TEST`. Note that grep is case-sensitive by default, so it will not match `test` or `Test`.

To make the search case-insensitive, use the `-i` flag:
```bash
grep -i "test" file.txt
```

This would match `test`, `Test`, `TEST`, and any other case variation.

Recursive Search:
```bash
grep -r "error" /var/log
```

Searches for `error` in all files and subdirectories under `/var/log`. Perfect for digging through large directories or entire projects.

Exclude Matches with `-v`:
![grep-v](/assets/images/Linux101/NavigatingTheCommandLine/grep-v.png)

Shows all lines in `file.txt` that do not contain the word `TEST`.
Useful when you want to filter out unwanted lines and focus on everything else.

## Getting Unstuck in the Linux Command Line

When working in the Linux terminal, it’s perfectly normal to forget commands or their options. Luckily, most commands come with built-in documentation and handy shortcuts to help you out:

### Manual pages

Opens the full **manual page** (or “man page”) for a command. For example,  
```bash
man ls
```

![man-ls](/assets/images/Linux101/NavigatingTheCommandLine/man-ls.png)

This will show detailed information about the `ls` command, including all options and examples.
Use the `arrow keys` to scroll, and press `q` to quit the manual.

### Help flag

Most commands support a quick help option that displays a summary of usage and available flags. For example:
```bash
man --help
man -h
```

![man-help](/assets/images/Linux101/NavigatingTheCommandLine/man-help.png)

This flag gives you a brief overview of how to use the `man` command.

## Commands 

- Press the `↑ (up arrow)` to scroll back through previous commands.
- Press the `↓ (down arrow)` to move forward again.

This is great for repeating or editing previous commands without retyping them. You can even use `Ctrl + R` to search through your command history interactively.

### History

The `history` command displays a numbered list of all the commands you've recently typed in your terminal session. This makes it easy to review, find, and reuse previous commands without retyping them.

For example, running `history` might show output like this:
```bash
101  ls -l
102  cd /var/log
```

To quickly rerun a command from your history, use the `!` followed by the command's number. For instance:
```bash
!101
```

This will execute the `ls -l` command again.

### Last Command - !!

The `!!` command is a quick shortcut that reruns the **last command** you executed in the terminal. 

For example, if you just ran:
```bash
sudo apt update
```

Executing `!!` will run `sudo apt update` again. 