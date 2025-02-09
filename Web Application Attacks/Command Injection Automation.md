# Evasion Tools Guide

## Overview
When dealing with advanced security tools, basic manual obfuscation techniques may not suffice. This guide covers automated obfuscation tools for both Linux and Windows environments, providing practical examples for command obfuscation.

---

## Linux Tool: Bashfuscator
### Installation Process
To set up Bashfuscator, execute the following commands:
```bash
git clone https://github.com/Bashfuscator/Bashfuscator
cd Bashfuscator
pip3 install setuptools==65
python3 setup.py install --user
```
### Basic Usage
Navigate to the binary directory and run the tool:
```bash
cd ./bashfuscator/bin/
./bashfuscator -h
```

Simple obfuscation:

```bash
./bashfuscator -c 'cat /etc/passwd'
```

Testing obfuscated commands:
```bash
bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'
```

## Windows Tool: DOSfuscation

### Setup Instructions

```bash
git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git cd Invoke-DOSfuscation Import-Module .\Invoke-DOSfuscation.psd1 Invoke-DOSfuscation`
```
### Tool Navigation

The tool provides three main options:

1. **BINARY** – Obfuscated binary syntax for `cmd.exe` & `powershell.exe`
2. **ENCODING** – Environment variable encoding
3. **PAYLOAD** – Obfuscated payload via DOSfuscation

### Usage Example

```powershell
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt Invoke-DOSfuscation> encoding Invoke-DOSfuscation\Encoding> 1
```