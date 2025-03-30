---
id: network-scanning
aliases: []
tags: []
---

# network scanning

### Open ports

```bash
nmap -F $rhost
```

```bash
ports=$(nmap -p- --min-rate=1000 -T4 $rhost | grep '^[0-9]' | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)
```

### Services version

```bash
nmap -sC -sV --reason --open -p $ports --min-rate=1000 $rhost
```

### Host discovery

```bash
sudo nmap -PEPM -sn -n $net | grep report | cut -d " " -f 5
```
