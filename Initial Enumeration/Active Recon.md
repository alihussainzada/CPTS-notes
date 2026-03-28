### 1. Discover all active hosts on network/IP ranges/Subnets, Document all findings in a note.
- Nmap Host Discover
	- The most effective host discovery method is to use ICMP echo requests
	```BASH
	sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
	
	nmap -sn -PE <target> # for discovering one host with normal ping
	```
- ICMP sweep (ping fping)
	```
	$ fping -asgq 172.16.5.0/23

172.16.5.5
172.16.5.25
172.16.5.50
172.16.5.100
172.16.5.125
172.16.5.240

       9 alive
	```
- TCP/UDP host discovery
- ARP scanning -> `nmap -PR -sn 192.168.1.0/24`
- Add hosts to `/etc/hosts`
### 2. For each Active Host, Scan all TCP/UDP Ports, Document all findings in a note
- #### Nmap TCP Port Scan:
	By default, `Nmap` scans the top 1000 TCP ports with the SYN scan (`-sS`). This SYN scan is set only to default when we run it as root because of the socket permissions required to create raw TCP packets. Otherwise, the TCP scan (`-sT`) is performed by default.

	1. First I do a full port scan:
	```BASH
	nmap -p- --min-rate 10000 10.129.234.87
	```
	2. Then, Performing the scan on discovered ports:
	```BASH
	nmap -vv -p 22,80 -sCV 10.129.234.87
	```
- #### Nmap UDP Port Scan
	1. Always run a udp port scanning in the background:
	```
	nmap -sU -p- --min-rate 10000 -oA nmap/alludp 10.10.10.116
	```
	2. Run `nmap` on the top 20 ports with standard scripts enabled. The scripts are more likely to get a responses from an open port. It works:
	```BASH
	nmap -sU -sC --top-ports 20 -oA nmap/udp-top20-scripts 10.10.10.116
	```
### 3. For each open ports, run service version scan on discovered port with scripts and OS detection
- Nmap service enumeration and OS detection
- Netcat banner grabbing (manual configuration) -> `nc -nv 10.10.1.1 4354`and `telnet 10.10.1.1 4354`
	- Document services and service in a note
### 4. For each open port, do individual Service Enumeration to look for vulnerabilities
###  5. Check for vulnerabilities in discovered services / service versions

- Search Metasploit for service exploits with the discovered version
- Search ExploitDB for service exploits with the discovered version
- Look at Nmap script output for discovered vulnerabilities or misconfigurations (ex: anonymous login)
- Look for OS version exploits
- Search Google for “<Service> <Version> Exploit GitHub”
- Document discovered vulnerabilities in Obsidian
 ### 6. Check file share services (FTP, SMB, etc.) for anonymous logon and credential files
- **FTP Enumeration**
- **SMB Enumeration**
### 7. If webserver(s) exist, see Webserver Enumeration Methodology


