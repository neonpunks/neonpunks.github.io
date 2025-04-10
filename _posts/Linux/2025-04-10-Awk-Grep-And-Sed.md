---
title: "Awk, Grep and Sed"
date: 2025-04-10 +0530
categories: [linux]
tags: [grep, sed, awk, bash, text-processing, urls, file, filter, recon, csv, httpx]
order: 1
---

Handy and efficient one-liners using `awk`, `grep`, and `sed` to clean, parse, and extract data from files — extremely useful for recon output, log files, and automation pipelines.

## 🔹 Remove `]` characters from a file

```bash
sed -i 's/]//g' filename
```

- `-i`: Edit files in-place
- `s/]//g`: Substitute all `]` with nothing (global)

Deletes all occurrences of `]` in-place from a file.

## 🔹 Strip `http://` and `https://` from URLs

```bash
sed 's|https\?://||g' input.txt > output.txt
```

- `s|https\?://||g`: Remove both `http://` and `https://` globally
- `>`: Redirects output to a new file

Removes protocol prefixes from URL lists and saves to a new file.

## 🔹 Extract IP addresses from a file

```bash
grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' IPs.txt | tee IP_List.txt
```

- `-E`: Use extended regex
- `-o`: Print only matching part
- `tee`: Write output to file and display

Extracts IPv4 patterns from any file and saves them.

## 🔹 Extract clean domain names

```bash
grep -Eo '\b([a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}\b' input.txt | tee domains.txt
```

- `\b`: Word boundaries
- `+`: Match one or more occurrences
- Captures full domain names without protocol

Grabs domain names without the URL scheme.

## 🔹 Remove empty lines from a file

```bash
sed '/^$/d' input.txt > no_empty_lines.txt
```

- `^$`: Matches empty lines
- `d`: Delete matching lines

Filters out blank lines from tool output or logs.

## 🔹 Replace colons with spaces

```bash
sed 's/:/ /g' raw.txt > formatted.txt
```

- `s/:/ /g`: Replace all `:` with space

Transforms colon-separated fields into space-separated.

## 🔹 Get only the path from URLs

```bash
cut -d '/' -f4- urls.txt
```

- `-d '/'`: Use slash as delimiter
- `-f4-`: Extract from field 4 onward (after protocol and domain)

Removes domain and protocol, leaves just the endpoint/path.

## 🔹 Remove query strings from URLs

```bash
sed 's|\?.*||' urls.txt > stripped.txt
```

- `\?.*`: Match `?` and everything after
- Removes query parameters from URLs

Removes `?param=value` and everything after.

## 🔹 Count unique domains from URL list

```bash
cut -d '/' -f3 urls.txt | sort | uniq -c | sort -nr
```

- `-f3`: Extract domain
- `sort | uniq -c`: Count duplicates
- `sort -nr`: Sort numerically in reverse

Extracts hostnames and ranks them by frequency.

## 🔹 Grep + Awk: Extract URLs with status 200

```bash
grep "200 OK" httpx.txt | awk '{print $1}'
```

- `grep "200 OK"`: Match lines with HTTP 200
- `awk '{print $1}'`: Print first column (usually URL)

Filters out only the URLs with 200 OK responses.

## 🔹 awk: Print 1st and 3rd column

```bash
awk '{print $1, $3}' input.txt
```

- `$1`: First field
- `$3`: Third field

Example: extract IP + response size from structured logs.

## 🔹 awk with custom delimiter (CSV-style)

```bash
awk -F ',' '{print $2}' data.csv
```

- `-F ','`: Set delimiter as comma
- `$2`: Print second field

Extracts the second column from comma-separated values.

## 🔹 awk: Match keyword, print field

```bash
awk '/login/ {print $1}' logs.txt
```

- `/login/`: Match lines containing "login"
- `$1`: Print first column

Find lines with "login" and show the first column (like IP or date).

## 🔹 Clean, deduplicate, and sort

```bash
cat input.txt | sed '/^$/d' | sort | uniq > cleaned.txt
```

- `sed '/^$/d'`: Remove blank lines
- `sort | uniq`: Sort and deduplicate
- `>`: Save to file

Great for preparing tool output for wordlists.

## 🔹 Exclude lines using grep

```bash
grep -v "404" urls.txt
```

- `-v`: Invert match (exclude)

Removes 404 entries from HTTP scan results.

## 🔹 Safe sed replace with backup

```bash
sed -i.bak 's/http:/https:/g' urls.txt
```

- `-i.bak`: Edit in-place, create `.bak` backup
- `s/http:/https:/g`: Replace `http:` with `https:` globally

Replaces all `http:` with `https:` and makes a `.bak` copy first.

## 🔹 Extract JS files from mixed URLs

```bash
grep '\.js' all.txt | sed 's|https\?://||' | sort -u > js.txt
```

- `grep '\.js'`: Find `.js` links
- `sed 's|https\?://||'`: Strip protocols
- `sort -u`: Sort and deduplicate

Pulls `.js` links, removes protocols, deduplicates.

## Best Practices

- Always back up files before using `sed -i`
- Use `sort -u` for quick deduplication
- Pipe into `tee` to both save and view output
- Combine `grep`, `awk`, `sed` for powerful data shaping

## Related Tools

- `cut`: Extract specific fields from lines
- `tr`: Translate or delete characters
- `jq`: Parse JSON output for similar use cases