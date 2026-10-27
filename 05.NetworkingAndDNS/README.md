# Networking & DNS

🕒 **Duration**: 3 days

Study the following topics in depth. It is recommended to write your own summary.

> **The test is oral, closed-book (no open material).**

> **Important - for this test and for all tests in general:**
> Whenever an English acronym appears, you are expected to know what it stands for.

### Goals
- The trainee will understand MAC and IP addressing
- The trainee will understand the OSI and TCP/IP models
- The trainee will understand the core network protocols in depth
- The trainee will understand common networking concepts and components

### MAC and IP addresses
- MAC addresses and IP addresses
- The IP address problem, and 3 solutions to it - for each solution, explain how it solves the problem

### OSI vs TCP/IP
- The names and the order of the layers
- Explain each layer, with an example protocol
- The differences between the 2 models, and the advantages and disadvantages of each

### Protocols
For each protocol, you need to know what the acronym stands for, and its port.
- **DNS** - in-depth explanation
- **SSH / RDP** - explanation, and the differences between them
- **TCP** - in-depth explanation and flow, flags, and checksum
- **UDP**
- **TCP vs UDP** - the differences, and the advantages / disadvantages of each
- **IP**
- **ICMP**
- **Ethernet**
- **NAT** - in-depth explanation and flow, and what the NAT table looks like

### Subnetting and CIDR
- What does an IPv4 address look like in binary? What are the network part and the host part?
- What is **CIDR** notation (e.g. `10.1.0.0/20`)? What does the `/20` mean?
- What is a **subnet mask**? Write `/20` as a dotted-decimal mask, and as binary
- For a given network, find:
  - the network address, the broadcast address, and the first and last usable addresses
  - the number of usable hosts (why is it `2^(32-prefix) - 2` and not `2^(32-prefix)`?)
- Calculate by hand, without a tool:
  - How many usable hosts are in a `/26`? In a `/29`?
  - Split `10.0.0.0/16` into 4 equal subnets. What are their prefixes and ranges?
  - Is `10.1.2.3` inside `10.1.0.0/20`? How do you check it?
  - Do `10.0.0.0/23` and `10.0.1.128/25` overlap? How do you know?
- What is **VLSM** (variable-length subnet masks)? When would you use different prefixes in the same network?
- What are the **private** IPv4 ranges (RFC 1918)? Why can't you route them on the internet?
- What is a **default route** (`0.0.0.0/0`)? How does a router pick a route when several match? (**longest prefix match**)
- What is **route summarization** (supernetting)? Why does it keep routing tables small?
- Why must subnets in the same network **not overlap**? What happens when two VPCs or two networks with the same range are connected (e.g. with VPN or peering)?
- Where do you use subnetting in cloud networks? Give examples from Google Cloud:
  - VPC subnets and their primary range
  - GKE pod and service secondary ranges
  - a **proxy-only subnet** for internal load balancers
  - firewall source ranges, e.g. the IAP range `35.235.240.0/20` and the health-check ranges
- Use a tool to check your answers: Python's `ipaddress` module, or `ipcalc` (`ipcalc 10.1.0.0/20`)

### Concepts
- **Firewall** - types
- **LB**
- **VPN** and **Proxy**, and the differences between them
- **IPAM**
