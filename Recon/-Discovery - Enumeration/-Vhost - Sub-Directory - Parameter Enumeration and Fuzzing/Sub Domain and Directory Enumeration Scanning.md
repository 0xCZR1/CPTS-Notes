Related articles:
Tags: #Tactics

---

# 📕Definition:
Against web applications we will use sub-domains, vhost or directory brute-forcing to discover more entry points. 
Directory brute-forcing applies for each domain and sub-domain.

We can do this through various tools like [[Gobuster]] or [[ffuf]]...

## Gobuster:

Useful commands: 
```bash
gobuster dir -u http://94.237.55.209:47594/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt --exclude-length 400-600 -t 50
```
```bash
gobuster vhost -u http://94.237.55.209:47594/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain --exclude-length 260-303 -t 50
```
```bash
gobuster dir -u http://10.129.56.46/nibbleblog/ --wordlist /usr/share/seclists/Discovery/Web-Content/common.txt
```

## ffuf:

Useful commands:
```bash
    ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u https://2million.htb/FUZZ -mc all -fs 42 -c -v
```

```bash
    ffuf -w hosts.txt -u https://example.org/ -H "Host: FUZZ" -mc 200
```

```bash
    ffuf -w entries.txt -u https://example.org/ -X POST -H "Content-Type: application/json" \
      -d '{"name": "FUZZ", "anotherkey": "anothervalue"}' -fr "error"
```

```bash
    ffuf -w params.txt:PARAM -w values.txt:VAL -u https://example.org/?PARAM=VAL -mr "VAL" -c
```
