# fuzzy

### ffuf

```
ffuf -v -c -u http://$ip/FUZZ -w wordlist -t 10000 -mc all -of csv
```

### feroxbuster

```
feroxbuster -u http://$ip.com -w wordlist -o samp --json -t 100000
```
