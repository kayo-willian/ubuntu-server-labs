# Ubuntu Server Networking

## Overview

This laboratory introduces basic network configuration and connectivity verification on Ubuntu Server.

The goal is to understand how Ubuntu identifies network interfaces, IP addresses, routing information, default gateways, and DNS configuration, while relating these concepts to network administration practices already explored with Cisco IOS.

The laboratory uses the Ubuntu Server virtual machine configured with VirtualBox NAT.

## Lab Objective

The objectives of this laboratory are to:

* Identify the network interfaces available on Ubuntu Server.
* Identify the IPv4 and IPv6 addresses assigned to a network interface.
* Understand CIDR notation.
* Identify the directly connected network.
* Identify the default gateway.
* Verify connectivity with the local gateway.
* Verify external IP connectivity.
* Verify DNS name resolution.
* Identify the DNS server being used by Ubuntu.
* Relate Linux networking concepts to equivalent concepts already studied with Cisco IOS.

## Network Configuration

The Ubuntu Server network interface identified during the laboratory was:

```text
enp0s3
```

The interface was operational and had the following IPv4 configuration:

```text
10.0.2.15/24
```

The `/24` prefix corresponds to the subnet mask:

```text
255.255.255.0
```

The address was obtained dynamically through DHCP.

## Network Interface Verification

### `ip addr`

The `ip addr` command displays network interfaces and their configured addresses.

It was used to identify the network interface assigned to the Ubuntu Server and verify its IPv4 configuration.

Relevant information observed:

```text
Interface: enp0s3
IPv4:      10.0.2.15/24
State:     UP
```

The system also contains the `lo` loopback interface:

```text
127.0.0.1/8
```

The loopback interface is used for communication within the local system and is different from the physical or virtual network interface used to communicate with other systems.

## Routing Table

### `ip route`

The `ip route` command displays the routes known to the Linux kernel.

The following routes were observed:

```text
default via 10.0.2.2 dev enp0s3
10.0.2.0/24 dev enp0s3
```

The default route indicates that traffic destined for networks not directly known by the server is sent through:

```text
10.0.2.2
```

Therefore, `10.0.2.2` is the current default gateway for the Ubuntu Server.

The route:

```text
10.0.2.0/24 dev enp0s3
```

indicates that the `10.0.2.0/24` network is directly connected through `enp0s3`.

### Relation to Cisco IOS

The concepts are similar to information observed with Cisco IOS:

| Networking Concept       | Linux      | Cisco IOS                 |
| ------------------------ | ---------- | ------------------------- |
| Interface and IP address | `ip addr`  | `show ip interface brief` |
| Routing table            | `ip route` | `show ip route`           |
| Connectivity test        | `ping`     | `ping`                    |

The commands are not identical, but they expose similar networking concepts.

## Connectivity Testing

After identifying the interface, IP address, and default gateway, connectivity was tested progressively.

### Gateway Connectivity

The first test checked communication between the Ubuntu Server and its default gateway:

```bash
ping -c 4 10.0.2.2
```

Result:

```text
4 packets transmitted
4 received
0% packet loss
```

This confirmed that the server could successfully communicate with the gateway.

### External IP Connectivity

The next test used a public IP address:

```bash
ping -c 4 8.8.8.8
```

Result:

```text
4 packets transmitted
4 received
0% packet loss
```

This confirmed that the server had IP connectivity beyond its local network.

At this point, connectivity to the Internet had been verified without depending on DNS name resolution.

## DNS Resolution

IP connectivity alone does not guarantee that domain names can be resolved.

To test DNS resolution, the following command was executed:

```bash
ping -c 4 google.com
```

The server successfully resolved `google.com` to an IPv4 address and received replies.

Observed result:

```text
4 packets transmitted
4 received
0% packet loss
```

This demonstrated that both network connectivity and DNS name resolution were working.

## DNS Configuration

### `resolvectl status`

The `resolvectl status` command was used to inspect the DNS configuration currently used by the system.

The relevant configuration for `enp0s3` was:

```text
Current DNS Server: 10.0.2.3
DNS Servers:       10.0.2.3
DNS Domain:        home
```

Therefore, the Ubuntu Server was using:

```text
10.0.2.3
```

as its DNS server.

This explains why the previous `ping google.com` test succeeded: Ubuntu was able to query its configured DNS server and obtain an IP address for the hostname.

## Connectivity Verification

The tests were performed progressively:

```text
Ubuntu Server
10.0.2.15
      |
      | ping
      v
Default Gateway
10.0.2.2
      |
      | Internet connectivity
      v
8.8.8.8
```

DNS resolution was verified separately:

```text
google.com
     |
     | DNS query
     v
10.0.2.3
     |
     | resolved address
     v
142.250.78.206
```

The exact public IP returned by DNS may change over time.

## Troubleshooting

No network connectivity problems were encountered during this laboratory.

The verification process was intentionally performed in stages rather than immediately testing a domain name.

This approach makes troubleshooting easier because different components can be isolated:

```text
Gateway
   ↓
External IP
   ↓
DNS resolution
   ↓
Hostname connectivity
```

If the gateway test had failed, the investigation would focus first on the local interface, IP configuration, subnet, and virtual network.

If `8.8.8.8` had failed while the gateway worked, the investigation would move toward routing or Internet connectivity.

If `8.8.8.8` worked but `google.com` failed, DNS would become the primary area of investigation.

## Lessons Learned

This laboratory demonstrated that Linux provides direct tools for inspecting the same fundamental networking concepts encountered when working with Cisco devices.

The `ip` command family can be used to inspect interfaces and routing information, while `ping` provides a simple method for verifying connectivity.

The tests also demonstrated why IP connectivity and DNS resolution should be treated as separate troubleshooting steps.

## Key Takeaways

1. `enp0s3` is the network interface used by the Ubuntu Server VM.

2. The server received the IPv4 address `10.0.2.15/24` through DHCP.

3. The `10.0.2.0/24` network is directly connected to `enp0s3`.

4. `10.0.2.2` is the default gateway.

5. `ip addr` can be used to inspect network interfaces and addresses.

6. `ip route` can be used to inspect the Linux routing table.

7. The server successfully communicated with its default gateway.

8. The server successfully reached `8.8.8.8`, confirming external IP connectivity.

9. The server successfully resolved `google.com`, confirming DNS resolution.

10. `resolvectl status` showed `10.0.2.3` as the configured DNS server.

11. Network troubleshooting is easier when connectivity is tested progressively rather than treating the network as a single component.

12. Linux and Cisco IOS use different commands, but the underlying networking concepts remain largely the same.
