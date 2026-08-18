# Network Vulnerability Scanner and Report Generator

## Overview

This project is a Bash-based tool that scans a target IP address or hostname for open ports and running services, identifies potential vulnerabilities using Nmap's scripting engine, cross-references detected services against the National Vulnerability Database (NVD), and generates a clean, readable security report. The script is built around modular functions that each handle one part of the report — header, open ports, vulnerabilities, and recommendations — making it easy to extend as the project grows.

## Purpose

This project was built for my bash scripting class, with a focus on network and device security, shell scripting fundamentals, and best programming practices such as modular functions, input validation, and clear output formatting.

## Current Status

**Version 3**

The script takes a target as a command-line argument and runs two scans against it:

- `nmap -sV` to detect open ports and running services, filtered down with `grep` for the report's port summary.
- `nmap -sV --script vuln` (Nmap's NSE vulnerability script category) to actively check the target for known vulnerabilities.

The vulnerability section is generated dynamically using two strategies:

1. **Keyword matching** — the NSE scan output is searched for the string `VULNERABLE`, which most `vuln` category scripts emit when they find a confirmed issue.
2. **Version matching** — scan output is processed line-by-line and checked against a `case` statement of known-vulnerable service/version strings (e.g. vsftpd 2.3.4, Apache 2.4.49, OpenSSH 7.2, ProFTPD 1.3.3). Each match extracts a clean product name and version number, flags the finding with a short description of the associated risk, and then queries the NVD's public REST API (`query_nvd`) for that specific product/version to pull in real, up-to-date CVE IDs, descriptions, and severity ratings.

Live NVD lookups are capped (currently 2 per scan) to stay within the API's public rate limits during testing and development; this cap can be raised once things are confirmed to work reliably.

Verified working end-to-end against Metasploitable2, which reliably produces both a `VULNERABLE` keyword hit and a version-match/NVD lookup (vsftpd 2.3.4). Scans against fully patched targets (e.g. scanme.nmap.org, a home router) may legitimately return no findings — see the Testing note below.

The recommendations section currently contains static best-practice guidance.

## Future Goals

- Make the recommendations section dynamic, tailoring advice to the specific vulnerabilities found rather than showing a fixed list.
- Expand the `case` statement version checks to cover more services as they're researched.
- Improve report formatting (e.g. HTML or PDF output options).
- Add progress/verbosity options so long-running NSE scans are easier to monitor in real time.
- Make the NVD query rate-limit cap configurable via a command-line flag.

## Dependencies

- [`nmap`](https://nmap.org/) with NSE (Nmap Scripting Engine) support
- `curl`
- [`jq`](https://jqlang.org/) — required for parsing NVD API responses (`sudo apt install jq` / `brew install jq`)

## Usage

```
chmod +x netscan.sh
./netscan.sh <target_ip_or_hostname>
```

Example:

```
./netscan.sh scanme.nmap.org
```

The report is written to `report.txt` in the current directory.

**Note:** The `--script vuln` scan is significantly slower than a basic port scan and can take several minutes depending on the target and number of open ports. Only scan hosts you own or have explicit permission to scan (e.g. `scanme.nmap.org`, `127.0.0.1`, or a device on your own network).

## Testing

Fully patched or hardened targets (public test hosts, routers, up-to-date machines) will often legitimately return zero NSE vulnerability hits — that's expected, not a bug. To reliably exercise and verify the vulnerability-detection and NVD-lookup logic, test against an intentionally vulnerable machine on your own network, such as [Metasploitable2](https://sourceforge.net/projects/metasploitable/), a well-known vulnerable VM built for exactly this kind of security-tooling practice.
