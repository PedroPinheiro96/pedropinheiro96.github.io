---
title: "OverTheWire: Bandit Levels 0-5"
date: 2024-10-06 23:14:00 +0100
categories: [OverTheWire, Bandit]
tags: [ctf, linux, overthewire, bandit]
image: 
  path: "assets/images/OverTheWire/header.jpg"
---

The Bandit challenges on OverTheWire serve as an excellent starting point for anyone seeking to develop foundational skills in Linux, security and command-line navigation.
Designed as a series of progressively difficult challenges, Bandit guides participants through real-world scenarios that sharpen problem-solving skills using terminal commands and scripting.

## Level 0

### Level Goal

>The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is `bandit0` and the password is `bandit0`. Once logged in, go to the Level 1 page to find out how to beat Level 1.

### Commands you may need to solve this level
>[ssh](https://manpages.ubuntu.com/manpages/noble/man1/ssh.1.html)

### Solution 

The main objective of this level is to establish a connection to a remote machine via SSH.

The hints have already provided us all the necessary details:
- Username: `bandit0`
- Password: `bandit0`
- Host: `bandit.labs.overthewire.org`
- Port: `2220`

Execute the following command to connect to the remote machine:

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

When prompted for a passord, type `bandit0` and press Enter.

>Note: If asked to accept any fingerprint, type `yes` and press Enter.

The following message was displayed to let us know the command was successful:

![Level0](assets/images/Bandit0-5/Level0.png)

## Level 0 -> 1

### Login

>SSH: `ssh -p 2220 bandit1@bandit.labs.overthewire.org`<br>Password: `example`

### Level Goal

>The password for the next level is stored in a file called `readme` located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

### Commands you may need to solve this level
>[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html), [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html), [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html), [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html), [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html), [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

### Solution 

You can see you're already at the home directory by observing at the `~` in the prompt.

To read the contents of the `readme` file, use the `cat` command.

```bash
cat readme
```

![Level0-1](assets/images/Bandit0-5/Level0-1.png)

The password for the next level is `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If` as shown above.

To SSH into the next level, run:
```bash
ssh -p 2220 bandit1@bandit.labs.overthewire.org
```

Enter `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If` when prompted for a password.

## Level 1 -> 2

### Login

>SSH: `ssh -p 2220 bandit2@bandit.labs.overthewire.org`<br>Password: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

### Level Goal

>The password for the next level is stored in a file called `-` located in the home directory

### Commands you may need to solve this level
>[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html), [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html), [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html), [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html), [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html), [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

### Solution 

Once again, you'll notice you're in the home directory by observing the `~` in the prompt.<br>To list the files in the current directory, use the `ls` command.

![Level1-2](assets/images/Bandit0-5/Level1-2.png)

Now, let's attempt to read the contents of `-` by using the `cat` command:

![Level1-2-1](assets/images/Bandit0-5/Level1-2-1.png)

The terminal asked us for input and printed every character that's been submitted.

Let's read the `man` page of the `cat` command to find out why.<br>You can press `/` and type `-` to search for it.
```bash
man cat
```

![Level1-2-2](assets/images/Bandit0-5/Level1-2-2.png)

As you can see, when the filename is `-`, this command will read standard input.

To read the contents of `-`, execute either of the following commands:

![Level1-2-3](assets/images/Bandit0-5/Level1-2-3.png)

This reveals the password for the next level: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`.

## Level 2 -> 3

### Login

>SSH: `ssh -p 2220 bandit2@bandit.labs.overthewire.org`<br>Password: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

### Level Goal

>The password for the next level is stored in a file called `spaces in this filename` located in the home directory

### Commands you may need to solve this level
>[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html), [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html), [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html), [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html), [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html), [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

### Solution 

To read the content of a file with spaces in its name, escape every space or wrap the filename in quotes:
><https://linuxhandbook.com/filename-spaces-linux/>


```bash
cat spaces\ in\ this\ filename
cat "spaces in this filename"
```

![Level2-3](assets/images/Bandit0-5/Level2-3.png)

## Level 3 -> 4

### Login

>SSH: `ssh -p 2220 bandit3@bandit.labs.overthewire.org`<br>Password: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`.

### Level Goal

>The password for the next level is stored in a hidden file in the `inhere/` directory.

### Commands you may need to solve this level
>[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html), [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html), [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html), [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html), [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html), [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

### Solution 

To go to the `inhere/` directory, run:
```bash
cd inhere/
```
After executing `ls` to view its contents, nothing was displayed.

![Level3-4](assets/images/Bandit0-5/Level3-4.png)

Let's check the `man` pages for `ls` to see if we can find anything:
```bash
man ls
```

![Level3-4-1](assets/images/Bandit0-5/Level3-4-1.png)

As you can see above, the argument `-a` can be used to not ignore entries starting with `.`.<br>In Linux, hidden file and directory names start with a dot.

To list hidden files and directories we can use `ls -a`:

![Level3-4-2](assets/images/Bandit0-5/Level3-4-2.png)

Now we are able to see the `Hidding-From-You` file.

Use the `cat` command to read it.

![Level3-4-3](assets/images/Bandit0-5/Level3-4-3.png)

## Level 4 -> 5

### Login

>SSH: `ssh -p 2220 bandit4@bandit.labs.overthewire.org`<br>Password: `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

### Level Goal

>The password for the next level is stored in the only human-readable file in the `inhere` directory. Tip: if your terminal is messed up, try the “reset” command.

### Commands you may need to solve this level
>[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html), [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html), [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html), [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html), [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html), [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

### Solution 

Start by going to the `inhere/` directory.
```bash
cd inhere
```

Next, list the files in this directory:

![Level4-5](assets/images/Bandit0-5/Level4-5.png)

To identify the file types, we can use the [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) command.

This command outputs the file type of the file passed as an argument.

>Note: The name of the files begin with a `-`. Remebmer that `-` is a special character in linux.

To check the file types of the files in the directory run:
```bash
file ./*
```

![Level4-5-1](assets/images/Bandit0-5/Level4-5-1.png)

We can see that `-file07` is an ASCII file, indicating it is human-readable.

To find the password for the next level, you can read the file using any of the following methods:

![Level4-5-2](assets/images/Bandit0-5/Level4-5-2.png)
