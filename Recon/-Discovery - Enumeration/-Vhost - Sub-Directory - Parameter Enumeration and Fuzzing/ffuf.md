Related articles:
Tags: #Tools

---

# 📖 Definition: 
Ffuf is a super useful tool when assessing the security of a web application.  It could be used by constructing queries manually or importing a web request from Burp (for example). It can fuzz any parameter. In addition to it's features is good to buckle up with a good set of wordlists and other magic stuff :P.

|      Extension fuzzing      | ```ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ```<br>                                          |
| :-------------------------: | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|      **Page Fuzzing**       | **```ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php```**                                 |
|    **Recursive Fuzzing**    | **```ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v```** |
|   **Sub-domain Fuzzing**    | **```ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/```**                                          |
| **Parameter Fuzzing - GET** | **```ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx ```**         |
|      **Value Fuzzing**      | ```ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx ```        |
|                             |                                                                                                                                                                       |


---

Useful commands:
```bash
    ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u https://10.10.10.245:80/FUZZ -mc all -fs 42 -c -v
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
