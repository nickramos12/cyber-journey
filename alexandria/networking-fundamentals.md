<a id="title">
  
# Networking Fundamentals

This write up is my personal understanding of the network, and how it functions at it's core. I will use this as a reference point in my future carreer to avoid innaccurate data. As with all write-ups, I will constantly update as I develop deeper understandings of the topic. 

For Quick Navigation:

- [OSI Model + TCP/IP Model](#osi)
- [IP Addressing & Subnetting](#ip)
- [ARP + Layer 2 (Switching)](#arp)
- [ICMP (Ping, Traceroute, Diagnostics)](#icmp)
- [TCP Fundamentals](#tcp)
- [UDP Fundamentals](#udp)
- [DNS Deep Dive](#dns)
- [Dynamic Host Configuration Protocol (DHCP)](#dhcp)
- [Network Translation (NAT + PAT)](#network)
- [Firewalls](#firewalls)
- [VPN Fundamentals](#vpn)
- [Essential Network Tools](#tools)


<a id="osi">
  
## OSI Model + TCP/IP Model


> [!IMPORTANT]
> The OSI & TCP/IP Model are two sides of the same coin. The OSI Model was (and is) the original detailed description of *how* data moves across the network, while the TCP/IP Model is a simplified version of the OSI.


### The OSI Model In Detail

The Open Systems Interconnection (or "OSI") Model is a conceptual framework that allows us to understand how data moves about the network. It helps us break down network communication (via the layers), making communication easier to understand, troubleshoot, and implement new features. 

#### Layer 1: Physical



#### Layer 2: Data Link

Within the data link layer, there are three important concepts to cover: network interfaces, switches, and their associated protocols. 

**Network Interface**: The network interface converts raw bits into data frames. A data frame is essentially a cluster of bits, wrapped in headers & trailers - also known as **metadata**. Headers include (among other data) the source MAC address and the destination MAC address, while the trailer includes the Frame Check Sequence (FCS - corruption detection). Think of this like a local packager - it simply dumps your data into a box and writes "From: *this MAC**, TO: *that MAC*".

**The Switch**: The switch receives that data frame, reads the destination MAC, and forwards to the correct device port. Remember, the MAC could be any device connected - including your router if the data is meant to leave your local network. In essence, the switch is like a dumb sorting machine, just reading MACs and sending them on their way. In the case of a switch not recognizing the MAC address (or a broadcast), it'll flood all ports with the data frame to ensure it gets to its destination. 

**Key Protocols**: 

**Ethernet (IEEE 802.3)** - 

**Wi-Fi (IEEE 802.11)** - 

**ARP (Address Resolution Protocol)** - 

**VLANs (IEEE 802.1Q)** - 

**STP (Spanning Tree Protocol - 802.1D)**

**MAC Learning (Behavior, not protocol)** - 

> [!IMPORTANT]
> **How Layer-2 Avoids Complexity**
> Answer here when ready. Test of true knowledge. 


#### Layer 3: Network

#### Layer 5/6: Transport & Session Layer

#### Presentation Layer

#### Application Layer




<a id="ip">
  
## IP Addressing & Subnetting

<a id="arp">
  
## ARP + Layer 2 (Switching)

<a id="icmp">
  
## ICMP (Ping, Traceroute, Diagnostics)

<a id="tcp">
  
## TCP Fundamentals

<a id="udp">
  
## UDP Fundamentals

<a id="dns">
  
## DNS Deep Dive

<a id="dhcp">
  
## Dynamic Host Configuration Protocol (DHCP)

<a id="network">
  
## Network Translation (NAT + PAT)

<a id="firewalls">
  
## Firewalls

<a id="vpn">
  
## VPN Fundamentals

<a id="tools">
  
## Essential Network Tools

[Back](#title)
