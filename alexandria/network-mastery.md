<a id="title">
  
# Network Mastery

This write up is my personal understanding of the network, and how it functions at it's core. I will use this as a reference point in my future career to avoid inaccurate data. As with all write-ups, I will constantly update as I develop deeper understandings of the topic. 

Ideally, this document includes everything needed to master the concepts of the Network. This should help me easily pass the Network+ exam. 

### For Quick Navigation:

- [OSI + TCP/IP Models](#osi)
- [Network Media & Data Flow](#flow)
- [IP Addressing & Subnetting](#ip)
- [Layer 2 – Switching & ARP](#switch)
- [ICMP (Ping, Traceroute, Diagnostics)](#icmp)
- [TCP Fundamentals](#tcp)
- [UDP Fundamentals](#udp)
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

**TCP/IP Model** - (Transmission Control Protocol / Internet Protocol) A practical 4-layer model based on the real protocols that actually run the internet. It describes how data is truly sent end-to-end across networks.

**OSI Model** - (Open Systems Interconnection) A conceptual 7-layer reference model created to standardize how different networking systems communicate. It’s mainly used for learning, design, and troubleshooting, not as an exact implementation of the internet.

> [!Important]
> OSI & TCP/IP are two sides of the same coin. It's important to understand that the network behavior is concrete, as in - the network behaves, how it behaves. These models are just attempts at describing said behavior in a way that is useful. 

**So what's the purpose of layering?** 

The network is extremely complex, so separating responsibilities and behaviors allows complex communication systems to be built, scaled, changed, and debugged without everything breaking. 

Below is a breakdown of each OSI layer, starting from the creation of application data and working downward toward the physical transmission of said data. Sending data = Layer 7 > Layer 1, Receiving data = Layer 1 > Layer 7

| OSI Model (7 Layers)        | TCP/IP Model (4 Layers)         |
|----------------------------|----------------------------------|
| 7 – Application            | 4 - Application                  |
| 6 – Presentation           | 4 - Application                  |
| 5 – Session                | 4 - Application                  |
| 4 – Transport              | 3 - Transport                    |
| 3 – Network                | 2 - Internet                     |
| 2 – Data Link              | 1 - Network Access               |
| 1 – Physical               | 1 - Network Access               |

> [!Tip]
> OSI Layers are numbered, TCP/IP layers are not. They are numbered in the table for clarity of order. 

### OSI Layers Explained

#### Layer 7 – Application: Creates user/application data and defines what service is being used.

> This is where user-facing software creates and consumes data. Browsers, email clients, APIs, and services live here and define what is being communicated (request, response, message, file). The application decides what it wants to say and to whom, but not how it gets delivered.

#### Layer 6 – Presentation: Formats, encodes, encrypts, or compresses the data so it can be understood.

> This layer ensures the data is in a usable format for both sides. It handles things like encoding, encryption, and compression so that data can be correctly interpreted and protected. The goal here is representation and privacy, not delivery.

#### Layer 5 – Session: Manages the logical conversation lifecycle between two endpoints.

> The session layer manages the logical conversation between two endpoints. It keeps track of whether a communication session is established, active, or closed, and helps resume or maintain conversations. In modern systems, this logic is often absorbed into the application or transport layer

#### Layer 4 – Transport: Adds ports and (optionally) reliability so app-to-app delivery can work.

> The transport layer enables end-to-end communication between applications. It adds port numbers and, depending on the protocol, manages reliability, ordering, and flow control. This is where the system decides how carefully data should be delivered, not where it goes or how it physically moves.

#### Layer 3 – Network: Adds IP addressing and selects a path to the destination network.

> This layer handles logical addressing and routing across networks. By adding IP addresses, it allows data to be sent beyond the local network and routed through multiple intermediate networks. Layer 3 decides where the data needs to go, not how it gets there or whether it arrives intact.

#### Layer 2 – Data Link: Handles local delivery using MAC addresses and error detection.

> The data link layer is responsible for local network delivery. It frames data, adds hardware (MAC) addresses, and performs basic error detection so devices on the same network can communicate. It only cares about the next hop, not the final destination.

#### Layer 1 – Physical: Moves bits as electrical, optical, or radio signals.

> This layer physically transmits bits across a medium using electrical signals (copper), light pulses (fiber), or radio waves (Wi-Fi). It defines signaling, timing, and physical characteristics of the connection. This is the only layer where actual movement happens.

### Encapsulation

Encapsulation (sending):
As data moves down the stack, each layer adds its own header (and sometimes trailer) around the same underlying bits.
- App creates data (L7)
- Transport adds TCP/UDP header (L4)
- Network adds IP header (L3)
- Data Link adds MAC header + FCS (L2)
- Physical encodes bits as signals (L1)

Decapsulation (receiving):
As data moves up the stack, each layer removes and processes its own header.
- Physical receives signals (L1)
- Data Link validates frame, strips MAC/FCS (L2)
- Network strips IP header (L3)
- Transport strips TCP/UDP header (L4)
- App receives original data (L7)

The raw application data never changes, it's simply nested within new sets of headers/trailers at each stage, and upon receiving they are sequentially removed. 

### Protocol Data Units (PDUs)

PDUs are the names we give to the same application data, upon the addition of their new headers & trailers. 

<a id="pdu">
  
| OSI Layer  | PDU Name | What it means                                             |
| ---------- | -------- | --------------------------------------------------------- |
| Layers 7–5 | Data     | Application payload (no transport or network headers yet) |
| Layer 4    | Segment  | Data + TCP or UDP header                                  |
| Layer 3    | Packet   | Segment + IP header                                       |
| Layer 2    | Frame    | Packet + MAC header + FCS                                 |
| Layer 1    | Bits     | Frame encoded as electrical, optical, or radio signals    |

> [!Tip]
> Key Point: The data **never** changes its actual identity, the headers/trailers simply add/remove context to ensure it arrives correctly. 


[Back to Top](#title)

<a id="flow">
  
## Network Media & Data Flow

When data leaves a local network, it flows from LAN → WAN uplink → ISP backbone → destination network, then back down through that network’s WAN uplink → LAN or server.

### Local Data Movement (LAN)

"Local" means devices on the same local network. There are two primary methods of local data transmission: Wi-Fi & Ethernet. It's important to understand that these are more like protocols or methods, and to not confuse them with devices or physical hardware. In simplicity, both of these methods aim to recognize devices by their unique MAC address, and forward the correct frames to said devices. Each method has it's own pros & cons. 

| Feature | Ethernet | Wi-Fi |
|------|---------|------|
| Network role | Local (LAN) | Local (LAN) |
| OSI layer | Layer 2 (uses Layer 1) | Layer 2 (uses Layer 1) |
| Transmission medium | Copper or fiber | Radio waves |
| Wired / wireless | Wired | Wireless |
| Data unit | Frames | Frames |
| Device identification | MAC addresses | MAC addresses |
| Medium type | Dedicated per link | Shared medium |
| Duplex | Full-duplex | Half-duplex–like behavior |
| Reliability | Very high | Lower (interference, contention) |
| Latency | Low and consistent | Higher and variable |
| Speed consistency | Stable | Variable |
| Interference | Minimal | Susceptible to interference |
| Typical use | Desktops, servers, switches | Laptops, phones, IoT |
| Primary advantage | Stability and performance | Convenience and mobility |

In summary, Wi-Fi is wireless, making it more convinient but slower. Ethernet is fast, but requires direct connection, typically leading to cable management and distance-to-gateway issues. Both methods achieve the same result, just in different ways. 

### WAN Uplinks

The WAN uplink is how data leaves your local network, using fiber, copper, cellular, or satellite to reach the ISP backbone (the data interstate). Data is sent according to what kind of gateway you have - fiber, copper, cellular, or satelite. All of these mediums have the same goal: get data from LAN to WAN, the WAN Uplink being the access point. 

| WAN Medium | Physical medium | Range | Typical environment | Notes |
|-----------|----------------|-------|----------------------|-------|
| Fiber | Light (fiber optic cable) | Long | Apartments, offices, backbone access | Fastest, lowest latency |
| Cable (Coax) | Electrical RF over copper | Long | Residential homes | Shared bandwidth |
| DSL | Electrical signals over phone lines | Long | Older residential areas | Slower, legacy |
| Cellular (LTE/5G) | Long-range radio | Long | Mobile & fixed wireless homes | Uses cell towers |
| Satellite (Starlink) | Microwave radio | Global | Rural / remote locations | Replaces physical last mile |


### Internet Backbone (The Interstate)

The internet backbone is a collection of high-capacity networks (owned by ISPs) that carry traffic between regions, countries, and major networks (typically fiber optic cables). It's primary function is to move massive amounts of data. Servers are sometimes directly connected to backbone networks in order to remove the uplink step. 

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

> [!Important] On every `/24` network, there are indeed 256 possible combinations but `.0` is reserved for your network ID (the root IP address, to which all others are built off), and `.255` is for broadcasting to all devices. This technically means there are only 254 available IP addresses for hosts to connect to on your network. (Don't forget, your router needs one too, so in practice there are really only 253 available combos)

[Back to Top](#title)

<a id="switch">

## Layer 2 – Switching & ARP



[Back to Top](#title)

<a id="icmp">
  
## ICMP (Ping, Traceroute, Diagnostics)

[Back to Top](#title)
<a id="tcp">
  
## TCP Fundamentals

[Back to Top](#title)
<a id="udp">
  
## UDP Fundamentals

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
