# README

## Roku Master Remote


This is going to be our first project. I've already confirmed that Roku leaves Port 8060 open (via `nmap`), and I've successfully enabled debug mode on my TV. 

`curl -d '' http://<ip>:8060/keypress/<command>` seems to work for many basic controls. 

Project Goals:
- Sniff the Authentication Packet to find the Authentication Key which prevents remote access *except* for the Roku Remote App, this will allow us to turn off developer mode so nobody else can access the device
- Write a basic python script that makes running commands quicker and more user friendly
- Design a basic app with core remote functions, along with quick links to favorite content (no more find app > click app > login > find tv show/youtube channel)
- 


We'll update this as we go. Cheers to my first project. 
