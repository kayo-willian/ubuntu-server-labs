# Lab 06 — Web Server

## Objective

Deploy a basic web server on Ubuntu Server using Nginx and make it accessible from the host machine through VirtualBox port forwarding.

The lab covers service management, TCP ports, HTTP requests, Nginx configuration files, and basic web content deployment.

---

## Environment

| Item             | Configuration             |
| ---------------- | ------------------------- |
| Operating System | Ubuntu Server 24.04.4 LTS |
| Virtualization   | VirtualBox                |
| Web Server       | Nginx                     |
| Protocol         | HTTP                      |
| Server Port      | TCP 80                    |
| Host Port        | TCP 8080                  |
| Port Forwarding  | VirtualBox NAT            |

---

## 1. Installing Nginx

Nginx was installed using APT:

```bash
sudo apt install nginx
```

After installation, the service was checked with:

```bash
sudo systemctl status nginx
```

The service was successfully running:

```text
Active: active (running)
```

The service was also shown as:

```text
enabled
```

meaning Nginx is configured to start automatically during system boot.

---

## 2. Testing the Web Server Locally

The Nginx server was tested directly from the Ubuntu Server:

```bash
curl http://localhost
```

The server returned the default Nginx HTML page.

This confirmed that Nginx was installed correctly and responding to HTTP requests locally.

---

## 3. Checking Listening Ports

The server's listening TCP ports were inspected with:

```bash
sudo ss -tlnp
```

Nginx was found listening on:

```text
0.0.0.0:80
[::]:80
```

A more specific query was also used:

```bash
sudo ss -tlnp | grep nginx
```

Result:

```text
LISTEN  0  511  0.0.0.0:80  0.0.0.0:*  users:(("nginx"...))
LISTEN  0  511  [::]:80     [::]:*     users:(("nginx"...))
```

This confirmed that Nginx was listening for HTTP connections on TCP port 80.

---

## 4. VirtualBox Port Forwarding

Because the virtual machine was using NAT networking, a VirtualBox port forwarding rule was configured.

| Field      | Value       |
| ---------- | ----------- |
| Name       | HTTP        |
| Protocol   | TCP         |
| Host IP    | `127.0.0.1` |
| Host Port  | `8080`      |
| Guest IP   | Default     |
| Guest Port | `80`        |

This created the following path:

```text
Host Machine
127.0.0.1:8080
       │
       │ VirtualBox NAT
       ▼
Ubuntu Server
      :80
       │
       ▼
     Nginx
```

---

## 5. Accessing Nginx from the Host

The web server was successfully accessed from the host machine using a web browser:

```text
http://127.0.0.1:8080
```

The default Nginx page was displayed successfully.

This confirmed that the service was not only working locally, but was also reachable through the VirtualBox port forwarding configuration.

---

## 6. Nginx Web Root

The default Nginx web directory was inspected:

```bash
ls -l /var/www/html
```

The directory contained:

```text
index.nginx-debian.html
```

The default page was inspected with:

```bash
sudo cat /var/www/html/index.nginx-debian.html
```

This demonstrated that Nginx serves web content from `/var/www/html`.

---

## 7. Creating a Custom Web Page

A custom HTML page was created:

```bash
sudo nano /var/www/html/index.html
```

The following content was used:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Ubuntu Server Lab</title>
</head>
<body>
    <h1>Ubuntu Server</h1>
    <p>Nginx is running successfully.</p>
    <p>Lab 06 - Web Server</p>
</body>
</html>
```

After creating the page, the server was tested locally:

```bash
curl http://localhost
```

The custom HTML content was returned successfully.

---

## 8. Final Browser Test

The custom page was accessed from the host machine:

```text
http://127.0.0.1:8080
```

The browser displayed:

```text
Ubuntu Server

Nginx is running successfully.

Lab 06 - Web Server
```

This confirmed the complete communication path from the host machine to the web server running inside the Ubuntu Server VM.

---

## Communication Flow

```text
Web Browser
     │
     │ HTTP :8080
     ▼
127.0.0.1
     │
     │ VirtualBox Port Forwarding
     ▼
Ubuntu Server VM
     │
     │ TCP :80
     ▼
   Nginx
     │
     ▼
/var/www/html/index.html
     │
     ▼
   HTML Response
```

---

## Commands Practiced

| Command                       | Purpose                                    |
| ----------------------------- | ------------------------------------------ |
| `sudo apt install nginx`      | Install Nginx                              |
| `sudo systemctl status nginx` | Check Nginx service status                 |
| `curl http://localhost`       | Test HTTP locally                          |
| `sudo ss -tlnp`               | Display listening TCP sockets              |
| `sudo ss -tlnp \| grep nginx` | Display Nginx listening sockets            |
| `ls -l /var/www/html`         | List web root contents                     |
| `sudo cat <file>`             | Read a protected file                      |
| `sudo nano <file>`            | Edit a file with administrative privileges |

---

## Key Takeaways

* Nginx is a web server capable of serving HTTP content.
* Services can be managed with `systemctl`.
* TCP port 80 is used by the Nginx HTTP service in this lab.
* `ss` can be used to identify listening network services.
* `curl` can test an HTTP service directly from the server.
* `/var/www/html` is the web root used for the default Nginx configuration in this lab.
* VirtualBox NAT can expose a service from a VM to the host through port forwarding.
* The host port and guest port do not need to be the same.
* A service being accessible locally does not automatically mean it is accessible from another machine.

---

## Lab Status

**Completed**

A functional Nginx web server was deployed on Ubuntu Server, tested locally, exposed through VirtualBox port forwarding, and accessed successfully from the host machine.
