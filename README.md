# Ubuntu Server Labs

Hands-on Ubuntu Server labs focused on Linux administration, networking, services, security, and infrastructure.

This repository documents practical experiments and learning activities performed while studying Linux and network administration.

The purpose of this repository is twofold: to demonstrate practical experience and to build a personal technical reference that can be consulted during future classes, laboratories, troubleshooting, and system administration tasks.

The focus is not simply on executing commands, but on understanding why a configuration is required, how it works, how to verify the result, and how to troubleshoot problems.

## Learning Approach

Each laboratory follows a practical learning cycle:

```text
Concept
   ↓
Why does it exist?
   ↓
Command / Configuration
   ↓
Observe the result
   ↓
Implementation
   ↓
Testing
   ↓
Troubleshooting
   ↓
Documentation
```

The documentation emphasizes understanding rather than memorization.

Commands are documented together with their purpose, expected behavior, observed results, and practical context.

## Labs

| Lab | Topic                                            | Description                                                       |
| --- | ------------------------------------------------ | ------------------------------------------------------------------|
| 01  | [Ubuntu Server Installation](./01-installation/) | Installation and initial configuration of Ubuntu Server 24.04 LTS |
| 02  | [Ubuntu Server Networking](./02-ubuntu-server-networking/) | Network interfaces, IP addressing, routing, default gateway, DNS, connectivity testing, and troubleshooting |
| 03 | [Ubuntu Server System Administration](./03-system-administration) | System identification, CPU and memory resources, storage, services, systemd, SSH administration, and initial server inventory |




This table will grow as new laboratories are created.

## Environment

### Host System

| Component        | Specification      |
| ----------------- | ------------------ |
| Operating System | Ubuntu Linux       |
| CPU              | Intel Core i7-3770 |
| RAM              | 16 GB DDR3         |
| Storage          | 120 GB SSD         |
| Virtualization   | VirtualBox         |

### Virtual Machine

| Component          | Specification           |
| ------------------- | ------------------------ |
| VM Name            | `Ubuntu-Server-Lab`     |
| Operating System   | Ubuntu Server 24.04 LTS |
| CPU                | 2 vCPU                  |
| RAM                | 2048 MB                 |
| Disk               | 25 GB                   |
| Disk Type          | VDI                     |
| Storage Allocation | Dynamically allocated   |
| Network            | NAT                     |

## Documentation Philosophy

Each laboratory should document the relevant parts of the learning process, including:

* Objective
* Scenario or problem
* Environment
* Concepts learned
* Implementation
* Important commands
* Expected results
* Actual results
* Testing and verification
* Troubleshooting
* Lessons learned
* Key Takeaways

The objective is not to create a transcript of every command executed.

Instead, the documentation should explain what was done, why it was done, what happened, and what was learned from the process.

## Linux and Networking

An important part of this project is connecting Linux administration with networking concepts.

The same fundamental networking concepts studied with Cisco IOS can also be observed and managed in Linux, although the commands and implementation methods are different.

For example:

| Concept            | Cisco IOS                 | Linux      |
| ------------------- | -------------------------- | ---------- |
| View interfaces    | `show ip interface brief` | `ip addr`  |
| View routing table | `show ip route`           | `ip route` |
| Test connectivity  | `ping`                    | `ping`     |

These comparisons are intended to reinforce concepts across different platforms rather than imply that the commands are equivalent.

## Ubuntu Server Documentation

The primary technical reference for this project is the official Ubuntu Server documentation:

https://documentation.ubuntu.com/server/

The documentation is organized into four useful categories:

* **Tutorials** for learning through practical guided activities
* **How-to guides** for accomplishing specific tasks
* **Explanation** for understanding concepts and architecture
* **Reference** for detailed technical information and configuration options

Learning how to locate and use the official documentation is itself part of the administration skill being developed through this project.

## Purpose

This repository is intended to become a practical Linux administration knowledge base built through real configuration, experimentation, verification, and troubleshooting.

The goal is to have documentation that remains useful after completing the individual laboratories and can be consulted when similar concepts appear in future studies or real administration scenarios.
