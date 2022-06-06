# network scanning

### Nmap&#x20;

```
nmap -F -iL $ips -oN nmap.scan -Pn
```

### Full scan

```
nmap -sC -sV -p- -iL $ips -oN nmap.fullscan -Pn
```

```
ip=x; nmap -sC -sV $ip -oN $ip.nmap& 
```

An alternative tool could be used to perform the scan on the full range of ports.

```
rustscan -a $ip --ulimit 6500 --batch-size 3000 -- -sC -sV -oN $ip.nmap
```

### UDP

```
nmap -F -sU -sV -iL $ips -oA nmap.udpscan -Pn
```

### Get OS version

```
sudo nmap $ip -O 
```

### Host discovery

```
sudo nmap -PEPM -sP -n $ip/24 | grep "report" | awk '{print $5}'
```

```
sudo arp-scan -g $net/mask                                                      
```
