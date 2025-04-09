---
title: "One Liners"
date: 2025-04-10 +0530
categories: [Cyber-Security]
tags: [anew, nuclei, target, subs, silent, subfinder]
order: 3
---

---
description: Things that are unfinished or don't know where to put them.
---

# Random Stuff

## Nuclei

cat domains.txt | httpx -silent | xargs -n 1 gospider -o output -s ; cat output/\* | egrep -o 'https?://\[^ ]+' | nuclei -t \~/nuclei-templates/ -o result.txt

## Heavy (Needs Dependencies installed first and then needs tweeking)

subfinder -d jora.com -silent | anew target-subs.txt | dnsx -resp -silent | anew target-alive-subs-ip.txt | awk '{print $1}' | anew target-alive-subs.txt | naabu -top-ports 1000 -silent | anew target-openports.txt | cut -d ":" -f1 | naabu -passive -silent | anew target-openports.txt | httpx -silent -title -status-code -mc 200,403,400,500 | anew target-web-alive.txt | awk '{print $1}' | gospider -t 10 -q -o targetcrawl | anew target-crawled.txt | unfurl format %s://dtp | httpx -silent -title -status-code -mc 403,400,500 | anew target-crawled-interesting.txt | awk '{print $1}' | gau --blacklist eot,svg,woff,ttf,png,jpg,gif,otf,bmp,pdf,mp3,mp4,mov --subs | anew target-gau.txt | httpx -silent -title -status-code -mc 200,403,400,500 | anew target-web-alive.txt | awk '{print $1}'| nuclei -eid expired-ssl,tls-version,ssl-issuer,deprecated-tls,revoked-ssl-certificate,self-signed-ssl,kubernetes-fake-certificate,ssl-dns-names,weak-cipher-suites,mismatched-ssl-certificate,untrusted-root-certificate,metasploit-c2,openssl-detect,default-ssltls-test-page,wordpress-really-simple-ssl,wordpress-ssl-insecure-content-fixer,cname-fingerprint,mx-fingerprint,txt-fingerprint,http-missing-security-headers,nameserver-fingerprint,caa-fingerprint,ptr-fingerprint,wildcard-postmessage,symfony-fosjrouting-bundle,exposed-sharepoint-list,CVE-2022-1595,CVE-2017-5487,weak-cipher-suites,unauthenticated-varnish-cache-purge,dwr-index-detect,sitecore-debug-page,python-metrics,kubernetes-metrics,loqate-api-key,kube-state-metrics,postgres-exporter-metrics,CVE-2000-0114,node-exporter-metrics,kube-state-metrics,prometheus-log,express-stack-trace,apache-filename-enum,debug-vars,elasticsearch,springboot-loggers -ss template-spray | notify -silent

## One liner

subfinder -d redacted.com -all | anew subs.txt; shuffledns -d redacted.com -r resolvers.txt -w n0kovo\_subdomains\_huge.txt | anew subs.txt; dnsx -l subs.txt -r resolvers.txt | anew resolved.txt; naabu -l resolved.txt -nmap -rate 5000 | anew ports.txt; httpx -l ports .txt | anew alive.txt; katana -list alive.txt -kf all -jc | anew urls.txt; nuclei -l urls.txt -es info, unknown -ept ssl -ss template-spray | anew nuclei.txt

## Nuclei Tweaks

nuclei -t \~/nuclei-templates/ -o result.txt -rl 20 -mhe 10 -es info -c 10 -no-verify

## One Liner

subfinder -d opensea.com -all | anew subs.txt; shuffledns -d opensea.com -r resolvers.txt -w n0kovo\_subdomains\_huge.txt | anew subs.txt; dnsx -l subs.txt -r resolvers.txt | anew resolved.txt; naabu -l resolved.txt -nmap -rate 5000 | anew ports.txt; httpx -l ports .txt | anew alive.txt; katana -list alive.txt -kf all -jc | anew urls.txt; nuclei -l urls.txt -es info, unknown -ept ssl -ss template-spray | anew nuclei.txt
