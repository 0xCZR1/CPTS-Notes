Related articles:
Tags: #OS


---
# <center> <b> <font size="8"> Windows BOOT SEQUENCE </font> </b> </center>

<p align="center">When <b>Windows OS</b> is booting, the first program to run is the <b>UEFI/BIOS</b> which initializes the <b>bootmgr.exe</b>, loads the kernel <b>ntoskernel.exe</b>, loads the <b>Session Manager Subsystem smss.exe</b>. Once all this phase ends, now <b>winit.exe</b> can start and set up the infrastructure for the other processes, such as <b>RPC</b> which set-up the line infrastructure for calls, then <b>DCOM</b> starts (<b>dcomlaunch.exe</b>) and finally <b>Service Control Manager</b> (<b>SCM</b> starts <b>services.exe</b>) which will turn on the services.</p>
 
![[Pasted image 20241202214630.png]] 



[[RPC]]
[[DCOM]]
[[SMC]]

--- 

# Authentication Mechanisms:

![[Pasted image 20241202222155.png]]
[[SAM]]
[[LSASS]]
[[NTLM and NTDS.dit]]
[[Kerberos]]



---

[[Credential Hunting in Windows]]