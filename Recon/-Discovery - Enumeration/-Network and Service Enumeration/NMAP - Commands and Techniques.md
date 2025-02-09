Related articles:
Tags: #Tools 

---
## [[Host Discovery]]:
NMAP is capable of performing host discovery by using the flag `-sn`.
It can also be used to scan ranges like 0/24, 0/16, etc...
See [[Host Discovery]] for useful commands

---
## [[Host and Port Scanning]]:
After we know that a certain host is up, we are interested in more information such as: 
- Open ports and its services
- Version of the services
- OS
For more information, see [[Host and Port Scanning]]

---
## [[Service Enumeration]]:
If we are able to get the service running on a certain port and it's version than we can start to understand it and obtain access to it.
For more information, see [[Service Enumeration]]

---
## [[Nmap Scripting Engine]]:
NSE, written in Lua language. Gives us a broader way of interacting with the ports we find, through communicating in certain ways based on the script for the service we run it for.
For more information, see [[Nmap Scripting Engine]]

---
## [[Performance]]:
Scanning performance plays a huge role due to the fact it can impact the production, the performance of certain services and etc... That's why we have to be careful when we launch certain scans.
It can also play a huge role in evasion.
For more information, see [[Performance]]

---

## [[Firewall and IDS IPS Evasion]]:
Nmap gives us many different ways to bypass firewalls rules and IDS/IPS. These methods include the fragmentation of packets, the use of decoys.
For more information see: [[Firewall and IDS IPS Evasion]]


