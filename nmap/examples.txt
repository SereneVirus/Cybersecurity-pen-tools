nmap -A <target>
Activates OS detection, version detection, script scanning and traceroute

nmap -sv <target>
Tries to determine version of services running on the target

nmap -sS <target>
This is Nmap's default TCP SYN Scan

nmap -sU <target>
UDP will be used instead of TCP

nmap -O <target>
Enable OS detection

nmap -p <number> <target>
Only scans the specificed port or range. TCP is default if not specified otherwise. Examples:
nmap -p 80 1.1.1.1 - Only scans port 80
nmap -p 1-500 1.1.1.1 - Only scans port 1 through 500
nmap -p T:80-90,U:100,105,S:110 1.1.1.1 - Scans port 80 through 90 with TCP, port 100 and 105 with UDP and port 110 with SCTP
nmap -p- 1.1.1.1 - Scan every port (1 - 65535)
nmap -p http,ssh 1.1.1.1 - Scans for ports commonly associated with these protocols

nmap -F 1.1.1.1 - Scan the 100 most commonly used ports

nmap -v <target>
Increases verbosity (can be extended with more -vv for greater verbosity). -vv is a minimum recommended




