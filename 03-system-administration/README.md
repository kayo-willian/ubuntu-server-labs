# Lab 03 - Ubuntu Server System Administration

## Objective

Begin administering an Ubuntu Server through the command line and understand the basic information, resources, services, and system components that an administrator needs to monitor and manage.

This lab builds on the previous networking and SSH configuration. The Ubuntu Server will now be managed remotely through the SSH terminal from the host PC.

The focus is not on memorizing commands, but on understanding what information each command provides and how that information can be used during server administration and troubleshooting.

---

## Environment

The server used in this lab is an Ubuntu Server 24.04.4 LTS virtual machine running on VirtualBox.

Remote administration is performed through SSH from the Ubuntu host.

The server is accessed using:

```bash
ssh -p 2222 kayo@127.0.0.1
```

The VirtualBox NAT configuration forwards the host's TCP port `2222` to the SSH service on the Ubuntu Server.

---

# 1. System Identification

### `hostnamectl`

The `hostnamectl` command displays information about the system's hostname, operating system, kernel, architecture, and virtualization environment.

I used this command to identify the basic characteristics of the Ubuntu Server.

Relevant information identified:

```text
Static hostname: ubuntu-server
Operating System: Ubuntu 24.04.4 LTS
Kernel: Linux 6.8.0-138-generic
Architecture: x86-64
Virtualization: VirtualBox
```

The hostname identifies the server as:

```text
ubuntu-server
```

The hostname is different from the server's IP address. The hostname identifies the system, while the IP address is used for network communication.

This distinction is important when administering servers because both names and addresses are commonly used to identify and access systems.

---

# 2. CPU Information

### `lscpu`

The `lscpu` command displays detailed information about the CPU architecture and processors available to the Linux system.

I used it to determine how much CPU capacity was assigned to the virtual machine.

The relevant information was:

```text
Architecture: x86_64
CPU(s): 2
```

The virtual machine has two virtual CPUs assigned to it.

The command also identified the processor presented to the virtual machine as an Intel Core i7-3770.

Most of the detailed information returned by `lscpu`, such as CPU flags and vulnerability information, was not necessary for the initial system inventory.

The important administrative lesson is knowing how to obtain CPU information when investigating system performance or resource availability.

---

# 3. Memory and Swap

### `free -h`

The `free -h` command displays the system's RAM and swap usage in a human-readable format.

The server reported approximately:

```text
Memory:
Total:       1.9 GiB
Used:        327 MiB
Available:   1.6 GiB

Swap:
Total:       2.0 GiB
Used:        0 B
```

The `available` value is particularly useful when determining how much memory is realistically available for new processes.

Swap provides additional memory space backed by disk storage. It is considerably slower than RAM and is generally not something an administrator wants a server to depend on heavily during normal operation.

At the time of the test, the server was not using swap.

---

# 4. Storage Usage

### `df -h`

The `df -h` command displays filesystem disk usage in a human-readable format.

The main filesystem was:

```text
Filesystem: /dev/mapper/ubuntu--vg-ubuntu--lv
Mounted on: /
Size:       12G
Used:       4.7G
Available:  6.0G
Usage:      44%
```

The `/` filesystem is the root filesystem and contains the main Ubuntu Server installation and its associated files.

The system also contains a separate `/boot` filesystem:

```text
Size:       2.0G
Used:       104M
Available:  1.7G
Usage:      6%
```

The storage configuration uses LVM, which is represented by:

```text
/dev/mapper/ubuntu--vg-ubuntu--lv
```

LVM will be studied in greater detail in a future administration task.

The `df` command is particularly important for troubleshooting because a filesystem reaching full capacity can cause services and applications to fail.

---

# 5. Running Services

### `systemctl --type=service --state=running`

Linux servers run many background services that provide different functions.

The `systemctl` command can be used to inspect and manage services controlled by `systemd`.

The command was used to identify services currently running on the server.

Among the services observed were:

```text
cron.service
dbus.service
rsyslog.service
ssh.service
systemd-journald.service
systemd-networkd.service
systemd-resolved.service
systemd-timesyncd.service
systemd-udevd.service
udisks2.service
```

Some of the most relevant services at this stage are:

### `ssh.service`

Provides remote access to the server through SSH.

### `systemd-networkd.service`

Manages network configuration and network interfaces.

### `systemd-resolved.service`

Provides DNS resolution functionality.

### `rsyslog.service`

Provides system logging functionality.

### `cron.service`

Provides scheduled task execution.

### `systemd-journald.service`

Collects system and service logs used by the systemd logging infrastructure.

The important concept is that a server's functionality is provided by multiple services operating in the background.

---

# 6. Understanding Service States

During the previous SSH configuration, the SSH service provided a practical example of service management.

The initial status was:

```text
Active: inactive (dead)
```

The service was started using:

```bash
sudo systemctl start ssh
```

After starting it, the status changed to:

```text
Active: active (running)
```

This demonstrated that installing a service and running a service are different operations.

A package may be installed on the system while its associated service is not currently running.

The basic service management commands introduced in this lab are:

```bash
systemctl status <service>
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl enable <service>
systemctl disable <service>
```

These commands will be explored in greater detail during the continuation of this laboratory.

---

# 7. SSH as a Server Administration Tool

SSH is now the primary method used to administer this Ubuntu Server.

The connection is established from the Ubuntu host using:

```bash
ssh -p 2222 kayo@127.0.0.1
```

After authentication, the shell prompt changes to:

```text
kayo@ubuntu-server:~$
```

This indicates that commands entered in the terminal are being executed on the Ubuntu Server rather than on the host machine.

The administration workflow is therefore:

```text
Ubuntu Host
     |
     | SSH
     | 127.0.0.1:2222
     v
VirtualBox NAT
     |
     | Port Forwarding
     | 10.0.2.15:22
     v
Ubuntu Server
```

This allows the server to be administered without interacting directly with the VM console.

From this point forward, the laboratory administration will primarily be performed through the SSH terminal.

---

# 8. Initial System Inventory

The information collected during the initial inspection can be summarized as:

```text
Ubuntu Server
├── Hostname: ubuntu-server
├── OS: Ubuntu 24.04.4 LTS
├── Architecture: x86_64
├── CPU: 2 vCPUs
├── RAM: 1.9 GiB
├── Swap: 2.0 GiB
├── Root filesystem: 12 GiB
├── Root usage: 44%
├── SSH: active
└── Virtualization: VirtualBox
```

This type of inventory is useful when an administrator first receives access to a server because it establishes a baseline of the system's resources and configuration.

---

# Troubleshooting

The SSH service provided the first real administration troubleshooting scenario in this project.

Initially, the SSH service was installed but inactive:

```text
Active: inactive (dead)
```

The service was started with:

```bash
sudo systemctl start ssh
```

The service was then verified as:

```text
Active: active (running)
```

The SSH daemon was also confirmed to be listening on TCP port 22.

The remote connection initially failed because of an incorrect VirtualBox port forwarding configuration.

After correcting the forwarding rule, remote SSH access was successfully established.

This demonstrated an important troubleshooting principle:

> Verify each layer of a service before changing the configuration.

In this case, the investigation checked:

```text
SSH service
    ↓
SSH listening port
    ↓
VirtualBox port forwarding
    ↓
Host connection
```

---

# Key Takeaways

* `hostnamectl` can be used to identify the operating system, hostname, kernel, architecture, and virtualization environment.
* `lscpu` provides information about CPU resources available to Linux.
* `free -h` is useful for monitoring RAM and swap usage.
* `df -h` is useful for monitoring filesystem storage usage.
* Linux servers rely on background services to provide system functionality.
* `systemctl` is the primary interface for managing systemd services.
* A service can be installed without currently being active.
* SSH allows administrators to manage Linux servers remotely through the command line.
* Server troubleshooting should verify each layer of a system before making changes.
* Establishing a system baseline is useful for future troubleshooting and administration.

---

## Lab Status

**In Progress**

The initial system inventory and remote administration environment have been established.

The next activities will focus on practical Linux server administration, including service management, system updates, packages, users, permissions, processes, logs, and other tasks commonly performed by Linux administrators.
