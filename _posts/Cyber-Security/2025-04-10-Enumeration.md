---
title: "Enumeration"
date: 2025-04-10 +0530
categories: [Cyber-Security]
tags: [bash, domain, subdomains, seclists, wfuzz, gobuster, ffuf, knockpy, assetfinder, altdns, dirsearch, httpx, aquatone, curl]
order: 1
image: /assets/images/Cyber-Security/Enumeration.png
---

Subdomain and directory enumeration are key techniques in recon during penetration testing. Below are powerful commands and tools used to identify subdomains, directories, and gather more information from target domains.

## Subdomain Enumeration

### `wfuzz`
```bash
wfuzz -u domain.com -w /usr/share/seclists/SecLists-master/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.domain.com" --hc 404 --hw 65
```
- `-u`: Target URL with `FUZZ` placeholder.
- `-w`: Wordlist of potential subdomains.
- `-H`: Custom header (host fuzzing).
- `--hc 404`: Hide results with HTTP 404.
- `--hw 65`: Filter based on content length.

### `gobuster`
```bash
gobuster dns -d domain.com -w /usr/share/seclists/SecLists-master/Discovery/DNS/n0kovo_subdomains.txt -i --wildcard -t 100
```
- `-d`: Target domain.
- `-w`: Wordlist for brute force.
- `-i`: Show IP address of valid entries.
- `--wildcard`: Avoid false positives due to wildcard DNS.
- `-t`: Number of threads.

### `ffuf` (Fuzz Faster U Fool)
```bash
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://domain.com/FUZZ -t 200
```
- `-w`: Wordlist for fuzzing.
- `-u`: Target URL with FUZZ placeholder.
- `-t`: Threads (speed up the scan).

### `knockpy`
```bash
knockpy domain.com
knockpy -d domain.com --recon --bruteforce
```
- Performs brute-force subdomain enumeration and optionally does recon on found subdomains.

### `assetfinder`
```bash
assetfinder -subs-only domain.com
```
- Passive tool to quickly find subdomains via public sources.

## Directory Brute-Forcing

### `dirsearch`
```bash
dirsearch -u http://domain.com/
dirsearch -u http://domain.com/ -i200,302 -w /usr/share/wordlists/dirb/common.txt
```
- `-i200,302`: Only show results with these HTTP status codes.
- `-w`: Use a custom wordlist.

### `gobuster` (Directory mode)
```bash
gobuster -t 10 dir -e -k -u http://domain.com/ -w /usr/share/dirb/wordlists/common.txt
```
```bash
gobuster -t 10 dir -e -k -u http://domain.com/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
```
- `-t`: Threads.
- `-e`: Show full URLs.
- `-k`: Skip SSL verification.
- `dir`: Use directory brute-force mode.

### `altdns` (DNS permutations)
```bash
altdns -i subdomains.txt -o permutation_output -w words.txt -r -s resolved_output.txt
cat permutation_output | cut -d '.' -f 1-3 | tee -a permutation_output_final
```
- `-i`: Input file of discovered subdomains.
- `-w`: Wordlist for generating permutations.
- `-r`: Resolve permutations.
- `-s`: Save resolved entries to a file.

## Checking URL Status

### `httpx-toolkit`
```bash
cat subdomains.txt | httpx-toolkit | tee -a alivesubdomains.txt
cat subdomains.txt | httpx-toolkit -sc -title
```
- First command saves live domains.
- Second checks status codes and page titles.

## Gather Info from Certificates

```bash
curl https://crt.sh/?q=<IP-address>
curl https://ssltools.com/certificate-search?q=<IP-address>
```
- Retrieve public certificates related to an IP or domain — useful for discovering more subdomains or services.

## Take Webpage Screenshots

### `aquatone`
```bash
cat alivesubdomains.txt | aquatone
```
- Captures screenshots of live subdomains for visual inspection.

## Best Practices

- **Mix Passive and Active Tools**: Use tools like `assetfinder` or `amass` (not listed here but recommended) for passive discovery, and `wfuzz` or `gobuster` for active brute-forcing.
- **Watch for Wildcards**: Always test for wildcard DNS responses — tools like `gobuster` and `amass` have support for this.
- **Thread Wisely**: High thread counts (`-t 100` or `-t 200`) speed up scans but may trigger rate-limiting or firewalls.
- **Chain Your Tools**: Discover with one tool, verify with another (e.g., `assetfinder → httpx → aquatone`).