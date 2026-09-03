# Lab 02 - Ubuntu Server Networking

## Objective

Understand the basic network configuration and operation of an Ubuntu Server, including network interfaces, IP addressing, routing, DNS, connectivity testing, and remote administration through SSH.

The objective of this lab was not to modify the network configuration, but to understand how Ubuntu Server handles networking and verify that the server was correctly connected and reachable.

---

## Environment

The Ubuntu Server VM was configured using VirtualBox with a NAT network adapter.

The server received its network configuration automatically through DHCP.

---

## 1. Network Interface Identification

### `ip addr`

The `ip addr` command displays network interfaces and IP addresses configured on the Linux system.

I used this command to identify the network interface assigned to the Ubuntu Server VM and verify its IPv4 address.

The main interface identified was:

```text
enp0s3
```

The server received the following IPv4 address:

```text
10.0.2.15/24
```

The loopback interface was also present:

```text
lo
127.0.0.1/8
```

The `enp0s3` interface was active and configured through DHCP.

In Cisco IOS, a conceptual equivalent for quickly checking interfaces and their IP addresses would be:

```text
show ip interface brief
```

The commands are not equivalent in syntax or output, but both are commonly used to obtain a quick view of network interfaces and addressing.

---

## 2. Routing Table

### `ip route`

The `ip route` command displays the routing table used by the Linux system.

The server had the following relevant routes:

```text
default via 10.0.2.2 dev enp0s3
10.0.2.0/24 dev enp0s3
```

The default gateway was:

```text
10.0.2.2
```

The directly connected network was:

```text
10.0.2.0/24
```

The default route allows traffic destined for networks outside the local `10.0.2.0/24` network to be forwarded through `10.0.2.2`.

A conceptual comparison in Cisco IOS would be:

```text
show ip route
```

---

## 3. Connectivity Testing

Network connectivity was tested at different levels to determine whether the server could communicate with the local network, external IP addresses, and domain names.

### Local gateway

```bash
ping -c 4 10.0.2.2
```

The test was successful with no packet loss.

This verified connectivity with the configured default gateway.

### External IP address

```bash
ping -c 4 8.8.8.8
```

The test was successful with:

```text
0% packet loss
```

This confirmed that the server could reach an external IP address.

### Domain name

```bash
ping -c 4 google.com
```

The test was also successful.

This was important because it tested not only network connectivity, but also DNS name resolution.

---

## 4. DNS Configuration

### `resolvectl status`

The `resolvectl status` command was used to inspect the DNS configuration currently being used by the system.

The configured DNS server was:

```text
10.0.2.3
```

The DNS search domain was:

```text
home
```

Since `ping google.com` was successful, DNS resolution was working correctly.

---

## 5. Netplan Configuration

Ubuntu Server uses Netplan as the configuration layer for network interfaces.

The available Netplan configuration file was identified with:

```bash
ls /etc/netplan/
```

The configuration was then inspected with:

```bash
sudo cat /etc/netplan/50-cloud-init.yaml
```

The configuration contained:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
```

This configuration means that the `enp0s3` interface obtains its IPv4 configuration automatically through DHCP.

No manual static IP configuration was applied during this lab.

---

## 6. Network Status and `networkctl`

### `sudo netplan status`

The command was used to verify the current state of the Netplan configuration.

### `networkctl status enp0s3`

The `networkctl` command provided additional information about the network interface.

The interface was reported as:

```text
State: routable (configured)
```

The interface was online and had DHCP4 configured.

Relevant information included:

```text
DHCP4: 10.0.2.2
DNS: 10.0.2.3
Driver: e1000
Speed: 1Gbps
Duplex: full
MTU: 1500
```

This provided a more detailed view of how the network interface was operating.

---

# 7. Remote Administration with SSH

After verifying the server's network configuration, the next step was to access the Ubuntu Server remotely instead of interacting with the VM console.

OpenSSH Server had been installed during the Ubuntu Server installation.

### Checking the SSH service

The service was initially found to be inactive:

```bash
sudo systemctl status ssh
```

The relevant state was:

```text
Active: inactive (dead)
```

This demonstrated an important distinction:

Installing a service does not necessarily mean that the service is currently running.

The SSH service was started with:

```bash
sudo systemctl start ssh
```

After starting it, the status was checked again:

```bash
sudo systemctl status ssh
```

The service was then:

```text
Active: active (running)
```

The output also confirmed that `sshd` was listening on port 22:

```text
Server listening on 0.0.0.0 port 22.
Server listening on :: port 22.
```

---

## 8. Verifying the SSH Listening Port

The listening sockets were verified with:

```bash
sudo ss -tlnp | grep :22
```

The result showed:

```text
LISTEN ... 0.0.0.0:22
LISTEN ... [::]:22
```

This confirmed that the SSH server was listening for incoming connections on TCP port 22.

The `ss` command is useful when troubleshooting services because it allows the administrator to verify whether a process is actually listening on the expected network port.

---

# 9. VirtualBox NAT Port Forwarding

The Ubuntu Server VM uses a NAT network.

Because of this, the Ubuntu host cannot simply connect directly to the guest's `10.0.2.15` address as though both machines were connected to the same physical network.

A VirtualBox NAT port forwarding rule was therefore configured to expose the SSH service.

The forwarding was configured as:

```text
Host: 127.0.0.1:2222
        |
        v
Guest: 10.0.2.15:22
```

This means that a connection made to port `2222` on the Ubuntu host is forwarded by VirtualBox to port `22` on the Ubuntu Server.

---

# 10. SSH Troubleshooting

The first attempt to connect remotely failed:

```bash
ssh -p 2222 kayo@127.0.0.1
```

The connection returned:

```text
kex_exchange_identification: read: Connection reset by peer
Connection reset by 127.0.0.1 port 2222
```

A connectivity test was also performed against the forwarded port:

```bash
nc -vz 127.0.0.1 2222
```

The result indicated that the connection was being refused.

At this point, the SSH service inside the server had already been verified as running and listening on port 22.

The problem was ultimately identified as an incorrect port configuration in the VirtualBox port forwarding rule.

After correcting the forwarded port, the SSH connection was attempted again.

---

# 11. Successful Remote Access

The final SSH connection was successful using:

```bash
ssh -p 2222 kayo@127.0.0.1
```

After authentication, the server displayed:

```text
Welcome to Ubuntu 24.04.4 LTS
```

The shell prompt changed to:

```text
kayo@ubuntu-server:~$
```

This confirmed that the terminal session was now running on the Ubuntu Server.

The important distinction is:

```text
kayo@ubuntu:~$
```

represents the Ubuntu host machine, while:

```text
kayo@ubuntu-server:~$
```

represents the Ubuntu Server running inside the VM.

From this point forward, the Ubuntu Server will be administered primarily through SSH from the terminal of the host PC.

---

# Troubleshooting Summary

| Problem                             | Investigation                           | Solution                                  |
| ----------------------------------- | --------------------------------------- | ----------------------------------------- |
| SSH service was inactive            | `systemctl status ssh`                  | Started with `systemctl start ssh`        |
| SSH connection was being reset      | Checked SSH service and listening ports | Confirmed `sshd` was listening on port 22 |
| Host could not connect to port 2222 | `nc -vz 127.0.0.1 2222`                 | Corrected VirtualBox port forwarding      |
| SSH connection succeeded            | `ssh -p 2222 kayo@127.0.0.1`            | Remote administration established         |

---

# Key Takeaways

* Linux network interfaces can be inspected with `ip addr`.
* Linux routing information can be inspected with `ip route`.
* The default gateway determines where traffic outside the local network is forwarded.
* DNS configuration can be inspected with `resolvectl status`.
* Netplan defines the network configuration applied to Ubuntu Server.
* `networkctl` provides detailed information about the state of network interfaces.
* A service can be installed without currently being active.
* `systemctl` is used to inspect and manage system services.
* `ss` can be used to verify whether a service is listening on a network port.
* SSH provides remote command-line access to the server.
* VirtualBox NAT requires port forwarding when the host needs to access a service running inside the guest.
* Troubleshooting should isolate each layer of the connection instead of changing multiple configurations at once.
* The Ubuntu Server can now be administered remotely from the host PC terminal through SSH.

---

## Lab Status

**Completed**

The basic Ubuntu Server networking environment was inspected and validated, and remote administration through SSH was successfully established.

Further server administration tasks will be performed remotely through the SSH terminal rather than through the VM console.
