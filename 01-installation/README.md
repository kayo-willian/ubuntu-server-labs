# Ubuntu Server Installation

## Overview

This laboratory documents the manual installation and initial setup of Ubuntu Server 24.04 LTS in a VirtualBox virtual machine.

The installation was performed manually instead of using VirtualBox Unattended Installation in order to understand the installation process, the initial system configuration, and the decisions involved in deploying a Linux server.

## Lab Objective

The objective of this laboratory was to:

* Install Ubuntu Server 24.04 LTS in a virtual machine.
* Understand the main stages of the Ubuntu Server installation process.
* Configure the initial network and storage settings.
* Create a local administrative user.
* Configure the server hostname.
* Install OpenSSH Server for future remote administration.
* Perform the first boot and verify that the installation completed successfully.
* Begin exploring the Linux filesystem and command line.

## Environment

### Host System

| Component        | Specification      |
| ---------------- | ------------------ |
| Operating System | Ubuntu Linux       |
| CPU              | Intel Core i7-3770 |
| RAM              | 16 GB DDR3         |
| Storage          | 120 GB SSD         |
| Virtualization   | VirtualBox         |

### Virtual Machine

| Component          | Specification                          |
| ------------------ | -------------------------------------- |
| VM Name            | `Ubuntu-Server-Lab`                    |
| Operating System   | Ubuntu Server 24.04 LTS                |
| ISO                | `ubuntu-24.04.4-live-server-amd64.iso` |
| CPU                | 2 vCPU                                 |
| RAM                | 2048 MB                                |
| Disk               | 25 GB                                  |
| Disk Type          | VDI                                    |
| Storage Allocation | Dynamically allocated                  |
| Network            | NAT                                    |

## Installation

### Installation Type

The standard **Ubuntu Server** installation option was selected.

The **Ubuntu Server (minimized)** option was not selected.

The standard installation was chosen because the goal of this laboratory is to learn the Ubuntu Server environment and its administration tools before optimizing the system for minimal resource usage.

### Network Configuration

The virtual machine was configured with a **NAT** network adapter in VirtualBox.

Ubuntu Server was initially left to obtain its network configuration automatically using DHCP.

A static IP address was intentionally not configured during installation.

Network configuration and static addressing will be studied separately so that the concepts can be understood rather than configured without context.

### Storage Configuration

The virtual machine uses a 25 GB dynamically allocated VDI disk.

The automatic storage configuration provided by the Ubuntu installer was selected.

A custom storage layout was not used.

This decision was intentional because manual partitioning introduces additional concepts that are not necessary for the first installation.

The initial objective was to become familiar with Ubuntu Server before studying filesystem layout and partition management in greater detail.

### User and Hostname

A local user was created during the installation:

```text
Username: kayo
Hostname: ubuntu-server
```

The user account was used for the initial login after installation.

The shell prompt later confirmed the hostname and username:

```text
kayo@ubuntu-server:~$
```

The `$` at the end of the prompt indicates that the current session is running as a regular user rather than directly as `root`.

### SSH Configuration

The **Install OpenSSH server** option was enabled during installation.

No SSH keys were imported at this stage.

OpenSSH was installed because SSH will later be used to remotely administer the Ubuntu Server from the Ubuntu host system.

The intended architecture is:

```text
Ubuntu Host
     |
     | SSH
     v
Ubuntu Server VM
```

Remote SSH administration will be explored in a later laboratory.

### Featured Server Snaps

No Featured Server Snaps were selected.

The installer offered several server applications, including services such as MicroK8s and Mosquitto.

These applications were intentionally not installed because they are not required for the initial Ubuntu Server environment.

Keeping the installation focused also makes it easier to understand each service when it is introduced later.

## First Boot

After the installation completed, the system was rebooted.

The server successfully reached the login prompt:

```text
ubuntu-server login:
```

The previously created `kayo` account was then used to log in.

After authentication, the shell displayed:

```text
kayo@ubuntu-server:~$
```

This confirmed that the Ubuntu Server installation was operational and that the user account could successfully start a shell session.

## Initial Command Line Verification

After the first login, several basic commands were used to understand the current environment.

### `whoami`

The `whoami` command displays the username associated with the current shell session.

```bash
whoami
```

Expected result:

```text
kayo
```

This was used to verify that the session was running under the expected user account.

### `pwd`

The `pwd` command means **Print Working Directory**.

```bash
pwd
```

The result was:

```text
/home/kayo
```

This showed that the shell initially started in the user's home directory.

The `~` symbol displayed in the shell prompt is an abbreviation for the current user's home directory.

Therefore:

```text
~ = /home/kayo
```

### `ls`

The `ls` command lists the contents of the current directory.

```bash
ls
```

The command initially produced no visible output because the user's home directory did not contain regular visible files at that point.

An empty result is still a valid command result and indicates that there were no visible directory entries to display.

### `ls -la`

The following command was then used:

```bash
ls -la
```

The additional options provide a more detailed listing and include hidden files.

Files such as the following were observed:

```text
.bashrc
.profile
```

Linux commonly treats filenames beginning with `.` as hidden from a normal `ls` listing.

`.bashrc` contains configuration used by Bash, while `.profile` contains shell environment configuration that can be loaded when the user's login session starts.

These files were observed but not modified.

## Linux Filesystem Exploration

The Linux filesystem uses a hierarchical directory structure.

The top of this hierarchy is represented by:

```text
/
```

The `/` directory is known as the **root directory**.

The system was navigated to the root directory using:

```bash
cd /
```

The current location was then verified with:

```bash
pwd
```

Result:

```text
/
```

The root directory was inspected with:

```bash
ls
```

Several important directories were observed:

```text
bin
boot
dev
etc
home
lib
media
mnt
opt
proc
root
run
sbin
srv
tmp
usr
var
```

Some of the most important directories for future administration work include:

| Directory | Purpose                                  |
| --------- | ---------------------------------------- |
| `/home`   | User home directories                    |
| `/etc`    | System and service configuration files   |
| `/var`    | Frequently changing data, including logs |
| `/usr`    | Programs and system resources            |
| `/tmp`    | Temporary files                          |
| `/root`   | Home directory of the `root` user        |
| `/dev`    | Device interfaces                        |
| `/proc`   | Kernel and process information           |

This initial exploration introduced the Linux filesystem hierarchy before beginning more advanced administration tasks.

## Directory Navigation

The `cd` command is used to change the current directory.

For example:

```bash
cd /home/kayo
```

moves directly to the user's home directory.

The special directory entry:

```text
..
```

represents the parent directory.

Therefore:

```bash
cd ..
```

moves one level upward in the directory hierarchy.

For example:

```text
/home/kayo
     |
     | cd ..
     v
/home
```

Another `cd ..` would move from `/home` to `/`.

This introduced the distinction between **absolute paths**, such as:

```text
/home/kayo
```

and navigation using relative paths, such as:

```text
..
```

## Troubleshooting

### VirtualBox Mouse and Keyboard Behavior

During the installation, the mouse pointer disappeared when interacting with the Ubuntu Server console.

This was caused by the way VirtualBox captures the mouse pointer and by the fact that the Ubuntu Server installer uses a text-based interface.

The installation could still be completed using the keyboard.

The following keys were useful for navigating the installer:

| Key        | Function                        |
| ---------- | ------------------------------- |
| `Tab`      | Move between interface elements |
| Arrow keys | Navigate options                |
| `Enter`    | Select or confirm               |

The default VirtualBox Host key was also used when necessary to release the captured mouse pointer.

### Text-Based Server Interface

After installation, Ubuntu Server presented a terminal login instead of a graphical desktop environment.

This is expected behavior.

Ubuntu Server can be administered primarily through the command line, which is particularly important for remote administration through SSH.

## Lessons Learned

The installation process introduced several fundamental concepts:

* A virtual machine can run a complete server operating system independently from the host operating system.
* Ubuntu Server does not require a graphical desktop environment to function as a server.
* The installation process involves several important decisions concerning networking, storage, users, hostname, and services.
* DHCP can provide the initial network configuration without requiring a static address during installation.
* OpenSSH Server can be installed during the initial deployment to enable future remote administration.
* Installing only the components currently required keeps the initial environment easier to understand.
* Linux uses a hierarchical filesystem beginning at `/`.
* User home directories are normally located under `/home`.
* The `~` symbol represents the current user's home directory.
* `ls`, `pwd`, `whoami`, and `cd` are fundamental commands for navigating and understanding a Linux system.

## Key Takeaways

1. Ubuntu Server can be installed and operated entirely through a command-line environment.

2. The initial installation process is already part of server administration because decisions made during deployment affect how the system will be used later.

3. The Linux filesystem is hierarchical and begins at `/`, with different directories serving different system purposes.

4. Basic commands such as `pwd`, `ls`, `cd`, and `whoami` provide essential information about the current environment.

5. OpenSSH Server was installed during deployment to prepare the environment for future remote administration.

6. A clean initial installation provides a useful foundation for progressively introducing networking, users, permissions, packages, services, security, and multi-server infrastructure.

