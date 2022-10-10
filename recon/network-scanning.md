# network scanning

### Open ports&#x20;

```
nmap -F $rhost
```

```
nmap -sS --open -p- --min-rate -n -Pn -v $rhost
```

```
nmap -sU --open -p- --min-rate -n -Pn  -v $rhost
```

### Services version

```
nmap -sCV -p ports
```

### Host discovery

```
sudo nmap -PEPM -sn -n $rhost/cidr | grep report | cut -d " " -f 5
```
