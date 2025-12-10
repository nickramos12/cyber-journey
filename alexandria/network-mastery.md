<a id="title">
  
# Network Mastery

This write up is my personal understanding of the network, and how it functions at it's core. I will use this as a reference point in my future career to avoid inaccurate data. As with all write-ups, I will constantly update as I develop deeper understandings of the topic. 

Ideally, this document includes everything needed to master the concepts of the Network. This should help me easily pass the Network+ exam. 

### For Quick Navigation:

- [OSI + TCP/IP Models](#osi)
- [Ethernet & Physical Media](#eth)
- [IP Addressing & Subnetting](#ip)
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

<a id="eth">
  
## Ethernet & Physical Media

### Ethernet Fundamentals

**Ethernet** is a set of rules that allows devices on the **same local network** to exchange data reliably. In essence, ethernet is the solution to "How do I send data to another device on my local network?" Ethernet lives across OSI L1 & L2, handling the physical transmission of data via cables, but also assi

> [!Important]
> Locality has nothing to do with distance. Even if you ran a wire to the edge of the universe and back, it would still be local.
Local vs non-local is determined by routing — the boundary where a device stops using MAC addresses, and starts making IP decisions. 

#### Core Ethernet Responsibilities

**Local Device Identification via MAC Address**

Each NIC (network card) has a MAC address and ethernet uses the MAC to distinguish devices on a local network, ensuring data gets to the correct device. 

**Package Data into Frames**

Ethernet wraps higher-layer data (typically an IP packet) into a frame:
- Header: contains source *and* destination MAC address
- Payload: the IP packet (fresh from L4 - original app data wrapped in TCP/UDP headers)
- Trailer: FCS for error checking

**Deliver the Frame to Next Local Device**

Switches/NICs look at the destination MAC address and accept/reject based on their own. 

- Media Access Coordination: defines how devices share transmission medium (collisions, duplexing)
- Error Detection: detects corrupted frames by using Framce Check Sequences (FCS)

Ethernet does **not**:
- route traffic between networks
- make global path decisions
- care about apps or IP logic

### Copper Ethernet Media

### Fiber Optic Media

### Media Selection & Design Decisions

[Back to Top](#title)

<a id="ip">
  
## IP Addressing & Subnetting

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
