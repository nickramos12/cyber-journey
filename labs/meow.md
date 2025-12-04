# Meow
**Difficulty:** Very Easy

[View My Completion Certificate](https://labs.hackthebox.com/achievement/machine/2884432/394)

## Purpose of Lab
The general purpose of this lab is to become comfortable using the terminal, basic commands, and connecting to target machines securely. This is a guided lab (as are most of the starting point labs).  

## Concepts Covered

### Virtual Private Networks (VPNs)
HTB offers two secure options to connect to their target machine: their pwnbx service (in-browser secure connection), or manually configuring OpenVPN. I elected to use OpenVPN for more exposure to the process.

Downloaded and Installed OpenVPN. After some intial issues with Windows Firewall (blocking literally everything I tried), I decided to take the extra step and download Kali Linux (paired with Oracle VirtualBox). 

**Key Points**
- Establishing VPN Connection with `sudo openvpn --config <file.ovpn>`
- Running OpenVPN with/without `--daemon` (i prefer this, cleaner terminal)
- Checking to ensure OpenVPN is running with `ps aux | grep openvpn`
- Verifying the server connection with `ip a s tun0` (or `ip addr show tun0`)

### Basic Recon & Port Access
After I was a bit more familiar with my VPN (and the terminal), I was able to finally focus on the lab itself. This lab was guided, so there's not much merit in completing it, however it did crack the seal on a few basic recon tools.

After running recon (nmap), I found that port 23 was open (Telnet) and contained the flag. 

**Key Points**
- ICMP requests with `ping <ip-address>` were useful for verifying connection to the target (and the internet in general)
- Augmenting the command with flags such as `ping -c 3 <ip-address>` helped me set predefined number of pings, avoiding Cntrl + C to manually control the "noise"
- nmap is extremely useful, along with a few flags such as -Pn (skip ping check), -sV (service/version detection), -sC (running default NSE scripts), and -O (OS detection)
- Telnet is quite insecure, I was able to connect with telnet <ip> and I simply needed the username 'root' no password requirement



## My Takeaways
1. Make a habit out of using `sudo` for elevated privileges (when needed)
2. Using `--daemon` coupled with `ip a s tun0` is a simple, organized way to establish and verify VPN connection
3. nmap is much more useful than most would like to admit, memorizing core flags is essential
4. using `ping -c <#> <ip>` instead of `ping <ip>` helps reduce detection
