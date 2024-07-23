# Fuzzy

### ffuf

```
ffuf -v -c -u http://$ip/FUZZ -w wordlist -t 10000 -mc all -of csv
```

### vhost enumeration

```
ffuf -c -u http://$ip -H "Host: FUZZ" -w wordlist.txt \
-H "X-Bug-Bounty: darccau" -ac -mc all -fc 400,404 -o vhost_$domain.csv \
-of csv -maxtime 120
```

## Fuzz based on request

```
ffuf -request req.txt -request-proto http -w wordlist
```
