# Network Vulnerability Scanner and Report Generator

## Overview
This project is a Bash-based tool that scans a target IP address or hostname
for open ports and running services, then generates a clean, readable
security report. The script is built around modular functions that each
handle one part of the report — header, open ports, vulnerabilities, and
recommendations — making it easy to extend as the project grows.

## Purpose
This project was built for my bash scripting class, with a focus on network
and device security, shell scripting fundamentals, and best programming
practices such as modular functions, input validation, and clear output
formatting.

## Current Status
Initial setup and basic port scanning functionality implemented. The script
takes a target as a command-line argument, runs an `nmap -sV` scan, filters
the results down to open ports and their detected services using `grep`,
and writes them to a formatted report file. The vulnerabilities and
recommendations sections currently contain placeholder content.

## Future Goals
Will be expanded to include automated vulnerability identification (mapping
detected service versions to known CVEs), more detailed and dynamic
remediation recommendations, and improved report formatting.

## Usage
```bash
chmod +x netscan.sh
./netscan.sh <target_ip_or_hostname>
```

Example:
```bash
./netscan.sh scanme.nmap.org
```

The report is written to `report.txt` in the current directory.

**Note:** Only scan hosts you own or have explicit permission to scan
(e.g. `scanme.nmap.org`, `127.0.0.1`, or a device on your own network).
