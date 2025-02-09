
Related articles:
Tags: #Maneuvers 

---


If we are able to get the service running on a certain port and it's version than we can start to understand it and obtain access to it.
For more information, see [[Service Enumeration]]

We will be able to list services running on ports via the `-sV` flag.

```bash
0xCZR@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 20:10 CEST
<SNIP>
NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..
Service scan match (Probe NULL matched with NULL line 3104): 10.129.2.28:25 is smtp.  Version: |Postfix smtpd|||
NSOCK INFO [0.4200s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
Nmap scan report for 10.129.2.28
Host is up (0.076s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
```

| **Scanning Options** | **Description**                                        |
| -------------------- | ------------------------------------------------------ |
| `10.129.2.28`        | Scans the specified target.                            |
| `-p-`                | Scans all ports.                                       |
| `-sV`                | Performs service version detection on specified ports. |
| `-Pn`                | Disables ICMP Echo requests.                           |
| `-n`                 | Disables DNS resolution.                               |
| `--disable-arp-ping` | Disables ARP ping.                                     |
| `--packet-trace`     | Shows all packets sent and received.                   |



