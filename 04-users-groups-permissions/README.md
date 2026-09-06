# Lab 04 — Users, Groups & Permissions

## Objective

Learn the fundamentals of user management, groups, file permissions, ownership, shared directories, SGID, and administrative privileges on Ubuntu Server.

The lab focuses on understanding how Linux controls access to resources and how groups can be used to provide shared access without granting administrative privileges.

---

## 1. Current User and Privileges

The existing administrative user was inspected with:

```bash
whoami
id
groups
```

The `kayo` user was identified as a member of the `sudo` group.

This allows administrative commands to be executed through `sudo`.

Example:

```bash
sudo systemctl status ssh
```

The important distinction is:

* `kayo` has administrative privileges through `sudo`.
* A regular user does not automatically have administrative privileges.

---

## 2. Creating a User

A dedicated laboratory user was created:

```bash
sudo adduser labuser
```

The command created:

* User: `labuser`
* UID: `1001`
* Primary group: `labuser`
* Home directory: `/home/labuser`

The user was inspected with:

```bash
id labuser
groups labuser
```

Example result:

```text
uid=1001(labuser) gid=1001(labuser) groups=1001(labuser),100(users)
```

The home directory permissions were checked with:

```bash
ls -ld /home/labuser
```

Result:

```text
drwxr-x--- 2 labuser labuser ... /home/labuser
```

This means:

| Entity | Permissions |
| ------ | ----------- |
| Owner  | `rwx`       |
| Group  | `r-x`       |
| Others | `---`       |

Therefore, other users cannot access the directory through normal permissions.

---

## 3. Testing User Access

A shell was started as `labuser`:

```bash
sudo -u labuser -i
```

The user and current directory were checked:

```bash
whoami
pwd
```

The session initially remained in `/home/kayo`, demonstrating that changing the user does not automatically change the current working directory.

After moving to the user's home:

```bash
cd ~
```

the current directory became:

```text
/home/labuser
```

A file was created:

```bash
touch test.txt
```

The file was verified:

```bash
ls -l
```

Result:

```text
-rw-rw-r-- 1 labuser labuser ... test.txt
```

---

## 4. File Permissions with chmod

The file permissions were changed using:

```bash
chmod 600 test.txt
```

The result was:

```text
-rw------- 1 labuser labuser ... test.txt
```

The numeric permission system was examined.

| Permission    | Value |
| ------------- | ----: |
| `r` — Read    |     4 |
| `w` — Write   |     2 |
| `x` — Execute |     1 |
| No permission |     0 |

Therefore:

```text
rw- = 6
r-- = 4
r-x = 5
rwx = 7
```

The permission:

```text
600
```

means:

```text
Owner  → rw-
Group  → ---
Others → ---
```

The file was then populated:

```bash
echo "Labuser's Private Archive" > test.txt
```

As `labuser`, the file could be read:

```bash
cat test.txt
```

After returning to `kayo`, a normal access attempt failed:

```bash
cat /home/labuser/test.txt
```

Result:

```text
Permission denied
```

However, administrative access succeeded:

```bash
sudo cat /home/labuser/test.txt
```

This demonstrated the difference between normal file permissions and administrative privileges.

---

## 5. Creating a Group

A group was created for shared project access:

```bash
sudo groupadd developers
```

The group was verified:

```bash
getent group developers
```

Example:

```text
developers:x:1002:
```

---

## 6. Adding a User to a Group

`labuser` was added to the `developers` group:

```bash
sudo usermod -aG developers labuser
```

The membership was verified:

```bash
groups labuser
```

Result:

```text
labuser : labuser users developers
```

The `-aG` options are important:

* `-a` — append to existing supplementary groups
* `-G` — specify supplementary groups

Using `-aG` prevents accidentally replacing the user's existing supplementary group memberships.

---

## 7. Creating a Shared Project Directory

A shared project directory was created:

```bash
sudo mkdir /srv/project
```

Ownership was configured:

```bash
sudo chown kayo:developers /srv/project
```

Permissions were then configured:

```bash
sudo chmod 770 /srv/project
```

The final state was verified:

```bash
ls -ld /srv/project
```

Result:

```text
drwxrwx--- 2 kayo developers ... /srv/project
```

This means:

| Entity               | Permissions |
| -------------------- | ----------- |
| Owner (`kayo`)       | `rwx`       |
| Group (`developers`) | `rwx`       |
| Others               | `---`       |

Because `labuser` belongs to `developers`, the user can access the directory without being an administrator.

---

## 8. Testing Group-Based Access

A new session was started as `labuser`:

```bash
sudo -u labuser -i
```

Group membership was confirmed:

```bash
id
```

The result included:

```text
groups=1001(labuser),100(users),1002(developers)
```

The user was then able to access the shared directory:

```bash
cd /srv/project
```

and create a file:

```bash
touch project.txt
```

The file initially inherited the user's primary group:

```text
-rw-rw-r-- 1 labuser labuser ... project.txt
```

This demonstrated that simply being a member of `developers` does not automatically make newly created files belong to that group.

---

## 9. Changing Group Ownership with chgrp

The group of `project.txt` was changed manually:

```bash
sudo chgrp developers /srv/project/project.txt
```

The result was verified:

```bash
ls -l /srv/project/project.txt
```

Result:

```text
-rw-rw-r-- 1 labuser developers ... project.txt
```

`chgrp` changes the group ownership of a file or directory.

---

## 10. Changing Ownership with chown

The ownership of `project.txt` was then changed:

```bash
sudo chown kayo:developers /srv/project/project.txt
```

The result:

```text
-rw-rw-r-- 1 kayo developers ... project.txt
```

The command follows the structure:

```bash
chown USER:GROUP FILE
```

In this case:

```text
Owner → kayo
Group → developers
```

The main ownership commands covered in this lab are:

| Command | Purpose                   |
| ------- | ------------------------- |
| `chmod` | Change permissions        |
| `chown` | Change owner and/or group |
| `chgrp` | Change group ownership    |

---

## 11. SGID on a Shared Directory

To prevent manually changing the group of every new file, the shared directory was configured with SGID.

The permission was changed to:

```bash
sudo chmod 2770 /srv/project
```

The `2` enables the SGID (Set Group ID) bit on the directory.

The result was:

```bash
ls -ld /srv/project
```

```text
drwxrws--- 2 kayo developers ... /srv/project
```

The `s` in the group permission indicates SGID is active.

With SGID enabled, new files created inside `/srv/project` inherit the directory's group.

---

## 12. Verifying SGID

A new file was created as `labuser`:

```bash
touch group-test.txt
```

The file was verified:

```bash
ls -l
```

Result:

```text
-rw-rw-r-- 1 labuser developers ... group-test.txt
```

This demonstrated the effect of SGID.

Before SGID:

```text
project.txt
Owner: labuser
Group: labuser
```

After SGID:

```text
group-test.txt
Owner: labuser
Group: developers
```

The owner is still the user who created the file, but the group is inherited from the shared directory.

Existing files are not automatically changed by SGID. This is why older files such as `project.txt` required a separate `chgrp` or `chown` operation.

---

## 13. Sudo and Least Privilege

The difference between a normal user and an administrator was tested.

As `labuser`:

```bash
sudo id
```

Result:

```text
labuser is not in the sudoers file.
```

This confirms that `labuser` does not have administrative privileges.

However, `labuser` can still work inside `/srv/project` because access was granted through the `developers` group.

This demonstrates the principle of **least privilege**:

> Users should receive only the permissions required to perform their tasks.

The resulting access model is:

```text
kayo
├── Member of sudo
├── Administrative privileges
└── Owner of /srv/project

labuser
├── Regular user
├── Member of developers
├── No sudo privileges
└── Access to /srv/project
```

---

## 14. Final Access Model

The lab created a basic shared-project permission model:

```text
                    /srv/project
                         │
              ┌──────────┴──────────┐
              │                     │
          kayo                   developers
        Owner + Admin               Group
              │                     │
              └──────────┬──────────┘
                         │
                     labuser
                         │
                  Regular User
                  No sudo access
```

The directory uses:

```text
Owner:       kayo
Group:       developers
Permissions: 770
SGID:        enabled
```

This allows members of `developers` to collaborate inside the directory while preventing access from other users.

---

## Commands Practiced

```bash
whoami
id
groups
adduser
groupadd
usermod -aG
mkdir
chmod
chown
chgrp
ls -l
ls -ld
getent group
sudo
sudo -u
```

---

## Key Takeaways

* Linux identifies users and groups using UID and GID values.
* Every file has an owner and a group.
* `chmod` controls permissions.
* `chown` changes ownership.
* `chgrp` changes group ownership.
* Groups are useful for managing shared access.
* `sudo` provides administrative privileges without requiring a user to log in as `root`.
* SGID directories allow new files to inherit the directory's group.
* A user can have access to a shared resource without being an administrator.
* Least privilege is an important principle for server security.

This lab establishes the foundation for managing users, groups, permissions, and access control on Ubuntu Server.
