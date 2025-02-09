
Related articles:
Tags: #Maneuvers 

---

NMAP is capable of performing host discovery by using the flag `-sn`.
It can also be used to scan ranges like 0/24, 0/16, etc...
```bash
sudo nmap 10.129.2.0/24 -sn -oA tnet
```

If we save the output into a file then we can give it as input with the flag `iL`

```bash
0xCZR@htb[/htb]$ sudo nmap -sn -oA tnet -iL hosts.lst
```

Furthermore we can specify the flag:
- -PE for ICMP Echo Reply type in communication.
- --packet-trace to trace the communication of packets between our host and the target.
- --disable-arp-ping to disable the default arp ping.

```bash
0xCZR@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
