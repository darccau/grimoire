# network scanning

### Open ports&#x20;

```
nmap -F $rhost
```

```
nmap -sT --reason --open -p- --min-rate 5000 -n -Pn -v $rhost
```

```
nmap -sU --reason --open -p- --min-rate 5000 -n -Pn  -v $rhost
```

### Services version

```
nmap -sCV -p ports
```

### Host discovery

```
sudo nmap -PEPM -sn -n $net | grep report | cut -d " " -f 5
```
