<a id="title">
  
# Network Mastery

This write up is my personal understanding of the network, and how it functions at it's core. I will use this as a reference point in my future career to avoid inaccurate data. As with all write-ups, I will constantly update as I develop deeper understandings of the topic. 

Ideally, this document includes everything needed to master the concepts of the Network. This should help me easily pass the Network+ exam. 

### For Quick Navigation:

- [OSI + TCP/IP Models](#osi)
- [Network Media & Data Flow](#ethernet)
- [IP Addressing & Subnetting](#ip)
- [Layer 2 – Switching & ARP](#switch)
- [ICMP (Ping, Traceroute, Diagnostics)](#icmp)
- [TCP & UDP Fundamentals](#tcpudp)
- [DNS Deep Dive](#dns)
- [Dynamic Host Configuration Protocol (DHCP)](#dhcp)
- [Network Translation (NAT + PAT)](#network)
- [Firewalls](#firewalls)
- [VPN Fundamentals](#vpn)
- [Essential Network Tools](#tools)

Use the "Back to Top" links, found at the bottom of each section (or the top, depending on your left brained-ness) to return here, anytime. No endless scrolling. 

---

<a id="osi">

## OSI + TCP/IP MODELS

**TCP/IP Model:** Practical 4-layer model reflecting real Internet protocols (e.g., TCP, IP). Describes actual end-to-end data flow.

**OSI Model:** Conceptual 7-layer reference model for standardizing interconnection. Primarily for learning, design, and troubleshooting — not a strict implementation.

> [!Important]
> Both models describe the same concrete network behavior. Layering exists to manage complexity: isolate responsibilities for independent development, scaling, debugging, and interoperability.

**So what's the purpose of layering?** 

The network is extremely complex, so separating responsibilities and behaviors allows complex communication systems to be built, scaled, changed, and debugged without everything breaking. 

| OSI Model (7 Layers)        | TCP/IP Model (4 Layers)         |
|----------------------------|----------------------------------|
| 7 – Application            | 4 - Application                  |
| 6 – Presentation           | 4 - Application                  |
| 5 – Session                | 4 - Application                  |
| 4 – Transport              | 3 - Transport                    |
| 3 – Network                | 2 - Internet                     |
| 2 – Data Link              | 1 - Network Access               |
| 1 – Physical               | 1 - Network Access               |

### OSI Layer Roles

**Focus:** What problem each solves. **Data flow:** Sending (L7 → L1), Receiving (L1 → L7).

- L7 Application: Generates/consumes user data; defines service/protocol (e.g., HTTP request). Problem solved: Human/machine interaction — what to communicate.
- L6 Presentation: Formats, encodes, compresses, encrypts data for interoperability/privacy (e.g., JPEG, TLS encryption). Problem solved: Ensure data is readable/secure across systems. Often absorbed by applications/TLS today.
- L5 Session: Manages dialog lifecycle (establish, maintain, terminate; checkpoints/resync). Problem solved: Coordinate full conversations. Often absorbed by applications (e.g., WebSockets) or Transport (TCP).
- L4 Transport: Provides end-to-end app delivery; adds ports, optional reliability/ordering/flow control (TCP vs UDP). Problem solved: Multiplex apps; control delivery quality.
- L3 Network: Logical addressing (IP); path selection across networks (routing). Problem solved: Deliver beyond local network; scalable internetworking.
- L2 Data Link: Local framing; MAC addressing; basic error detection (FCS); next-hop delivery. Problem solved: Reliable transfer between adjacent devices on same medium.
- L1 Physical: Bit transmission via signals (electrical/optical/radio). Problem solved: Actual movement over media.

### Encapsulation & Decapsulation

Same payload nests deeper on send; unwraps on receive.

#### Sending (Encapsulation):

L7: Raw application data.
L4: Add Transport header (ports, seq/ACK if TCP) → Segment.
L3: Add IP header (source/dest IP, TTL) → Packet.
L2: Add MAC header (source/dest MAC) + FCS trailer → Frame.
L1: Encode as bits/signals.

#### Receiving (Decapsulation):

L1: Receive signals → bits.
L2: Validate FCS, strip MAC → Packet.
L3: Process IP, route if needed → Segment.
L4: Process ports/reliability → Data.
L7: Deliver to application.

Payload unchanged; headers provide context per layer.

#### Key Header Fields (Essentials)

Transport (L4): Source/dest ports (multiplex applications).
Network (L3): Source/dest IP, TTL (prevents loops).
Data Link (L2): Source/dest MAC, EtherType (identifies payload protocol), FCS (error detection trailer).

<a id="pdu">
  
### Protocol Data Units (PDUs)

PDUs name the data unit at each layer as headers are added/removed.

| Layer    | PDU Name | Contents Added                          |
|----------|----------|-----------------------------------------|
| 7–5      | Data     | Raw application payload                 |
| 4        | Segment  | Data + Transport header (ports, etc.)   |
| 3        | Packet   | Segment + IP header (IPs, TTL)          |
| 2        | Frame    | Packet + MAC header + FCS               |
| 1        | Bits     | Frame encoded as signals                |

### Troubleshooting Tie-In

| Layer | Common Protocols/Examples | Tools/Examples                  |
|-------|---------------------------|---------------------------------|
| 7     | HTTP, SMTP                | Browser, curl                   |
| 6     | TLS encryption            | -                               |
| 5     | RPC                       | -                               |
| 4     | TCP, UDP                  | netstat/ss (connections/ports)  |
| 3     | IP, ICMP                  | ping, traceroute                |
| 2     | Ethernet                  | arp, switch MAC table           |
| 1     | -                         | Cable tester, signal analyzer   |

[Back to Top](#title)

<a id="ethernet">
  
## Physical Media & Ethernet (L1 + L2 Local Delivery)

Local (LAN) delivery happens at L1 (physical signals) and L2 (framing/MAC). All local methods use Ethernet-style frames; differences are in physical medium and access control.

### Ethernet Frame Basics
- Unit: Frame (L2 PDU).
- Key fields: Dest/src MAC (48-bit hardware address), EtherType (payload type, e.g., 0x0800 = IPv4), payload, FCS (error check).
- Purpose: Deliver frames to next hop on same network using MAC.

### MAC Addresses
- 48-bit (6 bytes), globally unique.
- First 3 bytes: OUI (vendor), last 3: device-specific.
- Local scope only — changes at each router hop.

### Wired Ethernet (Copper/Fiber)
Dedicated medium → no contention.

| Medium | Categories/Types | Max Speed | Max Distance | Use Case |
|--------|------------------|-----------|--------------|----------|
| Copper | Cat5e | 1 Gbps | 100m | Desktops |
| Copper | Cat6/6a | 10 Gbps | 100m/55m | Data centers |
| Copper | Cat8 | 40 Gbps | 30m | High-speed |
| Fiber (MMF) | OM3/OM4/OM5 | 10–100 Gbps | 300–550m | Campus/building |
| Fiber (SMF) | OS1/OS2 | 10–400 Gbps | 10–80km | Long-haul |

- Duplex: Full (simultaneous send/receive).
- Access: None needed (switched = point-to-point).
- Connectors: RJ-45 (copper), LC/SC (fiber).

### Wireless (Wi-Fi — 802.11)
Shared radio medium → contention.

- Standards: a/b/g/n/ac/ax/be (progressive speed/MIMO/channel improvements).
- Bands: 2.4 GHz (range, crowded), 5/6 GHz (speed, less range).
- Access: CSMA/CA (listen before talk, backoff on collision).
- Duplex: Full-duplex capable (MIMO), but contention reduces effective throughput.
- Challenges: Interference, distance, obstacles → variable latency/speed.

| Aspect | Wired Ethernet | Wi-Fi |
|--------|----------------|-------|
| Medium | Dedicated | Shared radio |
| Duplex | Full | Full capable (contention-limited) |
| Reliability | Near 100% | Interference-sensitive |
| Speed (real-world) | Consistent | Variable (up to multi-Gbps today) |
| Advantage | Performance/stability | Mobility |

### Beyond LAN (Brief)
Frames leave LAN via gateway → WAN uplink (fiber/coax/DSL/cellular/satellite) → ISP → backbone (Tier 1 fiber networks). L2 headers change at gateway; IP (L3) remains.

[Back to Top](#title)

<a id="ip">
  
## IP Addressing & Subnetting

In addition to MAC addresses, every device on the network needs an IP address for proper routing. There are two kinds of Ip Addresses: IPv4 (what we currently use), and IPv6 (what we'll eventually move to). Ideally, sometime in the distant future - the network will fully convert to IPv6 for better built-in security and to combat the incoming IPv4 address shortage. 

Most devices support both IPv4 as well as IPv6 - but IPv4 is still the fallback King which most devices expect. See the table below for a quick comparison:

| **Topic**             | **IPv4**                     | **IPv6**                           |
| --------------------- | ---------------------------- | ---------------------------------- |
| **Address size**      | 32-bit                       | 128-bit                            |
| **Total addresses**   | ~4.3 billion                 | ~3.4×10³⁸ (basically unlimited)    |
| **Format**            | Decimal (e.g., 192.168.1.10) | Hexadecimal (e.g., 2001:0db8::1)   |
| **Subnetting**        | CIDR (/0–/32)                | Prefix length (/0–/128)            |
| **Broadcast**         | Uses broadcast               | No broadcast (uses multicast)      |
| **NAT**               | Commonly used                | Not needed (address space is huge) |
| **Configuration**     | Often DHCP                   | Auto-config (SLAAC) + DHCPv6       |
| **Security**          | Optional IPsec               | IPsec built into the protocol      |
| **Header complexity** | Smaller, simpler             | Larger, more efficient routing     |

> [!Important]
> IPv4 addresses are considered 32 bits because each octet translates to binary, meaning each octet becomes 8 binary digits.
> Example: IPv4 address of "192.168.1.10" translates to "11000000.10101000.00000001.00001010" in binary, which is where we get the "32 bits" from. 

### Structure of IPv4 Addressing

Every home network has 3 core addressing attritbutes - private IP, public IP, and your subnet. These attributes are fundamental to how data can be routed to the correct network, and device. 

#### Public IP Addressing

Your **Public IP Address** is the IP address used to send data across the WAN, to different networks - this is the IP address the global network uses to identify **your** LAN. 

When your router goes online, it fires off a request to your ISPs DHCP server, saying "Hey I'm alive and I need a public IP address!" to which the DHCP server responds with an available IP. It's important to note, your public IP is **not** static, it's leased to you on a short term basis, and can dynamically change for a multitude of reasons. Its good practice to always check your public IP (if needed) because there's no guarantee that tomorrow you'll have the same one.

#### Private IP Addressing

Your **Private IP Address** is a pre-determuined IPv4 that the manufacturer bakes into your router's firmware, straight from the factory. It's important to note that this IP is not neccessarily permanent, it *can* be changed - it just typically does not (unless you change it yourself). 

Your private IP address is **only** used *inside* of your local network, meaning - the only devices that communicate with *your* private IP, are *your* devices, and each device has it's own IP address. Private IPs do not need to be *globally* unique, they must only be unique with *your* network. 

You may have the same private IP address as your neighbor, and it wouldn't matter if you did - since your devices are not local to theirs - yours (and his) are technically still unique. 

#### Subnet Masks (Subnetting)

Subnetting simply defines how many bits identify the network, versus the hosts. In simple terms, the more bits we dedicate to the network, the less availability we have for hosts - and remember we need a unique IP for each host (device) connected. 

We signal how many bits are reserved for the network by adding a `/#` to the end of the IP address, the most common being `/24` which means there are 256 unique IP combinations. (binary-options^remaining-bits = total unique IP combos)

`192.168.1.0/24` translates to "The first 24 bits (3 octets) are for the network to identify you correctly, and the remaining are for you: 256 unique combinations"

> [!Important]
> On every `/24` network, there are indeed 256 possible combinations but `.0` is reserved for your network ID (the root IP address, to which all others are built off), and `.255` is for broadcasting to all devices. This technically means there are only 254 available IP addresses for hosts to connect to on your network. (Don't forget, your router needs one too, so in practice there are really only 253 available combos)

[Back to Top](#title)

<a id="switch">

## Layer 2 – Switching & ARP

Layer 2 handles the forwarding of frames on a local network. There are two mediums (along with two devices) that assist in forwarding frames to their destination. 

Host A (sender) > Wireless AP/Switch > Host B (receiver)

See below for a quick comparison:

| Aspect             | Ethernet Switch Port                  | Wireless Access Point (AP)                 |
| ------------------ | ------------------------------------- | ------------------------------------------ |
| Medium             | Wired (copper/fiber)                  | Wireless (radio)                           |
| Connectivity       | One device per port                   | Multiple devices share the medium          |
| Frame Transmission | Sent directly out a specific port     | Broadcast over the air to all clients      |
| Frame Reception    | Only the connected device receives it | All clients hear it; only one processes it |
| MAC Learning       | Learns MAC → port                     | Learns MAC → radio interface               |
| Forwarding Basis   | MAC address table                     | MAC address table                          |
| Collision Handling | None (full-duplex)                    | Required (shared medium)                   |
| Duplex             | Full-duplex                           | Half-duplex                                |
| Core Role          | Deterministic frame forwarding        | Shared-medium frame distribution           |

> [!Important]
> Remember: Duplexing is about whether data can be sent/received at the same time, or not.
> Half-duplex = send OR receive, not both. Full-duplex = send and receive simultaneously. This ties into the earlier context on why Ethernet is typically always faster that Wi-fi.





[Back to Top](#title)

<a id="icmp">
  
## ICMP (Ping, Traceroute, Diagnostics)

[Back to Top](#title)

<a id="tcpudp">
  
## TCP & UDP Fundamentals

[Back to Top](#title)

<a id="dns">
  
## DNS Deep Dive

[Back to Top](#title)
<a id="dhcp">
  
## Dynamic Host Configuration Protocol (DHCP)

[Back to Top](#title)
<a id="network">
  
## Network Translation (NAT + PAT)

[Back to Top](#title)
<a id="firewalls">
  
## Firewalls

[Back to Top](#title)
<a id="vpn">
  
## VPN Fundamentals

[Back to Top](#title)
<a id="tools">
  
## Essential Network Tools

[Back to Top](#title)
