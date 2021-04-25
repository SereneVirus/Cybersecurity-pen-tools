#Nmap
Nmap ("Network Mapper") is a free and open source (license) utility for network discovery and security auditing.

## Scan Types Overview
> TCP
Most common:
- TCP Connect (-sT) - Default if run as regular user
- SYN "Half-open" (-sS) - Default if run as sudo or root

Less common:
- TCP Null (-sN)
- TCP FIN (-sF)
- TCP Xmas (-sX)

TCP Connect does the regular three-way handshake SYN -> SYN-ACK -> ACK
Half-open does SYN -> SYN-ACK -> RST (Reset)

Half-open can sometimes bypass security audits due to certain systems only logging a completed three-way handshake
Half-open saves resources and time since it doesn't have a need to terminate the connection that a regular TCP connection would establish

Drawback of Half-open is that the unsyncronized nature of the scan can bring down badly configured services

> Less common
NULL, FIN and Xmas tries to be even more sneaky than Half-open
NULL does not include any flag and expects the exchange to be (empty) -> RST
FIN sends the FIN flag (used to end an established TCP connection). Since no connection is established the expected exchange is FIN -> RST
Xmas sends a malformed packet with the URG (Urgent), PSH (Push) and FIN flags enabled. Analyzing the packet in Wireshark makes it look like a Christmas tree.
![Christmas tree](https://www.plixer.com/wp-content/uploads/2015/12/xmaswireshark.jpg)

> UDP
- UDP (-sU)

UDP, being stateless, expects no response. Nmap marks a scan on a port with no response as `open` or `filtered`
Filtered means a firewall is dropping the packet instead of replying with an SYN-ACK or RST. A dropped packet for a specific port can indicate something of value there
The firewall could also be set up to drop every packet destined to ports not in use. This would make it less conspicuous

It is also possible to spoof the firewall (in iptables for example) to send a RST instead of dropping the unwanted packet
`iptables -I INPUT -p tcp --dport <port> -j REJECT --reject-with tcp-reset`
This makes it virtually impossible for an attacker to discern what ports are of value

Every port in an UDP scan that does not receive a reply will send a second attempt to make sure the attempt didn't just get lost the first time around
A UDP packet sent to a closed UDP port will usually receive an ICMP (PING) response saying the port is unreachable

UDP is recommended to only run against high priority ports, due to how slow and inaccurate the scan is compared to TCP
`nmap -sU --top-ports 50 <target>` will only UDP scan the 50 most commonly open ports

## ICMP Network Scanning

Nmap is primarily used to scan ports with TCP or UDP, but it is possible to perform Ping sweeps as well with the `-sn` flag
`nmap -sn 192.168.0.1-254` or CIDR `nmap -sn 192.168.0.1/24` can both be used

All packets will be ICMP outside of local network and regular users will use ICMP inside of local network
Sudo or root users will use ARP inside of local network
In addition to the ICMP echo requests, the -sn switch will also cause Nmap to send a TCP SYN packet to port 443 of the target, 
as well as a TCP ACK (or TCP SYN if not run as root) packet to port 80 of the target

## Examples
`nmap -A <target>`
Activates OS detection, version detection, script scanning and traceroute

`nmap -Pn <target>`
Ignores sending ICMP packet to determine if host is alive and will do full scan even if host is down / doesn't exist

`nmap -sv <target>`
Tries to determine version of services running on the target

`nmap -O <target>`
Enable OS detection

`nmap -p <number> <target>`
Only scans the specificed port or range. TCP is default if not specified otherwise. Examples:
`nmap -p 80 1.1.1.1` - Only scans port 80
`nmap -p 1-500 1.1.1.1` - Only scans port 1 through 500
`nmap -p T:80-90,U:100,105,S:110 1.1.1.1` - Scans port 80 through 90 with TCP, port 100 and 105 with UDP and port 110 with Half-open
`nmap -p- 1.1.1.1` - Scan every port (1 - 65535)
`nmap -p http,ssh 1.1.1.1` - Scans for ports commonly associated with these protocols

`nmap -F 1.1.1.1`
Scan the 100 most commonly used ports

`nmap -v <target>`
Increases verbosity (can be extended with more -vv for greater verbosity). -vv is a minimum recommended




