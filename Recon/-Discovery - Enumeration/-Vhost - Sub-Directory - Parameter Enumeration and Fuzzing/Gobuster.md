Related articles:
Tags: #Tools

---


Useful commands: 
```bash
gobuster dir -u http://10.129.236.24:80/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt --exclude-length 400-600 -t 50
```
```bash
gobuster vhost -u http:/94.237.58.106:39481/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain --exclude-length 260-303 -t 50
```
```bash
gobuster dir -u http://10.10.11.217/ --wordlist /usr/share/seclists/Discovery/Web-Content/common.txt
```
```bash
gobuster dir -u http://10.10.10.245/ -w /usr/share/seclists/Discovery/Web-Content/common.txt --exclude-length 400-600 -t 50
```
