# Network-enumeration-with-nmap
Have you ever shown up to a new job, ready to hit the ground running, only to have your enthusiasm drained by the slow yet stunning realization that nobody has any network diagrams?

You might scratch your head and wonder: “How does anything get done around here?”

But don’t worry—wherever there’s a problem, there’s an opportunity to be a problem solver. You didn’t hear it from me, but I’ve heard that problem solvers get raises.

This article is for discovering the scope of a network as a sysadmin in a new environment. As such, it covers simple Nmap flags to help somebody inside the network discover hosts and facts about them. This is not an article about security auditing, penetration testing, or other advanced Nmap use cases. If you’re new to Nmap and you need to get to know your network, then read on.

Warning: Your employer might interpret network scanning as an attack. Please be sure you’re authorized to use Nmap before performing any scans.

There’s no better tool to solve the problem of an unfamiliar and undocumented network than Nmap. More than just a fancy ping sweep, with the right scans, Nmap can fill in your new network diagram with the MAC address, open ports, operating system (OS), and services of the hosts on your network.

Here's how to discover what's on your network.

Host discovery - Is there anyone home?
The first step in learning about a new network is to determine what's attached to your network and which services are exposed. While this might sound like a simple task, consider that a Class A network (10.0.0.0/8) has over 16 million available addresses. Making the job more difficult, most modern firewalls block ICMP echo (ping) requests.

That’s why, by default, Nmap performs host discovery by sending four different probes: An ICMP type 8 (echo request), an ICMP type 13 (timestamp request), a TCP SYN packet to port 443, and a TCP ACK packet to port 80. In addition, Nmap sends an ARP request for any hosts on the local network, filling in MAC address details.

[ You might also like to read: Finding rogue devices in your network using Nmap ]

Performing your first scan with nmap
# nmap -sn <target>
Here, <target> can be hostnames, IP addresses, CIDR notation, or IP ranges.

Note: Hostnames are queried against DNS, while IPs undergo a reverse-lookup. An IP range could look like 192.168.1-10.0-255, which will start at 192.168.1.0 and increment to 192.168.10.255.

To get to know a new network, it makes sense to use the full subnet address, such as 192.168.1.0/24.

This command performs a no port scan, sending the four host discovery probes discussed earlier, recording their result, and then ending. This is a quick and easy way to learn what’s on your network in a more reliable way than a simple broadcast ping or ping sweep.

Tracing routes
To dig deeper into your network environment, you can perform the same scan with the --traceroute option. This scan will attempt to determine what intermediate hops are between your workstation and any scanned remote hosts.

While this is enough to get your new network diagram started, you might decide that you are missing a few details.

Scanning for open ports
With the hosts in our network discovered, it’s time to dig a little deeper.

At this point, you might want to put your new list of active IPs into a file, with entries separated by a space, tab, or newline. Use this file as the target input for a port scan.

# nmap -iL <ip list>
This command scans all named ports plus ports 0-1024. If you want to speed up the process, you can issue the -F flag, which will reduce the scan to the 100 most common ports.

If you want to define a port range manually, then a simple -p#-# instructs Nmap to scan all ports in your range, including the ports defined as the start and endpoints.

Nmap reports each of these ports with one of these three results:

Open - An application is actively receiving packets
Closed - The port is accessible, but there’s no application listening
Filtered - Nmap can’t determine whether it’s open or closed
There are three other states (unfiltered, open|filtered, closed|filtered). However, you’ll generally only see these when running more advanced scans.

Next-level port scanning
Nmap can take port scanning further, providing details on the services listening on open ports.

One of the really impressive aspects of Nmap is its extensive database of 2,200+ services. It uses this database to correlate probe responses with the particular software and version listening on the port. You can use this information not only for developing a new network diagram but for verifying patching throughout your network.

Implementing this functionality is as simple as adding -sV to the open port scan command.

# nmap -iL <ip list> -sV
Identifying the OS
By now, you’ve enumerated hosts on the network, open ports, and the services running on those ports. There’s just one thing missing: The host operating systems.

Like most things in Nmap, finding this information is just one flag away: -O

This is best combined with a service version scan:

# nmap -iL <ip list> -sV -O
[ Free cheat sheet: Get a list of Linux utilities and commands for managing servers and networks. ] 

Network enumeration complete
You now have the tools you need to scan and enumerate your new network.

Of course, there will be a few things to figure out, such as missed or filtered ports, incorrect software version, OS misidentification, or other details, but the birds-eye view of your new network should be fairly complete.

Here's a quick review of what you’ve learned:

Scan targets can take the form of hostnames, IPs, CIDR networks, or IP ranges
-sn will perform a no port scan, a light-touch discovery method consisting of four probes
-iL will import a list of IPs, separated by newlines, tabs, or spaces
A default scan will scan all named ports plus ports 0-1024
Port ranges can be defined with -p#-#, which will scan all ports, inclusive
Ports will generally be reported as open, closed, or filtered
Determine the software and version listening on a port with -sV
OS information is discovered by using -O
If you want to learn more, have a look at the man page (man 1 nmap), or pick up the book Nmap Network Scanning to become a real Nmap expert.
