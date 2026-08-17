# Network Vulnerability Scanner and Report Generator

## Overview
This project is a Bash-based tool that scans a target IP address or hostname for open ports and running services, identifies potential vulnerabilities using Nmap's scripting engine, and generates a clean, readable security report. The script is built around modular functions that each handle one part of the report — header, open ports, vulnerabilities, and recommendations — making it easy to extend as the project grows.

## Purpose
This project was built for my bash scripting class, with a focus on network and device security, shell scripting fundamentals, and best programming practices such as modular functions, input validation, and clear output formatting.

## Current Status
Version 2.5

The script takes a target as a command-line argument and runs two scans against it:

nmap -sV to detect open ports and running services, filtered down with grep for the report's port summary.
nmap -sV --script vuln (Nmap's NSE vulnerability script category) to actively check the target for known vulnerabilities.

The vulnerability section is generated dynamically using two strategies:

Keyword matching — the NSE scan output is searched for the string VULNERABLE, which most vuln category scripts emit when they find a confirmed issue.
Version matching — scan output is processed line-by-line and checked against a case statement of known-vulnerable service/version strings (e.g. vsftpd 2.3.4, Apache 2.4.49, OpenSSH 7.2, ProFTPD 1.3.3), flagging matches with a short description of the associated risk.

## Future Goals
Make the recommendations section dynamic, tailoring advice to the specific vulnerabilities found rather than showing a fixed list.
Expand the case statement version checks to cover more services as they're researched.
Improve report formatting (e.g. HTML or PDF output options).
Add progress/verbosity options so long-running NSE scans are easier to monitor in real time.

## Usage
```bash
chmod +x netscan.sh
./netscan.sh <target_ip_or_hostname>
```

Example:
```bash
./netscan.sh scanme.nmap.org
```

The report is written to report.txt in the current directory.

Note: The --script vuln scan is significantly slower than a basic port scan and can take several minutes depending on the target and number of open ports. Only scan hosts you own or have explicit permission to scan (e.g. scanme.nmap.org, 127.0.0.1, or a device on your own network).

The report is written to `report.txt` in the current directory.

**Note:** Only scan hosts you own or have explicit permission to scan
(e.g. `scanme.nmap.org`, `127.0.0.1`, or a device on your own network).
