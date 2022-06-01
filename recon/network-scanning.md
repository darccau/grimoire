# network Scanning

## Nmap&#x20;

```
nmap -sC -sV -sU $ip -oN scan.nmap
```

## Rustcan&#x20;

```
rustscan -a $ip -r 1-65535 -- -sV -sC -Pn
```
