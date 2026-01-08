# homelab
My goal/plan was to set up 4 mini pcs in a proxmox cluster as I had been told this was common in small to medium enterprises with limited budgets.
So I got the 4 mini pcs running with proxmox. Installing Proxmox was never an issue.
Once they were clustered together my plan was then to run OPNSense for a firewall, Security Onion to monitor traffic (the two “better” mini pcs have 2 NICs each), Wazuh as a SIEM and ideally Windows Server to set up Active Directory.
Attempt 1…
I had OPNSense running fine and two VLANs running, the idea to separate the traffic I wanted to monitor. My aim is to become proficient in spotting non standard traffic shall we say. So VLAN10 for management of proxmox, opnsense etc… And VLAN20 for my smart TV, mobile phones, laptops etc… I ended up adding VLAN30 solely for my PS5 just for the speed. I had my Netgear GS308E set up to mirror port 1 (the trunk in from OPNSense) into port 3 which fed into Security Onion. All was well until I ran the cluster creation commands. Then I lost network access to everything. The internet still worked for my TV so I was initially puzzled. I had local access to the mini pcs but couldn’t find them on the network or access any GUIs. Turns out Proxmox is extremely particular about how up to date each node is. I believe what happened is the time (corosync?) manager had a hissy fit and knocked OPNSense off the top of the DHCP tree momentarily and in a very helpful turn of events my TP-Link Archer C6 (which was 100% in AP mode) took over. I know this as the gateway changed to the C6’s IP address and I can’t find a better explanation. I could be wrong.
So I decided to re-install everything from scratch which leads to attempt 2…
I did some more research and decided this time around to keep OPNSense separate and barebones. The mini pc I had in mind had a decent enough CPU and sufficient ram.

This is it running now so I believe this will be ok long term. 
I did still want a proxmox cluster but in researching Security Onion I decided to try barebones there too simply because of the RAM requirement. I had read a few websites that claimed 16gb of RAM would be OK but come the day of the race I had 16gb in the mini pc and Security Onion said no. So I borrowed RAM from another mini PC to get it up to 32gb and well let’s say Sec Onion was stubborn. The first 4 times I tried to install Sec Onion it got stuck near the beginning it turns out. I reached out on the official forum as there didn’t appear to be anyone else with the same issue. One of the devs did kindly reply but by this point I had Sec Onion up and running. I found the previous most recent release (iso) and installed it first time from that.
The problem came when trying to set up the VLANs across OPNSense and the Netgear. I got in a muddle I will admit and lost internet activity and access to everything. Even access the switch required me to use my spare Windows 10 laptop to give it a static ip address. So I decided to hit the reset button. Resetting the Netgear worked a treat.
So this leads to final attempt 3!
So at this point I’d kind of decided to attempt a Proxmox cluster another time. I didn’t feel I needed it to achieve my goal of being much better positioned to monitor my home’s network traffic and potentially find unwanted traffic in there somewhere.
Mini PC 1 (8gb of RAM) is now running OPNSense barebones. Trunk in from my ISP and trunk out into the Netgear switch. Mini PC 2 (32gb RAM) is running Sec Onion barebones. Management access through NIC0 and port mirroring running into NIC1. Mini PC 3 is actually running Proxmox as this it turned out to be the easiest way to install Wazuh. I originally downloaded the official pre-built VM. Which wasn’t as straightforward as I imagined it would be. Turns out Proxmox doesn’t like files other than ISO files. That I can tell at least, I’m sure someone more familiar with Proxmox can tell me otherwise. So the instructions I found meant I had to rename the file to upload it then faff around with it to extract the hard drive image. It was getting frustrating having to jump through so many hoops. After 4 days of trying I got to the point where I decided to ask the internet what is the quickest or easiest way to install Wazuh as a Proxmox VM. Turns out all I needed was a single line of commands from the extremely helpful devs at Proxmox VE Helper-Scripts (see their awesome website here). This is literally all it took:
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/wazuh.sh)"
And hey presto. Job done.
Here’s the Proxmox dashboard which shows there’s quite a lot of headroom resource wise to run a Windows 10 target/victim VM that can be left running all the time rather than leaving my laptop on:













