# Fawn
**Difficulty:** Very Easy

[View my Completion Certificate](https://labs.hackthebox.com/achievement/machine/2884432/393)

## Purpose of Lab
Full disclosure: this was a guided lab. The purpose of this lab was to simulate what an actual lab would feel like(connecting, searching, obtaining the flag). It also familiarized the student with basic commands. 

## Concepts Covered
### Startup Protocol
Having a repeatable startup process matters. This lab followed the same beggining structure as the last - except I had a better idea of what I was doing (and trying to accomplish). I verified the VPN connection, checked that the target machine was reachable with an ICMP, then ran a network scan for basic data: open ports, versions, and operating systems. 

**Key Points**
- Always verify VPN Connection with `ip a s tunO`
- Ensure host is reachable with `ping -c 3 <ipv4>`
- Scan for network data with `nmap -Pn -sV -sC -O <ipv4>`

### Connecting to FTP (tcp/21)
Understanding how to connect and navigate a port is crucial. After a completing a network scan, I found tcp/21 was open and anonymous login was enabled. I logged in and searched the directory for files, finding the flag fairly quick. 

**Key Points**
- Connect to tcp/21 with `ftp <ipv4>
- If anonymous login is enabled, user is `anonymous` and password is blank
- You can view the root directory by using `ls`

## Recon Summary
I did a basic network scan, found tcp/21 (ftp) was open and allowed anonymous login.

**Tools Used**
- `ping`
- `nmap`
- `ftp`

**Important Findings**
- tcp/21 open 
- anonymous login enabled

## Exploitation
I utilized anonymous FTP login and downloaded the flag from the available directory.
