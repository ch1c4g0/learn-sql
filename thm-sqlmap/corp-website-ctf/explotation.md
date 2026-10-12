

# Target URL

```text
10.64.138.125:3000
```

## Recon

```bash
┌──(root㉿kali)-[~]
└─# nmap -sV -vvv 10.64.138.125
Starting Nmap 7.93 ( https://nmap.org ) at 2026-10-11 19:29 UTC
NSE: Loaded 45 scripts for scanning.
Initiating Ping Scan at 19:29
Scanning 10.64.138.125 [4 ports]
Completed Ping Scan at 19:29, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:29
Completed Parallel DNS resolution of 1 host. at 19:29, 0.00s elapsed
DNS resolution of 1 IPs took 0.00s. Mode: Async [#: 1, OK: 1, NX: 0, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 19:29
Scanning ip-10-64-138-125.ec2.internal (10.64.138.125) [1000 ports]
Discovered open port 22/tcp on 10.64.138.125
Discovered open port 3000/tcp on 10.64.138.125
Completed SYN Stealth Scan at 19:29, 0.13s elapsed (1000 total ports)
Initiating Service scan at 19:29
Scanning 2 services on ip-10-64-138-125.ec2.internal (10.64.138.125)
Completed Service scan at 19:29, 11.15s elapsed (2 services on 1 host)
NSE: Script scanning 10.64.138.125.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 19:29
Completed NSE at 19:29, 0.01s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 19:29
Completed NSE at 19:29, 0.02s elapsed
Nmap scan report for ip-10-64-138-125.ec2.internal (10.64.138.125)
Host is up, received reset ttl 64 (0.0074s latency).
Scanned at 2026-10-11 19:29:14 UTC for 11s
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 64 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
3000/tcp open  ppp?    syn-ack ttl 63
```

```bash
──(root㉿kali)-[~]
└─# dirb http://10.64.138.125:3000 /usr/share/wordlists/dirb/common.txt 

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sun Oct 11 19:30:55 2026
URL_BASE: http://10.64.138.125:3000/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.64.138.125:3000/ ----
+ http://10.64.138.125:3000/cgi-bin/ (CODE:308|SIZE:8)                                  
                                                                                        
-----------------
END_TIME: Sun Oct 11 19:31:09 2026
DOWNLOADED: 4612 - FOUND: 1
```
