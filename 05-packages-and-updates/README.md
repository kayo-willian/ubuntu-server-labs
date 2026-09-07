# Lab 05 — Packages & Updates

## Objective

Learn the fundamentals of package management in Ubuntu Server using APT and DPKG.

In this lab, package installation, removal, updates, package search, package information, and Ubuntu repositories were explored through practical commands.

---

## Environment

| Item                   | Configuration             |
| ---------------------- | ------------------------- |
| Operating System       | Ubuntu Server 24.04.4 LTS |
| Virtualization         | VirtualBox                |
| Architecture           | x86_64                    |
| Package Manager        | APT                       |
| Low-level Package Tool | DPKG                      |
| Repository Format      | `.deb`                    |

---

## 1. Updating Package Information

The first step was updating the local package indexes:

```bash
sudo apt update
```

The command contacted the configured Ubuntu repositories and downloaded the latest package information.

The system reported that **68 packages could be upgraded**.

### Important distinction

`apt update` does **not** upgrade installed software.

It updates the information APT uses to determine which packages and versions are available.

---

## 2. Checking Available Updates

The available upgrades were inspected with:

```bash
apt list --upgradable
```

The command displayed packages with newer versions available.

Examples included:

* `openssh-server`
* `linux-generic`
* `linux-image-generic`
* `netplan.io`
* `nftables`
* `systemd`
* Python packages
* `snapd`

This demonstrated that updates can affect important system components, not only user applications.

---

## 3. Upgrading Installed Packages

The available package upgrades were installed with:

```bash
sudo apt upgrade
```

After confirmation, APT downloaded and installed the newer versions of the packages.

The general workflow is:

```text
apt update
    ↓
Refresh package indexes
    ↓
apt list --upgradable
    ↓
Check available upgrades
    ↓
apt upgrade
    ↓
Upgrade installed packages
```

---

## 4. Searching for Packages

APT can be used to search for available packages:

```bash
apt search tree
```

This searches the package indexes for packages matching the specified term.

---

## 5. Inspecting Package Information

Before installing a package, information can be displayed with:

```bash
apt show tree
```

This provides information such as:

* Package name
* Version
* Architecture
* Dependencies
* Description

This is useful for inspecting a package before installation.

---

## 6. Installing a Package

The `tree` package was used as a practical example:

```bash
sudo apt install tree
```

After installation, the command was tested:

```bash
tree ~
```

This demonstrated the basic package installation workflow.

---

## 7. Removing a Package

The package was subsequently removed:

```bash
sudo apt remove tree
```

The command confirmed that the package was removed and freed disk space.

Testing the command afterwards:

```bash
tree
```

returned:

```text
-bash: /usr/bin/tree: No such file or directory
```

This confirmed that the package had been successfully removed.

---

## 8. Automatic Dependencies

During package removal, APT reported:

```text
The following packages were automatically installed and are no longer required:
```

This introduces the concept of automatically installed dependencies.

APT can identify packages that are no longer required with:

```bash
sudo apt autoremove
```

`apt remove` removes the requested package.

`apt autoremove` removes packages that APT considers no longer required as dependencies.

---

## 9. Ubuntu Repositories

The configured Ubuntu package sources were inspected with:

```bash
grep -v '^#' /etc/apt/sources.list.d/ubuntu.sources
```

The server uses the following repositories:

```text
http://archive.ubuntu.com/ubuntu/
```

with:

```text
noble
noble-updates
noble-backports
```

and:

```text
http://security.ubuntu.com/ubuntu/
```

with:

```text
noble-security
```

The configured components are:

```text
main
restricted
universe
multiverse
```

### Repository Components

| Component    | Purpose                                                        |
| ------------ | -------------------------------------------------------------- |
| `main`       | Officially supported Ubuntu software                           |
| `restricted` | Supported software with licensing or distribution restrictions |
| `universe`   | Community-maintained software                                  |
| `multiverse` | Software with licensing or other restrictions                  |

### Repository Suites

| Suite             | Purpose                                           |
| ----------------- | ------------------------------------------------- |
| `noble`           | Base Ubuntu 24.04 repository                      |
| `noble-updates`   | Updates released for Ubuntu 24.04                 |
| `noble-security`  | Security updates                                  |
| `noble-backports` | Selected newer software backported to the release |

---

## 10. APT and DPKG

Ubuntu uses the `.deb` package format.

`dpkg` is the lower-level package management system, while APT provides a higher-level interface for managing packages and dependencies.

A simplified relationship is:

```text
Ubuntu Repositories
        ↓
      APT
        ↓
     DPKG
        ↓
    .deb packages
```

For normal package administration, APT is generally the preferred tool.

---

## Commands Practiced

| Command                      | Purpose                         |
| ---------------------------- | ------------------------------- |
| `sudo apt update`            | Update package indexes          |
| `apt list --upgradable`      | List available package upgrades |
| `sudo apt upgrade`           | Upgrade installed packages      |
| `apt search <package>`       | Search for packages             |
| `apt show <package>`         | Display package information     |
| `sudo apt install <package>` | Install a package               |
| `sudo apt remove <package>`  | Remove a package                |
| `sudo apt autoremove`        | Remove unused dependencies      |

---

## Key Takeaways

* `apt update` updates package indexes; it does not install updates.
* `apt upgrade` installs available updates.
* `apt search` can be used to find packages.
* `apt show` provides package information before installation.
* `apt install` installs packages and required dependencies.
* `apt remove` removes installed packages.
* `apt autoremove` can remove dependencies that are no longer required.
* Ubuntu obtains packages from configured repositories.
* APT operates at a higher level while DPKG handles `.deb` packages at a lower level.
* Repository components such as `main`, `restricted`, `universe`, and `multiverse` define different categories of available software.

---

## Lab Status

**Completed**

The lab established the basic package-management workflow required for administering Ubuntu Server.

**Next:** Lab 06 — Web Server
