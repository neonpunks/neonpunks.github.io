---
title: "Awk Grep And Sed"
date: 2025-04-10 +0530
categories: [linux]
tags: [grep, sed, bash, remove, urls, file]
order: 1
---

---
description: Everything related to text manipulation
---

# awk, grep and sed

### Remove "]" from urls

```bash
sed -i 's/]//g' filename
```

### Remove http and https from urls

```bash
sed 's|https?://||g' input.txt > output.txt
```

### Grep IP from a file

```bash
grep -E -o '([0-9]{1,3}.){3}[0-9]{1,3}' IPs | tee IP_List
```

### Get only domains from a file

```bash
grep -E -o '\b(?:[a-zA-Z0-9-]+.)+[a-zA-Z]{2,}\b' | tee domains
```









