---
description: I've spend more time configuring the android emulator than exploring it self
---

# RouterSpace

### Recon

At the beginning,  start running rustscan to find some  network services and it's versions, and at the same time i've opened the browser and search for the web application running on port 80, and looking through the wappalyzer i get the frontend technologies used to construct this site.

```
rustscan -b $ip -r 1-65535 --ulimit 5000 -- -A


PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack (protocol 2.0)
| ssh-hostkey:
|   3072 f4:e4:c8:0a:a6:af:66:93:af:69:5a:a9:bc:75:f9:0c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDTJG10LrPb/oV0/FaR2FprNXTVtRobg1Jwy5UOJGrzjWqI8lNDf5DDi3ilSdkJZ0+0Rwr4/gKG5UlyvqCz07XrPfnWG+E7NrgpMpVKR4LF9fbX750gxK+hOSco3qQclv3CUTjTzwMgxf0ltyOg6WJvThYQ3CFDDeOc4T27YqQ/VgwBT92PWu6aZgWX2oAn+X8/fdcejGWeumU9b+rufiNt/pQ1dGUz+wkHeb2pIaA4WfEQLHB1xF33rTZuAXFDiKSb35EpPvhuShsMPQv6Q+NfLAiENgdy+UdybSNH6k1gmPHyroSYoXth7Pelpg+38V9SYtvvoxQRqBbaLApEClTnIM/IvQba9vY8VdfKYDGDcgeuPm8ksnOFPrb5L6axwl0K2ngE4VHQBJM0yxIRo5dELswD1c9O1tR2rq6MbW2giPl6dx/xzEbdVV6VO5n/prjsnpEs8YvNmnELrt6mt0FkcJQ9ageN5ji3pecKxKTVY4J71yf4+cVZKwpX8xI5H6E=
|   256 7f:05:cd:8c:42:7b:a9:4a:b2:e6:35:2c:c4:59:78:02 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDiksdoNGb5HSVU5I64JPbS+qDrMnHaxiFkU+JKFH9VnP69mvgdIM9wTDl/WGjeWV2AJsl7NLQQ4W0goFL/Kz48=
|   256 2f:d7:a8:8b:be:2d:10:b0:c9:b4:29:52:a8:94:24:78 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP2psOHQ+E45S1f8MOulwczO6MLHRMr+DYtiyNM0SJw8
| fingerprint-strings:
|   NULL:
|_    SSH-2.0-RouterSpace Packet Filtering V1
80/tcp open  http    syn-ack
| fingerprint-strings:
|   FourOhFourRequest:
|     HTTP/1.1 200 OK
|     X-Powered-By: RouterSpace
|     X-Cdn: RouterSpace-92838
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 59
|     ETag: W/"3b-9tGbVNQAr+esq0eGsppsNXQA06Q"
|     Date: Tue, 12 Apr 2022 19:45:43 GMT
|     Connection: close
|     Suspicious activity detected !!! {RequestID: v Q s E }
|   GetRequest:
|     HTTP/1.1 200 OK
|     X-Powered-By: RouterSpace
|     X-Cdn: RouterSpace-82855
|     Accept-Ranges: bytes
|     Cache-Control: public, max-age=0
|     Last-Modified: Mon, 22 Nov 2021 11:33:57 GMT
|     ETag: W/"652c-17d476c9285"
|     Content-Type: text/html; charset=UTF-8
|     Content-Length: 25900
|     Date: Tue, 12 Apr 2022 19:45:41 GMT
|     Connection: close
|     <!doctype html>
|     <html class="no-js" lang="zxx">
|     <head>
|     <meta charset="utf-8">
|     <meta http-equiv="x-ua-compatible" content="ie=edge">
|     <title>RouterSpace</title>
|     <meta name="description" content="">
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <link rel="stylesheet" href="css/bootstrap.min.css">
|     <link rel="stylesheet" href="css/owl.carousel.min.css">
|     <link rel="stylesheet" href="css/magnific-popup.css">
|     <link rel="stylesheet" href="css/font-awesome.min.css">
|     <link rel="stylesheet" href="css/themify-icons.css">
|   HTTPOptions:
|     HTTP/1.1 200 OK
|     X-Powered-By: RouterSpace
|     X-Cdn: RouterSpace-6888
|     Allow: GET,HEAD,POST
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 13
|     ETag: W/"d-bMedpZYGrVt1nR4x+qdNZ2GqyRo"
|     Date: Tue, 12 Apr 2022 19:45:42 GMT
|     Connection: close
|     GET,HEAD,POST
|   RTSPRequest, X11Probe:
|     HTTP/1.1 400 Bad Request
|_    Connection: close
|_http-favicon: Unknown favicon MD5: 6A99977D215B048F9410A9C4A1191FAE
|_http-trane-info: Problem with XML parsing of /evox/about
|_http-title: RouterSpace
| http-methods:
|_  Supported Methods: GET HEAD POST OPTION
```

After look at the rustscan report, we notice that has only two network services,  looking at both of them, i don't find anything interesting, except the apk file downloaded through the app.

At this point, start analyzing the file running the command **strings** to retrieve strings from this file, but got nothing else than random characters.

Trying extract  data  about the file and don't retrieve anything useful, I decide to run this the apk file. After spend lot of time trying to make the android emulator works i found this tutorial that solve my problem:&#x20;

{% embed url="https://tutorialforlinux.com/2021/05/31/step-by-step-anbox-kali-linux-2021-installation" %}

Running the anbox android emulator, i've used adb(android debugging bridge) to install the apk.

```
adb install RouterSpace.apk
```

Running the app, we can see an router and a button "check status" and when we click on it, apparently anything happen. To better understand it's behavior, the burp suite proxy will used to intercept it's requests.

To do it, will be also necessary to redirect the app network traffic through burp suite and to do it, use adb:

```
adb shell settings put global http_proxy 10.10.14.11:8081
```

And add port 8081 on burp suite intercept configuration.

Intercepting the request performed at click "check status" button, and we can see an error, because it's directed to "[routerspace.htb](https://0xdedinfosec.vercel.app/posts/hackthebox-routerspace-writeup#routerspacehtb)", knowing it, come to /etc/hosts and add the ip of the application and the found domain.&#x20;

After solve this issue, we can intercept the request and see that it's execute an command on the system and return an ip address. Knowing this behavior, it's possible to substitute the internal string and run substitute it for another string concatenated by semi column like:

```
{
    'ip': '; id'
}
```

Allowed to run commands on the system, a search for a shell has been started, find .ssh directory i decide to push my ssh public key on it, and login-in as normal user.

Searching an possible vulnerability to scale privilege as root, the sudo(1.8) version was found and an exploit to it too.

{% embed url="https://github.com/mohinparamasivam/Sudo-1.8.31-Root-Exploit" %}

Compiling this script on local machine, scp was used to transfer the entire exploit directory to router space machine.&#x20;

Running the script target machines, we got root privilege and all flags are accessible.
