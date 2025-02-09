Related articles:
Tags: #Tactics

---
### 📕 Definition:
Enumeration is the most crucial step of every attack. It will dictate the possibilities and scope of the attack. If we have many targets, the chances of success is greater and broader.

HTB says enumeration is art. The difficulty is based on how well we understand certain services and that we do not only rely on automated tools.

Understanding the services is a broad aspect starting from syntax they use, input they expect and so on...

---

## Table of Contents:

#### Network Scanning:
Scan networks, find alive hosts, open ports, enumerate services running on those ports and even OS versions. It can also identify network rules, firewall rules, IDS/IPS.

- [[Nmap - Network Mapper]]
- [[Wireshark]]
- [[tcpdump]]

---
#### Sub Domain and Directory Enumeration Scanning
Against web applications we will use sub-domains, vhost or directory brute-forcing to discover more entry points. 
Directory brute-forcing applies for each domain and sub-domain.

- [[Gobuster]]
- [[ffuf]]

---
##### Protocols:
- [[Attacking Common Services]]
- [[FTP]]
- [[SMB and Samba]]
- [[RPC]]
- [[DNS]]
- [[NFS]]
- [[SMTP]]
- [[POP3 IMAP]]
- [[SNMP v1-2c]]
- [[MSSQL]]
- [[MYSQL]]
- [[RDP]]
- 